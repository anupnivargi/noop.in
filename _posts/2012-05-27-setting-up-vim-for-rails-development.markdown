---
layout: post
title:  "Setting up VIM for Rails Development"
date:   2012-05-27 12:40:24
---

Here is a list of some of the tools we need, to use Vim for Rails Development or for any software development for that matter.

- File browser
- Quickly Open Files
- Searching
- Goto method definitions
- Snippets
- Commenting

#### Install VIM ####

{% highlight bash %}
  sudo apt-get install vim vim-gnome vim-gui-common
{% endhighlight %}

#### Setting up Directories ####

{% highlight bash %}
mkdir ~/vim
mkdir ~/vim/bundle
mkdir ~/vim/autoload
ln -s ~/vim ~/.vim
cd ~/vim
touch vimrc
touch gvimrc
git init
{% endhighlight %}

### VIM Plugins ###

### [Pathogen](https://github.com/tpope/vim-pathogen) ###
Pathogen is a plugin manager for Vim.  

{% highlight bash %}
curl -so autoload/pathogen.vim https://raw.github.com/tpope/vim-pathogen/master/autoload/pathogen.vim
{% endhighlight %}

Add the following to ~/vim/vimrc
{% highlight bash %}
call pathogen#infect()
{% endhighlight %}

### [NerdTree (File browser)](https://github.com/scrooloose/nerdtree) ###
A tree explorer plugin for navigating the filesystem 

{% highlight bash %}
git submodule add https://github.com/scrooloose/nerdtree bundle/nerdtree
{% endhighlight %}

### [Command-T (Quickly Open Files)](https://github.com/wincent/Command-T) ###
Fast file navigation for VIM 

{% highlight bash %}
sudo apt-get install ruby1.8 rubygems1.8
sudo gem install bundler
git submodule add https://github.com/wincent/Command-T.git bundle/command-T
cd bundle/commant-T
bundle install
rake make
{% endhighlight %}

Usage:  
Goto your project folder and use "Leader t".

### [Ack  (Search)](https://github.com/mileszs/ack.vim) ###
Plugin for the Perl module / CLI script 'ack'

{% highlight bash %}
sudo apt-get install ack-grep
git submodule add https://github.com/mileszs/ack.vim.git bundle/ack
{% endhighlight %}

Usage:  
Ack config  
This will search for the word config in your project.  
Use :cn and :cp to navigate between search results

### [Ctags (Goto Method Definition)](http://ctags.sourceforge.net/) ###

{% highlight bash %}
sudo apt-get install exuberant-ctags
ctags -R --exclude=.git --exclude=.svn --exclude=log *
{% endhighlight %}

Usage:  
Run the above command in your project folder. This will generate a tags file  
To goto a definition of a method just take your cursor on the method and use Ctrl + ]  
To go back to previous position use Ctrl + o  

### [Snipmate (Code Snippets)](https://github.com/garbas/vim-snipmate) ###

{% highlight bash %}
git submodule add https://github.com/garbas/vim-snipmate bundle/snipmate
{% endhighlight %}
Snipmate Snippets 
{% highlight bash %}
git submodule add https://github.com/honza/snipmate-snippets bundle/snipmate-snippets
{% endhighlight %}
Usage:  
Use Tab after entering the snippet shortcut

### [TComment (Comment blocks of code)](https://github.com/tomtom/tcomment_vim.git) ###

{% highlight bash %}
git submodule add https://github.com/tomtom/tcomment_vim.git bundle/tcomment
{% endhighlight %}
Usage:  
Select a block and use gcc to comment the block.  
Use gc to comment current line

### [Rails.vim](https://github.com/tpope/vim-rails) ###

{% highlight bash %}
git submodule add git://github.com/tpope/vim-rails.git bundle/vim-rails
{% endhighlight %}


### [Fugitive (Git from Vim)](https://github.com/tpope/vim-fugitive) ###

{% highlight bash %}
git submodule add https://github.com/tpope/vim-fugitive.git bundle/fugitive
{% endhighlight %}

### [Color Schemes](https://github.com/squil/vim_colors) ###

{% highlight bash %}
git submodule add https://github.com/squil/vim_colors.git bundle/colors
{% endhighlight %}

### Finally Commit to Github ##

{% highlight bash %}
git add .
git commit -m "Vim configutation packages"
git remote add origin git@github.com:anupnivargi/vim.git
git pull origin master
git push origin master
{% endhighlight %}

Next time you can clone this repository and get started with your development

Happy Editing.
