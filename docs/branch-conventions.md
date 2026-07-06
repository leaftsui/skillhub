# Branch Conventions & Commit Rules

## Branch Model

SkillHub follows a lightweight Git Flow model:

```
main     ─────●──────────────●──────────●──→  (production-ready)
develop  ──────●────●──●──────●──●───────●──→  (integration branch)
                \  /    \    /    \    /
feature/*        ──    ──    ──    ──       (feature branches)
```

### Branches

| Branch | Purpose | Base | Lifecycle |
|--------|---------|------|-----------|
| `main` | Production-ready code | — | Permanent; merge only from `develop` via PR |
| `develop` | Integration branch for features | `main` | Permanent; created at project start |
| `feat/<name>` | Feature development | `develop` | Short-lived; delete after merge |
| `fix/<name>` | Bug fixes | `develop` | Short-lived; delete after merge |
| `chore/<name>` | Maintenance tasks | `develop` | Short-lived; delete after merge |
| `docs/<name>` | Documentation-only changes | `develop` | Short-lived; delete after merge |

### Rules

1. **`main` is sacred.** Never commit directly to `main`. All changes enter `main` through PRs from `develop`.
2. **`develop` is the integration hub.** Feature branches branch off `develop` and merge back into `develop`.
3. **Feature branches are short-lived.** Keep them alive for days, not weeks. Large features should be split into smaller, mergeable increments.
4. **Rebase before merging.** Before opening a PR, rebase your feature branch onto the latest `develop` to keep history linear and avoid merge bubbles.

---

## Branch Naming

Use lowercase with hyphens. All branches except `main` and `develop` use a type prefix:

```
<type>/<short-description>
```

**Types:**

| Type | When to Use |
|------|-------------|
| `feat/` | New feature |
| `fix/` | Bug fix |
| `chore/` | Build, CI, dependency, or maintenance tasks |
| `docs/` | Documentation-only changes |
| `refactor/` | Code restructuring with no behavior change |
| `test/` | Adding or updating tests |
| `ops/` | Operations, deployment, or infrastructure |

**Examples:**

```
feat/skill-search
fix/null-pointer-in-publish
chore/upgrade-spring-boot
docs/api-contract-sync
refactor/extract-query-repository
test/namespace-membership
ops/add-grafana-dashboard
```

---

## Commit Message Rules

### Format

```
<type>(<scope>): <subject>

<body>         (optional)
```

Each commit is a single logical change. Do not bundle unrelated changes.

### Type

Same types as branch naming: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `ops`.

### Scope

The module or domain area the change affects. Common scopes:

| Scope | Area |
|-------|------|
| `skill`, `publish`, `review` | `skillhub-domain` entities/services |
| `auth`, `oauth`, `rbac` | `skillhub-auth` module |
| `search` | `skillhub-search` or search UI |
| `namespace` | Namespace domain + controllers |
| `governance` | Governance/reporting domain |
| `admin` | Admin panel (backend + frontend) |
| `ui` | Frontend shared components |
| `api` | REST API contract changes |
| `ci` | CI workflow changes |
| `deploy` | Docker/K8s/deployment changes |
| `docs` | Documentation only |

### Subject

- Imperative present tense ("add", "fix", not "added", "fixed")
- No period at the end
- Capitalize the first letter
- Keep under 72 characters

### Body (optional)

- Explain *why* the change was made, not *what* (the code shows what)
- Wrap at 72 characters
- Reference issues or PRs when applicable

### Examples

```
feat(publish): add multipart upload support for large skill packages

The previous JSON body approach hit request size limits for packages
>10MB. Adding multipart upload to match the OpenAPI contract spec.

Closes #142
```

```
fix(auth): handle null provider in OAuth2 callback

Some OAuth providers (e.g., GitLab) return different userinfo
structures. Null-check the provider field before accessing it.
```

```
docs(api): clarify skill version status transition table
```

```
refactor(review): extract GovernanceQueryRepository from GovernanceService
```

---

## Workflow: From Code to Merge

```bash
# 1. Start from latest develop
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feat/my-feature

# 3. Work, commit frequently
git add <files>
git commit -m "feat(scope): description"

# 4. Keep branch up to date with develop
git fetch origin
git rebase origin/develop

# 5. Push and create PR
git push origin feat/my-feature
# -> Open PR against develop on GitHub
```

### PR Title

Same format as commit messages:

```
<type>(<scope>): <description>
```

The PR title should summarize the overall change. If multiple commits, the PR title captures the feature as a whole.

### Pre-Merge Checklist

- [ ] Rebased on latest `develop`
- [ ] `make test-backend-app` passes
- [ ] `make typecheck-web` passes (if frontend changed)
- [ ] `make lint-web` passes (if frontend changed)
- [ ] `make generate-api` run and committed (if API changed)
- [ ] New behavior has tests
- [ ] Docs updated if needed
- [ ] Smoke test passes (run `make staging` for significant changes)

---

## Squash vs. Merge

- **One-commit features**: use squash-merge to keep `develop` history clean.
- **Multi-commit features** (rare, only when each commit is independently meaningful): use regular merge.
- **Always rebase** to avoid merge commits from conflict resolution.
