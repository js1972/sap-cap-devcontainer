FROM mcr.microsoft.com/devcontainers/javascript-node:0-18

# Prepare for apt-based install of Cloud Foundry CLI by adding Cloud Foundry Foundation public key & package repository
# (see https://docs.cloudfoundry.org/cf-cli/install-go-cli.html#pkg-linux).
RUN wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | apt-key add - ; \
    echo "deb https://packages.cloudfoundry.org/debian stable main" | tee /etc/apt/sources.list.d/cloudfoundry-cli.list

# How to get Puppeteer to work inside docked:
# https://dev.to/cloudx/how-to-use-puppeteer-inside-a-docker-container-568c
# We don't need the standalone Chromium
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD true

# Install Google Chrome Stable and fonts
# Note: this installs the necessary libs to make the browser work with Puppeteer.
RUN apt-get update && apt-get install gnupg wget -y && \
  wget --quiet --output-document=- https://dl-ssl.google.com/linux/linux_signing_key.pub | gpg --dearmor > /etc/apt/trusted.gpg.d/google-archive.gpg && \
  sh -c 'echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list' && \
  apt-get update && \
  apt-get install google-chrome-stable -y --no-install-recommends && \
  rm -rf /var/lib/apt/lists/*

# Install Liquidprompt for a nice prompt in the terminal.
# https://liquidprompt.readthedocs.io/en/stable/index.html
RUN git clone --branch stable https://github.com/nojhan/liquidprompt.git /home/node/liquidprompt

# Install extra tools for CAP development & deployment.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
        sqlite3 \
        cf7-cli \
        neovim \
        bash-completion \
        fzf

# Install global node modules for SAP CAP and frontend development.
RUN npm i -g @sap/cds-dk@7.2.0 yo mbt @ui5/cli @sap/ux-ui5-tooling @sap/generator-fiori @githubnext/github-copilot-cli

# Add handy aliases and completions to .bashrc
# Setup completions for the github copilot cli
# Setup fzf with preview toggle on ? and ignoring .git and mode_modules directories files.
# Also setup fzf compltons and key bindings (ctrl-r for history, ctrl-t to copy selected file on the command line).
RUN npm completion >> /home/node/.bashrc && \
    echo "alias ll='ls -alF'" >> /home/node/.bashrc && \
    echo "alias la='ls -A'" >> /home/node/.bashrc && \
    echo "alias l='ls -CF'" >> /home/node/.bashrc && \
    echo "alias c='clear'" >> /home/node/.bashrc && \
    echo "alias gco='git checkout'" >> /home/node/.bashrc && \
    echo "# NICER BASH COMPLETIONS" >> /home/node/.bashrc && \
    echo "# If there multiple matches for a completion, Tab should cycle through them" >> /home/node/.bashrc && \
    echo "bind 'TAB:menu-complete'" >> /home/node/.bashrc && \
    echo "# And Shift-Tab should cycle backwards" >> /home/node/.bashrc && \
    echo "bind '\"\e[Z\" menu-complete-backward'" >> /home/node/.bashrc && \
    echo "# Display a list of the matching files" >> /home/node/.bashrc && \
    echo "bind 'set show-all-if-ambiguous on'" >> /home/node/.bashrc && \
    echo "# Perform partial (common) completion on the first Tab press, only start" >> /home/node/.bashrc && \
    echo "# cycling full results on the second Tab press" >> /home/node/.bashrc && \
    echo "bind 'set menu-complete-display-prefix on'" >> /home/node/.bashrc && \
    echo "source /usr/share/bash-completion/completions/git" >> /home/node/.bashrc && \
    echo 'eval "$(github-copilot-cli alias -- "$0")"' >> /home/node/.bashrc && \
    echo "export FZF_DEFAULT_OPTS=\"--layout=reverse --info=inline --height=80% --multi --preview-window=hidden --preview '([[ -f {} ]] && (bat --style=numbers --color=always {} || cat {})) || ([[ -d {} ]] && (tree -C {} | less)) || echo {} 2> /dev/null | head -200' --bind '?:toggle-preview'\"" >> /home/node/.bashrc && \
    echo "export FZF_DEFAULT_COMMAND='find . \( -name node_modules -o -name .git \) -prune -o -print'" >> /home/node/.bashrc && \
    echo "source /usr/share/doc/fzf/examples/key-bindings.bash" >> /home/node/.bashrc && \
    echo "source /usr/share/doc/fzf/examples/completion.bash" >> /home/node/.bashrc && \
    echo "# Only load Liquidprompt in interactive shells, not from a script or from scp" >> /home/node/.bashrc && \
    echo "export LP_PATH_METHOD=truncate_to_last_dir" >> /home/node/.bashrc && \
    echo "export LP_MARK_GIT=$" >> /home/node/.bashrc && \
    echo "export LP_ENABLE_SHLVL=0" >> /home/node/.bashrc && \
    echo '[[ $- = *i* ]] && source ~/liquidprompt/liquidprompt' >> /home/node/.bashrc