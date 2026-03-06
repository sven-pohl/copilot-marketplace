---
name: typescript-style
description: Practical skill for Angular + TypeScript linting, rule triage, and safe fixes using Angular ESLint, TypeScript ESLint, and Syngo presets.
---

# TypeScript Style Skill

Use this skill when you need to write, review, or fix TypeScript and Angular code in this repository while respecting the active linting contract.

## Purpose

This skill helps you:

- Apply TypeScript and Angular lint rules consistently.
- Fix lint issues with low regression risk.
- Distinguish upstream rules from Syngo-specific policy.
- Use narrow suppressions only when justified.
- Keep documentation aligned with actual ESLint configuration.

## Scope

Applies to:

- `**/*.ts` using `eslintConfig.angularTypescript`.
- `**/*.html` using `eslintConfig.angularTemplate`.
- `**/package.json` using `eslintConfig.syngoPackageJson`.

Primary rule sources:

- `@angular-eslint/eslint-plugin`
- `@angular-eslint/eslint-plugin-template`
- `@typescript-eslint/eslint-plugin`
- `@syngo/eslint-plugin`

## Ground Rules

1. Treat actual ESLint runtime config as source of truth.
2. Verify uncertain rules with `--print-config`.
3. Prefer real code fixes over suppressions.
4. Keep fixes minimal and local.
5. Respect project-level severity overrides.

## Required Workflow

1. Identify file type (`.ts`, `.html`, `package.json`).
2. Locate active config block affecting that file.
3. Confirm rule activation and severity.
4. Apply minimal semantic fix.
5. Re-run lint on changed files.
6. Use inline disable only with explicit justification.

## Verification Commands

Use commands like:

```bash
npx eslint <file>
npx eslint --print-config <file>
```

If present in the workspace, also check `workspace-setup.instructions.md` and Angular best-practices instruction files before updating lint documentation. If those files are absent, rely on `--print-config` and installed package versions.

## Syngo-Specific Policy

The following organizational rules are high-priority:

- `header/header`: enforce legal header and Windows line endings.
- `@syngo/restrict-internal-import`: block internal/private API imports.
- `@syngo/restrict-cross-package-relative-imports`: prevent crossing package boundaries through relative imports.
- `@syngo/restrict-undeclared-imports`: prevent imports not declared in nearest `package.json` (when enabled).
- `@syngo/require-caret-peer-deps`: require caret versions for configured peer dependencies.

Guidance:

- Do not disable `restrict-internal-import` except temporary migration cases with a removal plan.
- Escalate `restrict-cross-package-relative-imports` from `warn` to `error` after cleanup.
- Keep dependency hygiene strict once `restrict-undeclared-imports` is enabled.

## Angular Rule Focus Areas

Prioritize these when authoring or refactoring:

- Lifecycle correctness: no invalid/empty/manual lifecycle hook misuse.
- DI modernization: prefer `inject()` where appropriate.
- Standalone migration: `@angular-eslint/prefer-standalone` is intentionally `warn` during migration.
- Input/output metadata hygiene: avoid aliasing and deprecated metadata arrays.
- Signals modernization: prefer signal-based patterns where enabled.

Template priorities:

- Correct binding syntax (`banana-in-box`, no negated async).
- Avoid expensive patterns (`no-call-expression`, prefer control flow best practices).
- Accessibility (`alt-text`, keyboard support, ARIA validity, label association).
- Readability and maintainability (`attributes-order`, `prefer-self-closing-tags`, complexity rules).

## TypeScript ESLint Focus Areas

Treat these as common correctness anchors:

- Promise safety: `no-floating-promises`, `no-misused-promises`.
- Type safety: no unsafe `any` flows, avoid unnecessary assertions.
- Import hygiene: use type-only imports and avoid duplicate/useless imports.
- Class/API consistency: naming conventions, member ordering, interface/type consistency.
- Defensive coding: avoid non-null assertion abuse and throw `Error` objects.

Project stylistic constraints to keep:

- `@stylistic/ts/semi`: required semicolons.
- `@stylistic/ts/type-annotation-spacing`: consistent spacing.
- `unicorn/filename-case`: kebab-case file naming.
- `quotes`: single quotes with template literals when needed.
- `eqeqeq` with smart allowance.

## Common Triage Map

- Internal/private import flagged:
	Action: replace with public API export.
- Cross-package relative path flagged:
	Action: switch to package alias or public barrel export.
- Undeclared dependency flagged:
	Action: declare dependency or remove import.
- Standalone warning:
	Action: migrate from module declaration to standalone when feasible.
- Template inline style violation:
	Action: move styles to stylesheet.

## Suppression Policy

Only suppress when:

- Large migration is in progress and immediate fix is unsafe.
- Confirmed false positive exists.

Format (required rationale):

```ts
// eslint-disable-next-line <rule-name> -- why this is temporary, and when it will be removed
```

Prefer `@ts-expect-error` over `@ts-ignore` when TypeScript suppression is unavoidable.

## Known Severity Adjustments

In this repository, important adjusted severities include:

- `@angular-eslint/prefer-standalone`: `warn`
- `@angular-eslint/no-empty-lifecycle-method`: `warn`
- `@angular-eslint/no-input-rename`: `warn`

Many Syngo and TypeScript safety rules remain `error` and should be treated as non-negotiable unless explicit project exception exists.

## Maintenance Checklist

When dependencies change (`@angular-eslint/*`, `@typescript-eslint/*`, `@syngo/eslint-plugin`):

1. Re-run `--print-config` for representative `.ts`, `.html`, and `package.json` files.
2. Diff rule additions, removals, and severity changes.
3. Update lint documentation in this skill and companion docs.
4. Remove references to deprecated rules.
5. Keep migration guidance current (especially standalone and signals adoption).

## Output Expectations For Agents Using This Skill

When reporting lint work:

1. List rule violations grouped by severity.
2. Provide exact file and line references.
3. Explain minimal safe fix per violation.
4. Note any temporary suppression and removal plan.
5. Mention commands used for verification.

This skill is intentionally prescriptive: optimize for correctness, consistency, and long-term maintainability over short-term suppression.

