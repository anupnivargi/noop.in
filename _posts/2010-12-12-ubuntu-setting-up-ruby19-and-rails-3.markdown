---
layout: post
title:  "Ubuntu: Setting up Ruby 1.9 and Rails 3"
date:   2010-12-12
published: true
---
#### Update : This post might not be relevant any more ####

I have been using Ruby 1.8.x for a long time now on many projects professionally and personally exactly for 2 years now. I had heard about Ruby Version Manager(RVM) about 10 months back, but to install a version manager just to maintain versions of a ruby was too overwhelming for me and I let it pass at that time.
But having seen the latest developments and reading stuff about Ruby 1.9.x  I decide to give ruby 1.9.x a shot. Here is what I found out.

RVM: Its is a Ruby version manager as the name suggests, but the best part is you can have many version of rubies with different flavours like MRI, JRuby, Rubinius installed with different versions of the respective flavours on the same machine.

{% highlight bash %}
sudo apt-get install git-core #install git(required)
bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )
#install rvm
{% endhighlight %}

After the installation is complete, RVM will tell you to add a line to your .bashrc file which is in your home directory /home/username/.bashrc

# This loads RVM into a shell session.
{% highlight bash %}
[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"
{% endhighlight %}

Restart your terminal session and you should have rvm command available for use.
Remember that the original version of the ruby installed in your system will still be available


Commands to get started
{% highlight bash %}
rvm install 1.9.2 #install ruby 1.9.2
rvm list #list versions of rubies installed by rvm
rvm use 1.9.2 #use ruby 1.9.2
ruby -v #confirm the version of ruby in use
rvm system #switch back to system version of ruby
rvm use 1.9.2 --default #to set ruby 1.9.2 as default
{% endhighlight %}
Read the basics at [http://rvm.beginrescueend.com/rvm/basics/](http://rvm.beginrescueend.com/rvm/basics/)  
Best Practices at [http://rvm.beginrescueend.com/rvm/best-practices/](http://rvm.beginrescueend.com/rvm/best-practices/)

After the installation of Ruby the first things that comes to mind is how do install gems for specific versions of rubies, RVM handles that for us as well.

Install Gems for selected version of Ruby
{% highlight bash %}
rvm use 1.9.2 #Select the version of ruby to use
gem install rails
gem install mysql2
gem install unicorn 
{% endhighlight %}
Note: It is recommended to not use the 'sudo' command to install gems when you are using rvm and ruby.

{% highlight bash %}
rails new testapp #generate a new rails 3 app
rails g sacffold article title:string description:text published:boolean #generate a scaffold

{% endhighlight %}

Edit your Gemfile
{% highlight ruby %}
gem 'rails', '3.0.3'
gem 'unicorn'
gem 'mysql2'
gem 'paperclip'

{% endhighlight %}

{% highlight ruby %}
bundle install
unicorn_rails -p 3000
{% endhighlight %}

Open a browser and goto localhost:3000/articles  

When I first did this I got an error

{% highlight bash %}
LoadError: no such file to load openssl
{% endhighlight %}
After googling around I found the fix  
Check that you have openssl and libssl installed on your machine

If not install it with
{% highlight bash %}
sudo apt-get install openssl libssl
{% endhighlight %}

If that does not fix it then goto /home/username/.rvm/src/ruby-1.9.2-p0/ext/openssl
{% highlight bash %}
ruby extconf.rb
make
make install
{% endhighlight %}

This should fix the error.

Everything above was tested on ubuntu 10.10

#### Update ####
IRB readline issue

To work with irb you need to install libreadline
{% highlight bash %}
sudo apt-get install libreadline5-dev
{% endhighlight %}

Then
{% highlight bash %}
rvm package install readline
cd $HOME/.rvm/src/ruby-1.9.2-p0/ext/readline
ruby extconf.rb -- --with-readline-dir="$HOME/.rvm/usr"
make install
{% endhighlight %}
