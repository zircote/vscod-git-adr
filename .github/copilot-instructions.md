# Copilot Instructions for Git ADR VS Code Extension

## Project Overview

This is a Visual Studio Code extension that provides integration with [git-adr](https://github.com/zircote/git-adr), allowing users to manage Architecture Decision Records (ADRs) stored in git notes directly within VS Code.

**Key Technologies:**
- TypeScript
- VS Code Extension API
- Git integration
- Node.js

## Architecture

The codebase is organized as follows:

- **`src/`**: Source code
  - `extension.ts`: Main extension entry point
  - `cli/`: Git and git-adr CLI integration
  - `commands/`: VS Code command implementations
  - `documents/`: Virtual document provider for ADR content
  - `utils/`: Utility functions and helpers
  - `views/`: Tree view provider for ADR sidebar
- **`test/`**: Test suites
  - `suite-unit/`: Unit tests (mocked dependencies)
  - `suite-integration/`: Integration tests (real git operations)
  - `helpers/`: Test helpers and fixtures
- **`resources/`**: Extension icons and assets

## Development Workflow

### Setup
```bash
npm ci                  # Install dependencies
npm run compile         # Compile TypeScript
```

### Code Quality
```bash
npm run lint            # Run ESLint
npm run lint:fix        # Auto-fix ESLint issues
npm run format          # Format code with Prettier
```

### Testing
```bash
npm test                        # Run unit tests in VS Code extension host
npm run test:integration        # Run integration tests
xvfb-run -a npm test           # Run tests on Linux CI/headless
```

### Packaging
```bash
npm run package         # Create .vsix file
```

## Coding Standards

### TypeScript
- **Strict mode enabled**: All TypeScript strict checks are enforced
- **No unused variables/parameters**: Use `_` prefix for intentionally unused parameters
- **Explicit return types**: Functions should have explicit return types when not obvious
- **Error handling**: Always handle errors gracefully and provide user-friendly messages

### Code Style
- **Quotes**: Single quotes for strings (enforced by ESLint)
- **Semicolons**: Required (enforced by ESLint)
- **Naming conventions**:
  - camelCase for variables and functions
  - PascalCase for classes and interfaces
  - UPPER_CASE for constants
- **Formatting**: Use Prettier configuration (`.prettierrc.json`)

### VS Code Extension Patterns
- **Command registration**: All commands must be registered in `package.json` and `extension.ts`
- **Error messages**: Use `vscode.window.showErrorMessage()` for user-facing errors
- **Output channel**: Use the extension's output channel for debugging information
- **Async operations**: Always use async/await, never callbacks
- **Disposables**: Register all disposables in the extension context

## Testing Guidelines

### Unit Tests
- Located in `test/suite-unit/`
- Use mocked dependencies (see `test/helpers/mockCommandRunner.ts`)
- Fast execution, no real git operations
- Test individual functions and classes in isolation

### Integration Tests
- Located in `test/suite-integration/`
- Use real git repositories (see `test/helpers/testWorkspace.ts`)
- Test complete workflows (init, create, list ADRs)
- Clean up test fixtures after each test

### Test Conventions
- Use Mocha test framework
- Use Sinon for mocking and spying
- Test files should end with `.test.ts`
- Each test should be independent and idempotent

## Dependencies

### Runtime Dependencies
None - this is a pure VS Code extension that shells out to git/git-adr CLI

### Dev Dependencies
- **TypeScript 5.3.3**: Language and compiler
- **ESLint**: Linting with TypeScript plugin
- **Prettier**: Code formatting
- **Mocha**: Test framework
- **Sinon**: Test mocking/stubbing
- **@vscode/test-electron**: VS Code extension testing

## Common Tasks

### Adding a New Command
1. Add command definition to `package.json` under `contributes.commands`
2. Implement command handler in `src/commands/`
3. Register command in `src/extension.ts`
4. Add tests in `test/suite-unit/` and/or `test/suite-integration/`
5. Update README.md with command documentation

### Modifying CLI Integration
1. Changes to git/git-adr invocation go in `src/cli/`
2. Always handle errors (git not found, git-adr not installed, etc.)
3. Add unit tests with mocked command execution
4. Add integration tests with real git operations

### Working with Virtual Documents
- ADR content is provided via `AdrDocumentProvider` in `src/documents/`
- Uses custom URI scheme: `git-adr://`
- Content updates trigger document refresh via event emitter

## Important Constraints

### External Dependencies
The extension requires users to have:
1. **Git**: Must be in PATH or configured via `gitAdr.gitPath` setting
2. **git-adr**: Python CLI tool, must be installed separately

### Error Handling
- Never assume git or git-adr is installed
- Provide clear error messages with installation instructions
- Use graceful degradation when commands fail

### Cross-Platform Support
- Test on Windows, macOS, and Linux
- Use `vscode.workspace.workspaceFolders` for multi-root support
- Use Node.js `path` module for path operations

## CI/CD

The project uses GitHub Actions:
- **CI workflow** (`.github/workflows/ci.yml`):
  - Runs on every push to main and all PRs
  - Executes: lint, compile, unit tests, integration tests
  - Packages extension as .vsix artifact
  - Uses Xvfb on Linux for VS Code extension host
- **Release workflow** (`.github/workflows/release.yml`):
  - See `RUNBOOK_RELEASE.md` for details

## Resources

- [VS Code Extension API](https://code.visualstudio.com/api)
- [git-adr CLI](https://github.com/zircote/git-adr)
- [Git Notes Documentation](https://git-scm.com/docs/git-notes)

## Notes for Copilot

When making changes:
1. **Always run tests**: `npm test` and `npm run test:integration`
2. **Run linter**: `npm run lint` before committing
3. **Update package.json**: If adding new commands, update both `commands` and `activationEvents`
4. **Maintain backward compatibility**: This extension is published to VS Code Marketplace
5. **Consider multi-root workspaces**: Test with multiple folders open
6. **Document user-facing changes**: Update README.md and CHANGELOG.md
