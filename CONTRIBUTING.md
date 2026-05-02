# Contributing to DSM VS Code Extension

Thanks for your interest in contributing.

## Reporting issues

Use [GitHub Issues](https://github.com/digital-substrate/dsm-vscode/issues) and pick the appropriate template (bug report or feature request).

## Submitting pull requests

1. Fork the repository and create a feature branch from `main`
2. Make your changes (see "Running locally" below)
3. Open a pull request with a clear description of what changed and why

## Running locally

This extension has no runtime dependencies beyond VS Code itself (syntax highlighting via TextMate grammar, snippets, and language configuration).

To test changes:

1. Open this folder in VS Code
2. Press `F5` to launch the Extension Development Host
3. Open a `.dsm` file in the new window to exercise the extension

## Packaging

Install `vsce` globally if not already present:

```bash
npm install -g @vscode/vsce
vsce package         # Produces a .vsix file
```

## Code conventions

- Keep `syntaxes/dsm.tmLanguage.json` consistent with the DSM language grammar
- Keep `snippets.json` aligned with common DSM patterns
- Update `CHANGELOG.md` for user-visible changes

## License

This project is licensed under the MIT License (see [LICENSE](LICENSE)). By submitting a pull request, you agree that your contribution is provided under the same license (inbound = outbound). No CLA is required.
