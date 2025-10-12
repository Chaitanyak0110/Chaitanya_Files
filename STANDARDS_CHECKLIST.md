# Frontend App Standards Checklist

This document defines an automated checklist for evaluating whether this repository meets the expectations of a high-quality frontend-only Angular application.

Use the companion script `scripts/check_standards.js` to run the checks automatically. The script will print a PASS/FAIL summary and non-blocking suggestions.

Sections and checks

1) Project Metadata
- [ ] `package.json` contains scripts for `start`, `build`, `test`, and `check:standards`.
- [ ] `README.md` contains developer quickstart and testing instructions.

2) Code Quality
- [ ] TypeScript compiler is configured (`tsconfig.json` exists and `strict` options present where applicable).
- [ ] Prettier or formatting rules present (look for `prettier` field or `.prettierrc`).
- [ ] Linting configured (ESLint configs or similar).

3) Tests
- [ ] Unit tests exist under `src/**/*.spec.ts` and `npm test` runs successfully (Karma/Jasmine or configured runner).
- [ ] CI is set up to run tests on PRs (look for `.github/workflows/ci.yml`).

4) Accessibility & Performance
- [ ] Basic accessibility attributes used in templates where applicable (`aria-` attributes on interactive elements).
- [ ] No inline styles that conflict with global styling conventions.

5) Security & Sensible Defaults
- [ ] No hard-coded secrets in repository.
- [ ] Application does not depend on an external backend to run basic UI flows (localStorage seeding or mocks available).

6) Repository Hygiene
- [ ] `.github/*` contains PR and issue templates and CODEOWNERS where appropriate.
- [ ] Contribution guide (`CONTRIBUTING.md`) and code style (`CODE_STYLE.md`) present.

7) Build and Serve
- [ ] `ng serve` or `npm start` should start the frontend locally.
- [ ] `ng build` or `npm run build` should produce a production bundle without TypeScript errors.

8) Optional Pro Tips (recommended)
- [ ] lint-staged + Husky to run linters/tests on staged files.
- [ ] Unit test coverage thresholds configured.
- [ ] Automated accessibility check in CI (axe or pa11y).

Notes
- The checklist is intentionally conservative; it focuses on basic, verifiable checks a frontend-only app should satisfy.
- The companion Node script inspects the repository and reports which checks pass. It is not a substitute for human review for subjective items (accessibility and performance) but will flag obvious omissions.

Run:

npm run check:standards
