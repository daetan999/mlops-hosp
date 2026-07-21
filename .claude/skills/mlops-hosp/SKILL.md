```markdown
# mlops-hosp Development Patterns

> Auto-generated skill from repository analysis

## Overview
This skill documents the core development patterns and conventions used in the `mlops-hosp` TypeScript repository. It covers file organization, code style, commit conventions, and testing patterns, providing clear guidelines and actionable commands for consistent and efficient collaboration.

## Coding Conventions

### File Naming
- Use **camelCase** for all file names.
  - Example: `patientData.ts`, `modelTrainer.ts`

### Import Style
- Use **relative imports** for referencing modules within the project.
  - Example:
    ```typescript
    import { preprocessData } from './dataUtils';
    ```

### Export Style
- Use **named exports** for all modules.
  - Example:
    ```typescript
    // In dataUtils.ts
    export function preprocessData(data: any) { /* ... */ }
    export function cleanData(data: any) { /* ... */ }

    // In another file
    import { preprocessData, cleanData } from './dataUtils';
    ```

### Commit Messages
- Follow **conventional commit** format.
- Use the `docs` prefix for documentation-related commits.
- Keep commit messages concise (average: 43 characters).
  - Example:
    ```
    docs: update README with setup instructions
    ```

## Workflows

### Documentation Update
**Trigger:** When updating or improving documentation files.
**Command:** `/update-docs`

1. Make necessary changes to documentation files (e.g., `README.md`, `SKILL.md`).
2. Stage and commit changes using the `docs:` prefix.
   ```bash
   git add README.md
   git commit -m "docs: clarify setup instructions"
   ```
3. Push your changes to the remote repository.

## Testing Patterns

- Test files follow the `*.test.*` naming convention.
  - Example: `dataUtils.test.ts`
- The specific testing framework is not detected; look for test files matching the above pattern.
- To add a test:
  1. Create a new file with `.test.ts` suffix.
  2. Use named exports for test utilities if needed.
  3. Place tests alongside or near the module being tested.

  Example:
  ```typescript
  // dataUtils.test.ts
  import { preprocessData } from './dataUtils';

  describe('preprocessData', () => {
    it('should clean missing values', () => {
      // test implementation
    });
  });
  ```

## Commands
| Command        | Purpose                                    |
|----------------|--------------------------------------------|
| /update-docs   | Start the documentation update workflow    |
```
