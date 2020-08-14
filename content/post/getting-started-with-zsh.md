---
title: "Zsh with Oh My Zsh"
date: 2020-08-08T04:06:11+05:30
draft: flase
url: zsh
tags: ["zsh", "gnu/linux","cli"]
---

**Z shell** or **Zsh** is a unix shell that was written by Paul Falstad in 1990. The name zsh derives from the name of Yale professor Zhong Shao (then a teaching assistant at Princeton University) — Paul Falstad regarded Shao's login-id, "zsh", as a good name for a shell.
Zsh has features like interactive Tab completion, automated file searching, regex integration, advanced shorthand for defining command scope, and a rich theme engine. Combine it with a framework like oh-my-zsh, it really could help you be more productive.

### Installing Zsh
- Debian, Ubuntu and other Debian based disros:
```bash
$ sudo apt install zsh
```
- Archlinux, Manjaro and other Arch-based distros:
```bash
$ sudo apt install zsh
```
- Fedora, RHEL, and CentOS:
```bash
$ sudo dnf install zsh
```

### Installing Oh My Zsh
- Using Curl
```bash
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
- Using Wget
```bash
$ sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

At this point you might as well install a favourite powerline font. if you're on Debian or any of it's forks `sudo apt install powerline-fonts` should work for most cases.

### Plugins
A lot of plugins come built in, for example plugins for distro specific commands are available in the name of the distro it's written for, so you'll find plugins such as `debian` and `fedora`. you can find all the plugins at `~/.oh-my-zsh/plugins`. All the plugins are not enabled by default. In-order to enable a plugin you need to add the plugin nams to your plugins list in your `.zshrc`

One plugin that I find a lot of value in is the `zsh-autosuggestions` plugin. It commands based on your `.zsh_history`. But it is not a default plugin so we need to manually add it. We can do so by cloning the plugin repo to the `~/.oh-my-zsh/custom/plugins` folder. 

Here's a list of all the plugins that I use:
- colored-man-pages
- debian 
- emacs 
- git
- gnu-utils
- lxd 
- zsh-autosuggestions
- zsh-syntax-highlighting 

Only two of the above plugins are custom, so we clone them to the custom plugins folder.
```bash
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions
```
Then I add the names of the plugins to the list in my `.zshrc`
```bash
plugins=(colored-man-pages debian emacs git gnu-utils zsh-autosuggestions zsh-syntax-highlighting )
```
### Theme
There are a wide variety of themes built-in to oh-my-zsh but my favourite is `Powerlevel10k`. You can install it by running the following command:
```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```
This theme is pretty neat and if configured properly can give you a lot of information through the prompt itself. It uses a bunch of icons which you can get through the theme's recomended font Meslo Nerd Font. You can get these fonts by downloading the TTF files given below to your `~/.fonts/` folder
Download these four ttf files:
- [MesloLGS NF Regular.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf)
- [MesloLGS NF Bold.ttf]( https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf)
- [MesloLGS NF Italic.ttf]( https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf)
- [MesloLGS NF Bold Italic.ttf]( https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf)

Then set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc`.
After this source your ~/.zshrc or open a new terminal to run the setup process for the theme.

And there you go this is my setup.
