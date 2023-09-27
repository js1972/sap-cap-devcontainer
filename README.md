# sap-cap-devcontainer

A vscode devcontainer for SAP CAP / UI5 development

This devcontainer is based on the `javascript-node:0-18` docker image which use Node.js 18.

Added features in the docker container include:

- Google Chrome for use with puppeteer for headless browser tests
- Liquidprompt to enable nice bash prompts
- SQLite3
- Cloud Foundry cli v7
- Neovim
- fzf (fuzzy finder : ? toggles preview, ctrl-r for bash command history)
- bash-completion
- cds-dk v7.2.0
- Yeoman
- mbt (MTA build tool)
- ui5 tooling and cli
- @sap/generator-fiori
- githubnext/github-copilot-cli
- misc. simple bash aliases

The devcontainer config file adds:

- Forwards ports 4004 and 3010
- Installs the cf cli mutliapps plugin (doesn't seem to work from the Dockerfile)
- Set the cf api region (change this to match your region)
- Mount the host computer home folder as /host-home-folder in the container
- Adds features for github cli; shell history; some shell utils.
