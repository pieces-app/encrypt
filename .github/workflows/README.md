# CI/CD Workflows Documentation

This directory contains GitHub Actions workflows that automate testing, code quality checks, and release management for the `encrypt` package.

## Overview

The CI/CD system consists of three main workflows:

1. **Dart CI** (`dart.yml`) - Continuous integration for code quality and testing
2. **Release Please** (`release-please.yml`) - Automated release management
3. **Enhance Release PR** (`enhance-release-pr.yml`) - AI-powered release PR enhancement

---

## Workflows

### 1. Dart CI (`dart.yml`)

**Purpose:** Ensures code quality and runs tests on all changes.

**Triggers:**
- Push to `5.x` branch
- Pull requests targeting `5.x`
- Manual dispatch (`workflow_dispatch`)

**Jobs:**

#### 🧹 Analyze
- Runs on: `ubuntu-latest`
- Checks:
  - Code formatting (120 character line length)
  - Static analysis (`dart analyze`)
- **Note:** Analysis warnings don't fail the build (legacy codebase compatibility)

#### 🧪 Test
- Runs on: `ubuntu-latest` and `macos-latest`
- SDK: Stable Dart SDK
- Features:
  - Retry logic (3 attempts, 10s wait between retries)
  - 15-minute timeout per test run
  - Parallel test execution (4 concurrent tests)

**Concurrency:** Cancels in-progress runs when new commits are pushed.

---

### 2. Release Please (`release-please.yml`)

