FROM mcr.microsoft.com/devcontainers/javascript-node:20

# Prepare for apt-based install of Cloud Foundry CLI by adding Cloud Foundry Foundation public key & package repository
# (see https://docs.cloudfoundry.org/cf-cli/install-go-cli.html#pkg-linux).
RUN wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | apt-key add - ; \
    echo "deb https://packages.cloudfoundry.org/debian stable main" | tee /etc/apt/sources.list.d/cloudfoundry-cli.list

# Install extra tools:
#   - SQLite for CAP development
#   - Cloud Foundry CLI v8
#   - Neovim
#   - Helpful bash completion if you open a bash termina (note zsh is default using oh-my-zsh)
#   - fzf fuzzy finder
#   - chromium and chromium-driver for headless browser testing (used by wdio)
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        sqlite3 \
        cf8-cli \
        neovim \
        bash-completion \
        fzf \
        chromium \
        chromium-driver

# Install global node modules for SAP CAP and frontend development.
RUN npm i -g @sap/cds-dk@7.9.2 yo @ui5/cli @sap/ux-ui5-tooling @sap/generator-fiori @githubnext/github-copilot-cli

# Install the arm64 version of the mbt tool from github releases. Cannot use npm install for this at the moment (May 2024)
RUN wget -O cloud-mta-build-tool_1.2.27_Linux_arm64.tar.gz https://github.com/SAP/cloud-mta-build-tool/releases/download/v1.2.27/cloud-mta-build-tool_1.2.27_Linux_arm64.tar.gz \
  && mkdir -p cloud-mta-build-tool \
  && tar -xzf cloud-mta-build-tool_1.2.27_Linux_arm64.tar.gz -C cloud-mta-build-tool \
  && mv cloud-mta-build-tool/mbt /usr/local/bin \
  && rm -rf cloud-mta-build-tool_1.2.27_Linux_arm64.tar.gz cloud-mta-build-tool

# --- Add better default bash and zsh configuration ---

# Install Liquidprompt for a nice prompt in the terminal.
# https://liquidprompt.readthedocs.io/en/stable/index.html
RUN git clone --branch stable https://github.com/nojhan/liquidprompt.git /home/node/liquidprompt

# Install zsh and oh-my-zsh
# Uses "eastwood" theme, with git plugins only (minimalist)
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.5/zsh-in-docker.sh)" -- \
    -t 'eastwood' -p git -x

# Add handy aliases and completions to .zshrc (most of my normal aliases are already created by oh-my-zsh)
# Setup completions for the github copilot cli
# Setup fzf with preview toggle on ? and ignoring .git and mode_modules directories files.
# Also setup fzf compltons and key bindings (ctrl-r for history, ctrl-t to copy selected file on the command line).
# We need to change the .zshrc to re-set the theme - somewhere in this process its getting overriden to `devcontainer`.
RUN npm completion >> /home/node/.bashrc && \
    echo "alias c='clear'" >> /home/node/.zshrc && \
    echo 'eval "$(github-copilot-cli alias -- "$0")"' >> /home/node/.zshrc && \
    echo "export FZF_DEFAULT_OPTS=\"--layout=reverse --info=inline --height=80% --multi --preview-window=hidden --preview '([[ -f {} ]] && (bat --style=numbers --color=always {} || cat {})) || ([[ -d {} ]] && (tree -C {} | less)) || echo {} 2> /dev/null | head -200' --bind '?:toggle-preview'\"" >> /home/node/.zshrc && \
    echo "export FZF_DEFAULT_COMMAND='find . \( -name node_modules -o -name .git \) -prune -o -print'" >> /home/node/.zshrc && \
    echo "source /usr/share/doc/fzf/examples/key-bindings.zsh" >> /home/node/.zshrc && \
    echo "source /usr/share/doc/fzf/examples/completion.zsh" >> /home/node/.zshrc && \
    sed -i '/^ZSH_THEME/c\ZSH_THEME="eastwood"' /home/node/.zshrc
