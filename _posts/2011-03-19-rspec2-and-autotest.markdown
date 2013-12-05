---
layout: post
title:  "RSpec2 and Autotest"
date:   2011-03-19
---

Autotest automatically runs your tests in background after a file in the project is saved, which lets you concentrate on the job at hand rather than manually running the tests every time you make a change.This approach helps in really speeding the development process, saving a lot of time.  
There are multiple ways to show the output apart from the terminal, one of which is the notification area in Ubuntu as shown in the image below.
Other than visual people use different sound notifications too.

### Notification Popup###
![alt pass](https://lh3.googleusercontent.com/-uceLKWAHAlY/TYSqwztjUuI/AAAAAAAAAF8/eexHHQ3AVws/s200/pass.png)
![alt fail](https://lh5.googleusercontent.com/-KtMl057wFZQ/TYSq-By8xLI/AAAAAAAAAGA/dZAQdgl1fZg/s200/fail.png)

### Setup ###

Use RVM  for managing different versions of Ruby on a system. Read this post on how to install it and get started.  
{% highlight bash %} 
#Use Ruby 1.9.2
rvm use 1.9.2
 
#Generate a new rails 3 application
rails new todo_list -T -J -d mysql
{% endhighlight %}

Install Rspec, Cucumber & Webrat
Gemfile
{% highlight ruby %}
group :test do
  gem 'webrat'
  gem 'cucumber'
  gem 'cucumber-rails'
  gem 'rspec'
  gem 'rspec-rails'
end
{% endhighlight %}

Paste in config/application.rb to load Rspec generator


{% highlight ruby %}
config.generators do |g|
  g.test_framework :rspec, :fixture => true
end
{% endhighlight %}
Install Rspec in the application

{% highlight bash %}
rails g rspec:install
{% endhighlight %}
Generate a scaffold


{% highlight bash %}
bundle install
 
rails g scaffold User email:string first_name:string last_name:string password_salt:string password_hash:string
 
rake db:create && rake db:migrate

{% endhighlight %}

### AutoTest ###
[AutoTest Getting Started Guide ](http://ph7spot.com/musings/getting-started-with-autotest)

Install ZenTest Gem

{% highlight bash %}
gem install ZenTest
{% endhighlight %}
Install Notification library for Ubuntu

{% highlight bash %}
sudo apt-get install libnotify-bin
{% endhighlight %}

Test libnotify
{% highlight bash %}
notify-send "Hello"
{% endhighlight %}
Now goto your home directory and create a .autotest file and paste the following code

Edit ~/.autotest
{% highlight ruby %}
Autotest.add_hook :initialize do |autotest|
  autotest.add_mapping(/^spec\/.*_spec\.rb$/) do |file|
    file
  end
 
  autotest.add_mapping(/^spec\/(controllers|helpers|lib|models|views)\/.*_spec\.rb$/) do|file|
    file
  end
 
end

module Autotest::Notify
  def self.notify(title, msg, img, pri='low', time=3000)
    `notify-send -i #{img} -u #{pri} -t #{time} '#{title}' '#{msg}'`
  end
 
  Autotest.add_hook :ran_command do |autotest|
    begin
      results = autotest.results
      output = results.last.gsub(/\e\[(\d+)m/,'')
      images = "~/Pictures/autotest_images"
      if output  =~ /[1-9]\d*\sfailures?/
        notify "Failure", "#{output}", "#{images}/fail.png", 'critical', 10000
      else
        notify "Passed", "#{output}", "#{images}/pass.png",'normal', 10000
      end
    rescue Exception => e
      p "#{e.message} \n #{e.backtrace}"
    end
  end
end
{% endhighlight %}
  
Run Autotest in your project root
{% highlight bash %}
autotest
{% endhighlight %}
[Download Source](https://github.com/anupnivargi/todo_list) for the above example.  
It uses an authentication example for explaining Rails3, Rspec2 and Cucumber working together.

[Download Autotest file](https://github.com/anupnivargi/dotfiles)
