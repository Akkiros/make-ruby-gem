# Make Ruby Gem

Tutorial for people who want to make ruby gem.

## Why gem?

When you developing Ruby or Rails applications, you want public your codes for developer who makes same features like you. So you makes your codes like libarary. Yes, it's a gem.

## Environment

- ruby 2.2.3
- rails 4.1.8

## Initialize

First, choose your gem name and make initial gem with bundle command. In this time i'll make gem named "Malygos"

```ruby
bundle gem malygos
```

This command makes basic gem structure.

```
malygos
├── Gemfile
├── Rakefile
├── malygos.gemspec
├── lib
│   └── malygos.rb
└── malygos
    └── version.rb
```

- Gemfile: Write your gems dependencies.
- Rakefile: TODO
- malygos.gemspec: your gem informations like author, summary.
- lib/malygos.rb : When you ``require 'malygos'``, start with this file.
- lib/malygos/version.rb: define this gem version.

Before build our gem, set gem information in ``malygos.gemspec``  
when you open this file, description and summary are set TODO: ~~~ so you **must rewrite** these.

```ruby
# malygos.gemspec
....
Gem::Specification.new do |spec|
  spec.name          = "malygos"
  spec.version       = Malygos::VERSION
  spec.authors       = ["YOUR_NAME"]
  spec.email         = "YOUR_EMAIL"
  spec.summary       = ""
  spec.description   = "A simple hello world gem"
  spec.homepage      = ""
  spec.license       = "MIT"
  
  spec.files         = "git ls-files -z".split("\x0")
  spec.executables   = spec.files.grep(%r{^bin/}) { |f| File.basename(f) }
  spec.test_files    = spec.files.grep(%r{^(test|spec|features)/})
  spec.require_paths = ["lib"]
 
  spec.add_development_dependency "bundler", "~> 1.6"
  spec.add_development_dependency "rake"
  spec.add_development_dependency "rspec"
  spec.add_development_dependency "jeweler"
end
```

Now we build our gem.

```sh
gem build malygos.gemspec
```

now we run build and install command.

```sh
rake build
rake install
```

if you want to publish your gem, run below command.

```sh
gem push malygos.0.0.1.gem
```

## Add some features

Before add some features, we change some structures.  
Make ``lib/malygos/malygos.rb`` and ``lib/malygos/railtie.rb``  
In ``lib/malygos/malygos.rb`` we make some features and in ``lib/malygos/railtie.rb`` load our tasks.

So change some codes ``lib/malygos.rb``

```ruby
# lib/malygos.rb
requrie 'malygos/version'
require 'malygos/malygos'
require 'malygos/railtie' if defined?(Rails)
```

And add your codes in ``lib/malygos/malygos.rb``

```ruby
# lib/malygos/malygos.rb
module Malygos
  class << self
    def get_local_assets
      local_files = []
      path = "#{Rails.root}/public/**/*"
      
      Dir.glob(path) do |fname|
        local_files << fname.sub("#{Rails.root}/", "") if File.file?(fname)
      end
      
      local_files
    end
  end
end
```

For example, get\_local\_assets method is a part of [Malygos] gem. This method is return local assets array in rails project.  
This method using Rails, so if you want to use method in ruby project define another method like this.

```ruby
# lib/malygos/malygos.rb
def random
  Random.rand(10)
end
```

So now we make gem method. Build and install our gem.

```sh
rake build
rake install
```

and run by irb

```ruby
irb > require 'malygos'
=> true
irb > Malygos.random
=> 3
```

## Install custom gem in rails project

Now we make our custom gem, so we add our gem in rails project.

First, add your gem in ``Gemfile``

```ruby
# Gemfile
gem 'malygos', path: '/path/to/your/gem'
```

If you publish your gem, you don't need path.

And if you want to check your gem install successful, run ``rails console``

```ruby
rails c
> Malygos.get_local_assets
> ["public/favicon.ico", ...] # your local assets array
```

## Add rails task

Sometimes ruby gems do task by user or schedule. So we made ``railtie.rb`` before.  
This time we make rails task and load task using ``railtie.rb``.

First, make task. make ``lib/tasks/malygos.rake`` file and open.

```ruby
# lib/tasks/malygos.rake
namespace :malygos do
  desc "Get local assets list"
  task :list do
    puts Malygos.get_local_assets
  end
end
```
***Tips***

> If you don't write desc, when you run ``rake -T`` not display your task. So i recommand that write taks's desc.

And open ``lib/malygos/railtie.rb`` to load rake file.

```ruby
# lib/malygos/railtie.rb
require 'malygos'
require 'rails'

module Malygos
  class Railtie < Rails::Railtie
    rake_tasks do
      load "tasks/malygos.rake"
    end
  end
end
```

Finally we add our tasks use on rails project!  
In your rails project, run command ``rake -T`` show our task like ``rake malygos:list``.

Run ``rake malygos:list``, run list task in malygos gem.

```sh
rake malygos:list
public/favicon.ico
...
```

## Feedback

If you doing same this tutorial but not working, please message to my account below facebook. And if you have good information or tutorial for reader, please pull request! lol

## Author

Wonjae Kim / [@Akkiros][Facebook]

[Malygos]: https://github.com/Akkiros/Malygos
[Facebook]: https://www.facebook.com/akkiros