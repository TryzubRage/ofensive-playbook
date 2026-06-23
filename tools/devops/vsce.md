# vsce

`vsce` packages Visual Studio Code extensions into `.vsix` files. In this repo it is used to build a malicious extension that executes JavaScript on activation.

## Commands Used

### Install and Package an Extension
<!-- cmd: linux -->
```bash
npm install -g @vscode/vsce
vsce package --allow-missing-repository
```
Used on: **Checkpoint**

`--allow-missing-repository` bypasses the packaging warning when the extension is not backed by a public repository.
