# MacOS Setup

This guide covers the basics of setting up a development environment on a new Mac. Whether you are a beginner or an experienced developer, this could be helpful for anyone to use as a reference for setting up a complete environment and/or installing tools.

To start with, you need to understand what is a `root` user. Then, step-by-step, you will be guided to install tools per category. Of course, it you read through a step and you don't know what the tools is about, you may just skip it. It's not recommanded to installing something you don't know what it is. Or you do something searching to get to know about that tool then ask yourself if you'll use it or not, thus install it or not.

## Root user

The most advanced tasks demand the most powerful user account of them all: the root user. When you log in as root, you have read and write privileges to every file on your Mac. This awesome power lets you override any account or permission restriction, but be careful! You could really mess things up if you don’t know what you’re doing.

> __Warning__: The root account is disabled by default. To prevent accidents, you should only enable and utilize the root account when you absolutely need it.

### Acting as Root with Sudo

Before you enable the root user account on your Mac, you should know about an alternative called sudo. As in other Unix-based operating systems, this command allows administrators to temporarily act as the root user in Mac OS X

If you still need it, you can proceed the following step.

### Enabling the Root User Account

`System prefs > Users & Groups > Login options > Network Account Server : Join ... > Open Directory Utility ... > (Menu) Edit > Enable Root User`

However, this 'Enable Root User' option may have been removed from late macos version, and an admin user can use sudo.

## First Steps