**Purpose:** Automates version management and release creation using [Release Please](https://github.com/googleapis/release-please).

**Triggers:**
- Push to `5.x` branch

**How It Works:**

Release Please analyzes conventional commits and automatically:
- Creates/updates release PRs when changes are detected
- Bumps version numbers based on commit types:
  - `feat:` → Minor version bump (e.g., `5.1.0` → `5.2.0`)
  - `fix:` → Patch version bump (e.g., `5.1.0` → `5.1.1`)
  - `feat!` / `BREAKING CHANGE:` → Major version bump (e.g., `5.1.0` → `6.0.0`)
- Updates `CHANGELOG.md` with categorized changes
- Updates `pubspec.yaml` version
- Updates `.release-please-manifest.json`

**Jobs:**

#### 📦 Release Please
- Creates or updates release PRs
- Outputs release information (version, tag name, PR number, etc.)

#### 🔧 Handle Untagged Releases (Fallback)
- **When:** Runs if Release Please didn't create a release directly
- **Purpose:** Handles cases where release PRs were merged but tags/releases weren't created
- **Actions:**
  - Finds merged release PRs with `autorelease: pending` label
  - Creates missing git tags
  - Creates missing GitHub Releases
  - Updates PR labels to `autorelease: tagged`

#### ✅ Verify Release
- **When:** Runs after a release is created (by Release Please or fallback job)
- **Purpose:** Validates the released package
- **Checks:**
  - Version matches between `pubspec.yaml` and release
  - Code analysis passes (`dart analyze --fatal-infos`)
  - All tests pass (`dart test`)
- **Output:** Creates a release summary with installation instructions

**Configuration:**
- Config file: `release-please-config.json`
- Manifest: `.release-please-manifest.json`
- Target branch: `5.x`

**Concurrency:** Only one Release Please run per branch at a time (doesn't cancel in-progress runs).

---

### 3. Enhance Release PR (`enhance-release-pr.yml`)

**Purpose:** Uses Claude AI to enhance release PRs with release highlights, version review, and documentation updates.

**Triggers:**
- Pull request opened, synchronized, or reopened
- Only runs for PRs with branch names starting with `release-please--`

**How It Works:**

When a Release Please PR is created, this workflow:

1. **Extracts version** from PR title (e.g., "chore: release 5.1.4")
2. **Checks if already enhanced** by looking for "### 🚀 Release Highlights" in CHANGELOG.md
3. **If not enhanced**, Claude AI:
   - Reviews version appropriateness based on commit history
   - Adds "### 🚀 Release Highlights" section to CHANGELOG.md
   - Syncs documentation (README.md, CHANGELOG.md)
   - Closes related issues (if commits reference them)
   - Posts a summary comment on the PR

**Claude AI Tasks:**

1. **Version Review** - Validates version bump is appropriate
2. **Release Highlights** - Adds developer-focused highlights (2-4 sentences)
3. **Documentation Sync** - Updates README and CHANGELOG if needed
4. **Issue Management** - Closes issues fixed by commits
5. **PR Summary** - Posts comprehensive release summary

**Configuration:**
- Uses `ANTHROPIC_API_KEY_GLOBAL_CLOUD_RUNTIME` secret
- Skips enhancement if already done (idempotent)

**Concurrency:** Cancels in-progress runs when PR is updated.

---

## Release Process Flow

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Developer commits with conventional commit message       │
│    Example: "fix: resolve dart analyze lint errors"          │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Push to 5.x branch                                       │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│ 3. Release Please workflow runs                                  │
│    - Analyzes commits                                           │
│    - Creates/updates release PR (e.g., "chore: release 5.1.4")  │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Enhance Release PR workflow triggers                      |
│    - Claude AI enhances the PR                              |
│    - Adds release highlights                                |
│    - Reviews version                                        |
│    - Updates documentation                                  |
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. PR is reviewed and merged                                │
└────────────────────┬────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────┐
│ 6. Release Please creates:                                  │
│    - Git tag (e.g., v5.1.4)                                 │
│    - a GitHub Release                                       │
│    - Verify Release workflow validates the release           │
└─────────────────────────────────────────────────────────────┘
```

---

## Key Features

### Conventional Commits
All commits should follow [Conventional Commits](https://www.conventionalcommits.org/) format:
- `feat:` - New features (minor version bump)
- `fix:` - Bug fixes (patch version bump)
- `feat!:` or `BREAKING CHANGE:` - Breaking changes (major version bump)
- `docs:`, `chore:`, `refactor:`, `perf:`, `test:`, `ci:` – Do not trigger a version bump by themselves, but may still appear in releases and changelogs

### Version Management
- Versions follow [Semantic Versioning](https://semver.org/)
- Managed automatically by Release Please
- Tracked in `.release-please-manifest.json`

### Release Highlights
- Automatically added by Claude AI
- Focus on developer impact, not implementation details
- Appears in CHANGELOG.md under "🚀 Release Highlights"

### Fallback Mechanism
- If Release Please doesn't create a release automatically, the fallback job handles it
- Ensures releases are never missed

### Release Verification
- Every release is verified before completion
- Checks version consistency, code analysis, and tests
- Provides installation instructions in release summary

---

## Configuration Files

### `release-please-config.json`
- Defines release type (`dart`)
- Configures changelog sections and formatting
- Sets PR title patterns

### `.release-please-manifest.json`
- Tracks current released version
- Used by Release Please to determine next version

---

## Permissions

All workflows require:
- `contents: write` - To create tags and releases
- `pull-requests: write` - To create/update PRs and comments
- `issues: write` - To close issues
- `actions: write` - To read workflow outputs

---

## Troubleshooting

### Release PR not created
- Check that commits follow conventional commit format
- Verify Release Please workflow ran successfully
- Check `.release-please-manifest.json` is up to date

### Enhancement not running
- Verify PR branch name starts with `release-please--`
- Check workflow has necessary permissions
- Ensure `ANTHROPIC_API_KEY_GLOBAL_CLOUD_RUNTIME` secret is set

### Release not created after merge
- Check fallback job logs
- Verify PR has `autorelease: pending` label
- Check if tag/release already exists

### Tests failing
- Tests retry automatically (3 attempts)
- Check for flaky tests or environment issues
- Review test logs for specific failures

---

## Manual Operations

### Trigger Release Please manually
```bash
# Push an empty commit to trigger Release Please
git commit --allow-empty -m "chore: trigger release"
git push origin 5.x
```

### Manually create release
If automatic release fails:
1. Check fallback job output
2. Manually create tag: `git tag v5.1.4`
3. Push tag: `git push origin v5.1.4`
4. Create GitHub Release via UI or CLI

### Skip enhancement
If enhancement needs to be skipped:
- Enhancement is idempotent - it checks if already done
- Can be manually triggered by reopening PR
- Can be disabled by removing workflow file

---

## Related Documentation

- [Release Please Documentation](https://github.com/googleapis/release-please)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Semantic Versioning](https://semver.org/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

## Workflow Status

All workflows are active and running on the `5.x` branch. The system is fully automated and requires minimal manual intervention.

