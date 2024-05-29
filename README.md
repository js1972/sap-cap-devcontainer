# sap-cap-devcontainer

A vscode devcontainer for SAP CAP / UI5 development

This devcontainer is based on the `javascript-node:20` docker image which use Node.js 20 LTS.

Added features in the docker container include:

- ?? NOT WORKING YET - NEED TO FIND A LINUX/ARM64 SOLUTION : Google Chrome for use with puppeteer for headless browser tests
- SQLite3
- Cloud Foundry cli (v8)
- Neovim (latest)
- fzf (fuzzy finder : ? toggles preview, ctrl-r for bash command history) (latest)
- shell setup [arm64 version defaults to zsh which has inbuilt completion; amd64 version defaults to bash and adds :bash-completion]
- cds-dk v7.9.2 (April 2024)
- Yeoman (latest)
- mbt (MTA build tool) (latest for linux/arm64)
- ui5 tooling and cli (latest)
- @sap/generator-fiori (latest)
- githubnext/github-copilot-cli (latest)
- zsh and oh-my-sh setup (latest)

The devcontainer config file adds:

- Forwards ports 4004 and 3010.
- Installs the cf cli mutliapps plugin (doesn't seem to work from the Dockerfile).
- Set the cf api region (change this to match your region) (deafults to APJ).
- Mount the host computer home folder as /host-home-folder in the container - for easy access to files on the host computer.
- Adds features for github cli; shell history; some shell utils.

## devcontainer config files
Two sets of config files exist:
- .devcontainer folder - for macos silicon
- .devcontainer/linux_amd64 - for intel type architectures (older macs and windows).

Vscode only allows one set of these config files inside the root .devcontainer directory when working with named docker volumes so we need to duplicate one set - I have chosen to use the apple silicon files.
