




- **Linting:**
  - Prettier + ESLint for frontend
  - ktlint for Android/Kotlin
  - flake8 and black for backend
- **Pull Requests:**
  - Write clear summaries for every PR
  - Link related issues (use `Fixes #issue_num`)
  - At least one reviewer before merging
  - Pass all CI checks (tests, lint)
- **Documentation:**
  - Use JSDoc (frontend), KDoc (Android), Python docstrings (backend)
  - Update README and feature docs for major changes
- **Branding & Design:**
  - Follow MyHaki brand guidelines for colors, typography, logos
    - See [Brand Guideline Reference](images/brand-guideline.png)
  - Keep UI consistent with Figma designs

---

## <span style="font-weight: 600; color: #A87352;">Accessibility & Values</span>

- **Accessibility:** All UIs must be usable by keyboard, screen reader, and on low-bandwidth devices.
- **Values:** Code and UI should always reflect MyHaki’s core values: Justice, Integrity, Accessibility.

---

## <span style="font-weight: 600; color: #A87352;">Best Practices</span>

- Keep functions and components small, focused and reusable.
- Prefer composition over inheritance.
- Avoid commented-out code in committed files.
- Refactor and remove unused code regularly.
- Always run lint and all tests before pushing or merging.
- Use environment variables for secrets (never commit credentials).

---

## <span style="font-weight: 600; color: #A87352;">References</span>

- [Brand Guideline](images/brand-guideline.png)
- [API Reference](backend.md)
- [Developer Docs](how-it-works.md)
- [QA Process](qa-process.md)

---