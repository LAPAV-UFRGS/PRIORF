# Contributing to PRIORF

This document defines the development workflow adopted for `LAPAV-UFRGS/PRIORF`.

PRIORF is public, versioned scientific software. The repository therefore distinguishes between the **current integrated development state** and **formally published, citable releases**.

## 1. Development model

The basic model is:

```text
repository = project
main       = integrated development state
branch     = temporary work for a specific task
tag/release = formally published software version
```

The `main` branch contains changes already accepted into the project, but it may be ahead of the latest published release. Users who require a stable and citable version should use a tagged GitHub Release and its corresponding Zenodo record.

Because this repository is public, branches pushed to GitHub are also publicly visible. A development branch is not, however, a formal software release.

## 2. Starting new work

Do not use long-lived branches to represent people or parallel copies of the project. A branch should correspond to a concrete task and should normally be deleted after it is merged.

Before starting a task, update your local `main`:

```bash
git switch main
git pull origin main
```

Then create a branch from the updated `main`:

```bash
git switch -c feature/name-of-task
```

## 3. Branch naming

Use lowercase, descriptive names, preferably in `kebab-case`.

| Prefix | Purpose | Example |
|---|---|---|
| `feature/` | New functionality | `feature/network-routing` |
| `fix/` | Bug fix or correction of unintended behavior | `fix/mrvbf-path-detection` |
| `refactor/` | Internal restructuring without intended behavioral change | `refactor/susceptibility-module` |
| `docs/` | Documentation-only changes | `docs/installation-guide` |
| `test/` | Addition or maintenance of tests | `test/susceptibility-workflow` |
| `chore/` | Repository maintenance, metadata, configuration or non-feature work | `chore/institutional-transfer` |
| `hotfix/` | Urgent correction to a published or production-relevant state | `hotfix/model-download` |
| `release/` | Release preparation when a dedicated release branch is useful | `release/v2.2.0` |

For normal development, prefer `feature/`, `fix/`, `refactor/`, `docs/`, `test/` and `chore/`.

## 4. Commits

Commits should represent coherent units of work and use concise messages that explain what changed.

Examples:

```text
Add route disruption analysis
Fix model asset download URL
Update installation instructions
Refactor susceptibility preprocessing
```

Avoid vague messages such as `update`, `changes`, `fix` or `adjustments` without context.

A task may contain multiple commits when that makes the history easier to understand and review.

## 5. Publishing a branch

On the first push of a new branch:

```bash
git push -u origin feature/name-of-task
```

After that:

```bash
git push
```

## 6. Keeping a branch current

If `main` changes while a branch is being developed, update the local `main` and merge it into the work branch when needed:

```bash
git switch main
git pull origin main
git switch feature/name-of-task
git merge main
```

Resolve any conflicts in the work branch and validate the result before continuing.

Rebase may be used by contributors familiar with history rewriting, but it is not required by the project workflow.

## 7. Pull Requests

When a task is ready, open a Pull Request from the temporary branch into `main`.

The Pull Request should make clear:

- what was changed;
- why the change was necessary;
- how the change was checked or tested;
- whether the change affects outputs, methodology, dependencies, data requirements or compatibility.

After the Pull Request is reviewed and merged, the temporary branch can normally be deleted.

## 8. Scientific releases and versioning

A commit to `main` is **not** automatically a new published version of PRIORF.

Formal versions are represented by Git tags and GitHub Releases, for example:

```text
v2.1.0
v2.2.0
v3.0.0
```

The project follows semantic-versioning principles:

- **PATCH** (`2.1.0` → `2.1.1`): compatible fixes and minor corrections;
- **MINOR** (`2.1.0` → `2.2.0`): new compatible functionality;
- **MAJOR** (`2.1.0` → `3.0.0`): incompatible or methodologically breaking changes.

Before publishing a release, review at least:

- the software version (`__version__`);
- `CHANGELOG.md`;
- `README.md` where user-facing behavior changed;
- `CITATION.cff` metadata;
- `.zenodo.json` metadata;
- required model/data download references;
- installation and dependency information.

A release should be created from an accepted state of `main`. Do not create test releases merely to test the Zenodo integration.

## 9. Zenodo and DOI workflow

The GitHub repository is integrated with Zenodo. Published GitHub Releases are archived as versioned scientific software records.

PRIORF uses:

- a **version-specific DOI** for each archived release;
- a **Concept DOI** representing the full series of versions and resolving to the latest version.

Existing release tags and Zenodo records are part of the scientific provenance of the project. Do not delete, recreate or move historical release tags casually.

When a new release is published, verify that Zenodo has archived it successfully and that the new version remains in the existing PRIORF version series before updating citation metadata where necessary.

## 10. Data, models and large files

Do not commit generated outputs, temporary processing files, credentials or private data to the repository.

Large geospatial datasets that must be versioned may use Git LFS according to the existing `.gitattributes` rules. Large trained models should not be added to ordinary Git history; release assets or another explicitly documented distribution mechanism should be used instead.

The current census GeoPackage is managed with Git LFS. Model artifacts used by released versions are distributed through GitHub Release assets and checked by SHA-256 in the application.

## 11. Credentials and security

Never commit:

- Google Earth Engine service-account JSON keys;
- passwords, API keys, access tokens or private SSH keys;
- local files containing credential paths or account information;
- `.env` files containing secrets.

If a credential is accidentally committed, removing it in a later commit is not sufficient. The credential must be considered exposed and should be rotated, with repository history cleaned when appropriate.

## 12. Development and published versions

A typical lifecycle is:

```text
main
  │
  ├── feature/new-capability
  │         │
  │         └── Pull Request ──► main
  │
  ├── fix/specific-problem
  │         │
  │         └── Pull Request ──► main
  │
  └── accepted release state
              │
              ├── tag vX.Y.Z
              ├── GitHub Release
              └── Zenodo archived version + DOI
```

This workflow allows ongoing development to remain transparent while preserving stable, immutable and citable scientific releases.
