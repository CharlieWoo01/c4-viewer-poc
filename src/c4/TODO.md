# C4 Viewer — TODOs

Tracking improvements for LikeC4 diagram generation, validation, and CI integration.

---

## Immediate Tasks

- [ ] Clean up duplicate specification definitions  
  Ensure only one `specification.c4` exists in the workspace.  
  All `.c4` model files should reference it rather than defining `specification` blocks inline.

- [ ] Validate all models before build  
  Add a `lint:c4` script that runs:
  ```bash
  likec4 lint src/c4
````

Include this in GitHub Actions before the build step.

---

## Model Validation (from LikeC4 Guide)

* [ ] Add automated LikeC4 model validation tests using [`LikeC4.fromWorkspace()`](https://likec4.dev/guides/validate-your-model/)
* [ ] Create `tests/model.spec.ts` with Vitest:

  * Check that all `system` elements have `description`
  * Verify no duplicate IDs or missing titles
  * Validate that all `.c4` files compile without warnings
* [ ] Add `npm run test:c4` and include it in CI before `build:c4`
* [ ] Optionally pre-generate the model for faster test execution

---

## Future Enhancements

* [ ] Add CI job to upload built diagrams as artifacts for review
* [ ] Add visual diffing to detect layout or link changes
* [ ] Create `docs/DIAGRAMS.md` explaining architecture hierarchy and naming conventions
* [ ] Investigate a custom LikeC4 plugin to detect:

  * Unused elements
  * Missing descriptions
  * Duplicate names or icons

---

## Reference Commands

```bash
# Validate syntax and structure
likec4 lint src/c4

# Export models for snapshot tests
likec4 export json src/c4 --outfile exports/views.json

# Build all diagrams (CI mode)
likec4 build src/c4 -o dist --use-dot
```