1. Install [XCode from AppStore](https://developer.apple.com/xcode/)

If you are about to install a development environment, this is the first must have.

```sh
xcode-select --install
```

2. Homebrew

[Homebrew](https://brew.sh) complements MacOS, and installs the stuff you need in a simple way. It is a package manager for which makes installing lots of different software like Git, Ruby, and Node simpler. Homebrew lets you avoid possible security problems associated with using the sudo command to install software like Node. 

Installing Homebrew is straightforward as long as you understand the Mac Terminal.

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

# Intel chips
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/usr/local/bin/brew shellenv)"

# M chips
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"

brew update
```

3. Git

```sh
brew install \
    git \
    tig

git config --global core.editor nano
git config --global user.name 'fullname'
git config --global user.email 'example@mail.com'

brew tap alajmo/mani
brew install mani
```

4. SSH

Many of us are familiar with Secure Shell (SSH), which allows us to connect to other systems using a key instead of a password. This guide will explain how to eliminate SSH keys and use a GNU Privacy Guard (GPG) subkey instead.

Using GPG does not make your SSH connections more secure. SSH is a secure protocol, and SSH keys are secure. Instead, it makes certain forms of key distribution and backup management easier. It also will not change your workflow for using SSH. All commands will continue to work as you expect, except that you will no longer have SSH private keys and you will unlock your GPG key instead.

By having SSH authenticated by your GPG key, you will reduce the number of key files you need to secure and back up. This means that your key management hygiene still has to be good, which means choosing good passphrases and using appropriate key preservation strategies. Remember, you shouldn't back your private key up to the cloud!

Additionally, today SSH keys are distributed by hand and oftentimes directly. If you want to grant me access to a machine, you have to ask me for my SSH key. You may get lucky and find one posted on my website. However, you still have to decide if you trust my website. If I use a GPG key for SSH, you can select a known, good key for me using the GPG web of trust from a public keyserver. This is what The Monkeysphere Project is working on. Otherwise, nothing you do here affects the web of trust used for GPG encryption and signing.

- Ssh-keygen

The SSH protocol uses public key cryptography for authenticating hosts and users. The authentication keys, called SSH keys, are created using the keygen program.

Ssh-keygen is a tool for creating new authentication key pairs for SSH. Such key pairs are used for automating logins, single sign-on, and for authenticating hosts.

```sh
#ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

ssh-keygen -t ed25519
# then follow instructions in command line

# kill ssh-agent
eval "$(ssh-agent -k)"

# start ssh-agent
eval "$(ssh-agent -s)"

#ssh-add -K ~/.ssh/id_rsa
ssh-add -K ~/.ssh/id_ed25519
# Enter passphrase for /Users/user/.ssh/id_ed25519:
# Identity added: /Users/user/.ssh/id_ed25519 

# Copy the contents of the id_ed25519.pub file to your clipboard
pbcopy < ~/.ssh/id_ed25519.pub
# Load your GitHub Settings page for adding an SSH key and paste the contents of your clipboard
```

5. GPG (TGNU Privacy Guard)

GnuPG is a complete and free implementation of the OpenPGP standard as defined by RFC4880 (also known as PGP). GnuPG allows you to encrypt and sign your data and communications; it features a versatile key management system, along with access modules for all kinds of public key directories.

```sh
brew install gpg2 gnupg pinentry-mac  

export GPG_TTY=$(tty)
echo 'export GPG_TTY=$(tty)' >> ~/.zprofile

# This tells gpg to use the gpg-agent
mkdir  ~/.gnupg
chmod 700 ~/.gnupg


gpg --full-generate-key
# then follow instructions in command line


# remove key
# gpg --delete-key key-ID


echo 'use-agent' > ~/.gnupg/gpg.conf
echo 'pinentry-program $(brew --prefix)/bin/pinentry-mac' > ~/.gnupg/gpg-agent.conf


# get your key id
# gpg -k
# sec rsa4096/######## YYYY-MM-DD [SC] [expires: YYYY-MM-DD]
gpg --list-keys --keyid-format=long

# The export command below gives you the key you add to GitHub
gpg --armor --export <your key id> | pbcopy
# GPG key copied to clipboard, beginning with -----BEGIN PGP PUBLIC KEY BLOCK----- and ending with -----END PGP PUBLIC KEY BLOCK-----
# Add a new PGP key to your GitHub keys page

# Configure Git to use gpg
git config --global gpg.program $(which gpg)
git config --global user.signingkey ########
git config --global commit.gpgsign true

# If you have any errors when generating a key regarding gpg-agent, try the following command to see what error it generates:
gpgconf --kill gpg-agent
killall gpg-agent
gpg-agent --daemon
gpg-connect-agent reloadagent /bye
```

Check if the gpg is properly installed:

```
which gpg
gpg --version
echo "test" | gpg --clearsign
```


## Install CLI tools

You may refer to all available tools at [Homebrew formula](https://formulae.brew.sh/formula/). For the following steps, you will be install programming languages, and CLI tools for development purpose, so you won't have any details about each package to be installed.

6. Http utility :

```sh
brew install \
    curl \
    jq \
    htop \
    ncdu \
    tmux \
    HTTPie \
    tree \
    wget
```

7. Shell Utility :

```sh
brew install bash-completion

echo '[[ -r "$(brew --prefix)/etc/profile.d/bash_completion.sh" ]] && . "$(brew --prefix)/etc/profile.d/bash_completion.sh"' >> ~/.bash_profile
```

oh-my-zsh :

```sh
brew install zsh

#chmod -R 755 '$(brew --prefix)/share/zsh'
chmod -R go-w '$(brew --prefix)/share/zsh'
```

```sh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

Then open a new tab/terminal to spawn a new zsh console in order to customize your oh-my-zsh.

Shell Integration:
```sh
curl -L https://iterm2.com/shell_integration/install_shell_integration_and_utilities.sh | bash

curl -L https://iterm2.com/shell_integration/zsh -o ~/.iterm2_shell_integration.zsh
```

Plugins :

```sh
brew install zsh-completions

chmod -R go-w '$(brew --prefix)/share/zsh'

echo '
if type brew &>/dev/null; then
    FPATH=$(brew --prefix)/share/zsh-completions:$FPATH
    FPATH=$(brew --prefix)/share/zsh-navigation-tools:$FPATH
    FPATH=$(brew --prefix)/share/zsh/site-functions:$FPATH
    FPATH=$HOME/.oh-my-zsh/plugins/git:$FPATH
    FPATH=$HOME/.oh-my-zsh/lib/functions:$FPATH
    FPATH=$HOME/.oh-my-zsh/lib/completions:$FPATH
    FPATH=$HOME/.oh-my-zsh/cache/completions:$FPATH
    #FPATH=$(brew --prefix)/Cellar/zsh/5.9/share/zsh/functions:$FPATH

    autoload -Uz compinit
    compinit
fi
' >> ~/.zprofile
```

```sh
brew install zsh-autosuggestions

echo 'source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh' >> ~/.zprofile

```

```sh
brew install zsh-history-substring-search

echo 'source $(brew --prefix)/share/zsh-history-substring-search/zsh-history-substring-search.zsh' >> ~/.zprofile
```

```sh
brew install zsh-navigation-tools

echo 'source $(brew --prefix)/share/zsh-navigation-tools/zsh-navigation-tools.plugin.zsh' >> ~/.zprofile
```

```sh
brew install zsh-syntax-highlighting

echo 'export ZSH_HIGHLIGHT_HIGHLIGHTERS_DIR=$(brew --prefix)/share/zsh-syntax-highlighting/highlighters' >> ~/.zprofile
```

Oh-my-zsh plugins :

```sh
sed -i -e "/plugins=(git)/plugins=(alias-finder brew common-aliases copypath copyfile docker docker-compose dotenv encode64 extract git jira jsontools node npm urltools vi-mode vscode web-search z)/g" ~/.zshrc
```

```sh
export echo 'ZSH_ALIAS_FINDER_AUTOMATIC=”true”' >> ~/.zprofile
```

Theme : 

```sh
brew install romkatv/powerlevel10k/powerlevel10k

echo 'source $(brew --prefix)/opt/powerlevel10k/powerlevel10k.zsh-theme' >> ~/.zprofile
```

Restart your machine.

7.1 Reset oh-my-zsh (if needed):

```sh
rm -f ~/.zshrc*
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

8. Python utility :

```sh
brew install \
    python \
    pipenv \
    pyenv 
```

Install pip :

```sh
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
#python get-pip.py --user
pip3 install --upgrade setuptools
pip3 install --upgrade pip
```

9. Node utility :

```sh
brew install \
    node \
    yarn \
    yarn-completion
```

NVM :

```sh
brew intsall nvm
#curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash

mkdir ~/.nvm

echo 'export NVM_DIR="$HOME/.nvm"
  [ -s "$(brew --prefix)/opt/nvm/nvm.sh" ] && \. "$(brew --prefix)/opt/nvm/nvm.sh"  # This loads nvm
  [ -s "$(brew --prefix)/opt/nvm/etc/bash_completion.d/nvm" ] && \. "$(brew --prefix)/opt/nvm/etc/bash_completion.d/nvm"  # This loads nvm bash_completion' >> ~/.zprofile
```

10. Docker utility :

```sh
brew install docker
```

Docker with UI :

```sh
brew install --cask docker
# brew install --cask docker-toolbox
```

```sh
brew install minikube kind
```

11. IDE

```sh
brew install --cask visual-studio-code
brew install --cask intellij-idea-ce 
brew install --cask atom
```

12. Complementary Dev Tools

```sh
brew install --cask \
    iterm2 \
    cheatsheet \
    chrome-devtools \
    postman \
    # cmake \
    # gitkraken \
    # kafka-tool
```

13. Ansible (optional) :

```sh
brew install ansible
```

14. Paas (optional) :

```sh
brew install heroku/brew/heroku
```

15. Virtual Machine (optional) :

* virtualbox not yet compatible with M1 arm machine

```sh
brew install --cask virtualbox vagrant
```

16. Java utility (optional) :

```sh
brew install --cask adoptopenjdk

brew install \
    maven \
    gradle \
    scala \
    sbt
```

SDKMAN (optional) :
```sh
curl -s "https://get.sdkman.io" | bash

echo 'source $HOME/.sdkman/bin/sdkman-init.sh' >> ./.zprofile

# sdk install java x.y.z-adpt
# sdk install java x.y.z-open
# sdk install java x.y.z-grl

```

## Terminal Customization

17. dotfiles customisation

Customize Bash :

```sh
curl -L https://raw.githubusercontent.com/newlight77/dotfiles/main/customize-bash.sh | bash
curl -L https://raw.githubusercontent.com/newlight77/dotfiles/main/customize-util.sh | bash
```

NeoVim Customization :

```sh
brew install neovim
pip3 install pynvim

# I think these 3 are not required
#pip3 install 'python-lsp-server[all]'
#brew install ripgrep
#brew install fd

curl -L https://raw.githubusercontent.com/newlight77/dotfiles/main/customize-nvim.sh | bash
```

Examples of language server to be installed (support for LSP on Vim) :

```sh
npm install -g bash-language-server
npm install -g typescript-language-server typescript
npm install -g graphql-language-service-server

npm install -g pyright
pip3 install "python-language-server[all]"

npm install -g dockerfile-language-server-nodejs

#go get golang.org/x/tools/gopls@latest
#composer require felixfbecker/language-server
#dotnet tool install --global csharp-ls
```

Git Customization :
```sh
curl -L https://raw.githubusercontent.com/newlight77/dotfiles/main/customize-git.sh | bash

git config --global core.editor nano
git config --global user.name 'fullname'
git config --global user.email 'example@mail.com'
```

Undo it:

```sh
m -fr /tmp/dotfiles
rm -rf $HOME/.config/bash
rm -rf $HOME/.config/git
rm -rf $HOME/.config/nvim
rm -rf $HOME/.onfig/util
```

## Github

```sh
brew install gh
```

```sh
echo '
machine api.github.com
login <user>
password <token>
' >> ~/.netrc
```

Github requires a personal access token, a gpg key, and a ssh key.

Here we gonna need the generated GPG Key.

```bash
# get your key id
# gpg -k
# sec rsa4096/######## YYYY-MM-DD [SC] [expires: YYYY-MM-DD]
```

Now encrypt you email address using the gpg key, where <EMAIL> is the address you used when creating the key:

```bash
gpg -e -r <EMAIL> ~/.netrc
```

### Github enterprise

If you want to access github entreprise on premiss, set this in vimrc :

```bash
let g:github_enterprise_urls = ['https://example.com']
```

### git credentials using git-credential-netrc (optional)

Note : This step is already covered by [customize-git.sh](https://raw.githubusercontent.com/newlight77/dotfiles/main/customize-git.sh).

You gonna need a credential helper to decrypt the .netrc file automatically by git:

```bash
echo "creating folder $HOME/.config/util" 1>&2
mkdir $HOME/.config/util/

echo "retrieve the git-credential-netrc from github"
curl -o $HOME/.config/util/git-credential-netrc https://raw.githubusercontent.com/git/git/master/contrib/credential/netrc/git-credential-netrc.perl
chmod +x $HOME/.config/util/git-credential-netrc

echo "adding export GPG_TTY and add git-credential-netrc to PATH in $HOME/.zshrc" 1>&2
echo '
# ===================================================================
# added by https://github.com/newlight77/dotfiles
export PATH=$HOME/.config/util/:$PATH
export GPG_TTY=$(tty)
# ===================================================================
' >> $HOME/.zshrc
```

At last, configure git to use credential helper

```bash
git config --global credential.helper "netrc -f ~/.netrc.gpg -v"
# automatically sign commits
git config --global commit.gpgsign true
```

## Useful links

You may refer to the following links to see how tools installation can be fully automated.

- [:rocket: MacOS setup](https://github.com/sb2nov/mac-setup)
- [MacOS machine config](https://github.com/bkuhlmann/mac_os-config)
- [MacOS machine setup](https://github.com/bkuhlmann/mac_os)
- [Set Up Your MacBook for Web Development](https://medium.com/better-programming/setting-up-your-mac-for-web-development-in-2020-659f5588b883#a175)
- [dotfiles setup](https://github.com/newlight77/dotfiles)

