# Pixelbook Dev Setup

### Initial setup of container
```sudo apt-get update && sudo apt-get dist-upgrade```

### Set root password
```sudo passwd root```

### Setting a user password
First, log-in as root
```sudo su root```
Change password. If this is the first time, it will not ask for current password.
```passwd <username>```

### Give root & sudo access to non-root user
If more detail is needed, [check this guide](https://linoxide.com/linux-how-to/add-user-to-sudoers-on-debian/)
Add user to root group for easier Docker handling:
```usermod -aG root <username>```
Verify if this correctly added the user to the root group.
```id <username>```
You should see something like this where `0(root)` is attached to the user id.
<img src="blob:chrome-untrusted://media-app/a7177af1-72e3-4e34-9a76-b9bf88c816f3" alt="Screenshot 2021-02-15 at 2.28.50 PM.png"/>

Add user to sudoers group using the usermod command
```usermod -aG sudo <username>```
Verify if this correctly added the user to the sudo group
```id <username>```
Similar to the above screenshot for root, you should see `27(sudo)` attached to the user id.
Test the user with sudo priveleges. First, switch to the new user.
```su - username```
Now, invoke the sudo command followed by any command. in this case, we will run the whoami command.
```sudo whoami```

### Generate a new ssh key
```
ssh-keygen -t rsa -b 4096 -C pixelbook
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
cat .ssh/id_rsa.pub
# Paste in GitHub/GitLab
```

### Enable power button as 2FAS
Pixelbook's power button has a built-in 2fa/2sv key (think YubiKey), but it's disabled by default. If you use Two-Factor authentication on GitHub, Gmail etc, you can turn it on by entering this command on the Crosh shell (Ctrl+Alt+T):
```u2f_flags u2f```

### Install fonts
Create the fonts directory
```mkdir -p ~/.local/share/fonts```
Copy any desired font .ttf file to ~/.local/share/fonts
While we're here, [install recommended font for Powerlevel10k](https://github.com/romkatv/powerlevel10k#meslo-nerd-font-patched-for-powerlevel10k) (_which will be installed later_)
Cache the newly installed fonts and verify they are in the right place:
```
fc-cache -vf ~/.local/share/fonts
fc-list | grep [your font name] # To verify
```

### Install zsh and oh-my-zsh
[Easy installation instructions from this blog for Ubuntu 20.04 ](https://chrisrmiller.com/install-zsh-and-oh-my-zsh-on-ubuntu-20-04/)
Make sure everything is up to date and install prerequisites
```
sudo apt-get install build-essential curl file git
sudo apt update
sudo apt upgrade
```

Install zsh and powerline fonts
```
sudo apt install zsh
sudo apt-get install powerline fonts-powerline
```

Clone the oh-my-zsh repo
```sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"```

Copy & create the ZSH config file
```cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc```

Change the default shell
```chsh -s /bin/zsh```

### Install zsh-antigen plugin manager
[Antigen's GitHub Repo](https://github.com/zsh-users/antigen)
Install Antigen from the main repository
```curl -L git.io/antigen > antigen.zsh```
Edit the ~/.zshrc file to add the antigen path
```vi ~/.zshrc```
Comment out the existing plugin text
```Plugins (git)```
Paste this code below the plugin section
```
source /Users/[yourUserName]/antigen.zsh # this depends on where you saved the file

# Load the oh-my-zsh's library.
antigen use oh-my-zsh

# Bundles from the default repo (robbyrussell's oh-my-zsh).
antigen bundle git
antigen bundle zsh-users/zsh-autosuggestions
antigen bundle lein
antigen bundle command-not-found

# Syntax highlighting bundle.
antigen bundle zsh-users/zsh-syntax-highlighting

# Load the theme.
antigen theme robbyrussell

# Tell Antigen that you're done.
antigen apply
```
To add more plugins, go to the [official repo](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins) to get the name of the plugin you want to add
Add this line, replacing with the plugin name when you are in the `vi ~/.zshrc` file
```antigen bundle {repoName}```

### Install Powerlevel10k
[Powerlevel10k GitHub Repo](https://github.com/romkatv/powerlevel10k)
If already using Antigen, simply go the following:
Add `antigen theme romkatv/powerlevel10k` to `~/.zshrc`. Make sure that `antigen apply` is somewhere after it.

If only using `Oh-My-Zsh` without Antigen:
```git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k```

For manual installation (_which can be used with or without a plugin manager_):
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
echo 'source ~/powerlevel10k/powerlevel10k.zsh-theme' >>~/.zshrc
```

### Install VSCode
Download the .deb install file from [VSCode](https://code.visualstudio.com/)

### Set VSCode as default text editor
```sudo update-alternatives --set editor /usr/bin/code```
If Visual Studio Code doesn't show up as an alternative to editor, you need to register it:
```sudo update-alternatives --install editor /usr/bin/editor $(which code)
```

### If there are keychain issues after syncing VSCode with GitHub 
[A good explanation of the keyring concept for Ubuntu and how to use it](https://itsfoss.com/ubuntu-keyring/)
Installing gnome-keyring usually works:
```sudo apt install gnome-keyring```

### Install Docker
- Follow debian installation instructions at [Docker](https://docs.docker.com/engine/install/debian/)
- Make sure to follow [post-installation instructions](https://docs.docker.com/engine/install/linux-postinstall/) to improve usability

### Make sure to back-up all of this to make re-installing or copying to a remote server easy
[Google: Back up & Restore Your Linux (Beta) Files and Apps](https://support.google.com/pixelbook/answer/9654926?hl=en&ref_topic=9146794)
Go to Settings > LInux > Backup & restore > Backup Linux apps and files

