[![Build Status](https://travis-ci.org/palkan/anyway_config.svg?branch=master)](https://travis-ci.org/palkan/anyway_config)

# Anyway Config

Rails plugin/application configuration using any source: YAML, _secrets_, environment.
Requires Rails 4.

Apps using Anyway Config: 
- [influxer](https://github.com/palkan/influxer).

## Using with Gem

Configure your gemspec

```ruby
Gem::Specification.new do |s|
  ...
  s.add_dependancy 'anyway_config', "~>0.1"
  ...
end
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install anyway_config

### Usage

Create configuration class:

```ruby
require 'anyway'
module MyCoolGem
  class Config < Anyway::Config
    attr_config user: 'root', password: 'root', host: 'localhost'
  end
end
```

`attr_config` creates accessors and default values. If you don't need default values just write:

```ruby
attr_config :user, :password, host: 'localhost'
```

Your config will be filled up with values from `RAILS_ROOT/config/my_cool_gem.yml`, `Rails.application.secrets.my_cool_gem` (if using Rails) and `ENV['MYCOOLGEM_*']`.  

### Customize name

If you want to load config params from, for example, "cool.yml" (secrets, env), just add one line:

```ruby
module MyCoolGem
  class Config < Anyway::Config
    config_name :cool
    attr_config user: 'root', password: 'root', host: 'localhost', options: {}
  end
end
```

### Config clear and reload

You can use `clear` and `reload` functions on your config (which do exactly what they state).


## Using with Rails app

In your Gemfile

```ruby
require 'anyway_config', "~>0.2"
```

In your code

```ruby

config = Anyway::Config.for(:my_app) # load data from config/my_app.yml, secrets.my_app, ENV["MYAPP_*"]

```

## Differences between `Rails.application.config_for` and `Anyway::Config.for`

Rails 4.2 introduces new feature: `Rails.application.config_for`. It looks very similar to 
`Anyway::Config.for`, but there are some differences:

| Feature       | Rails         | Anyway  |
| ------------- |:-------------:| -----:|
| load data from `config/app.yml`     | yes | yes |
| load data from `secrets`      | no      |   yes |
| load data from environment | no   |   yes |
| return Hash with indifferent access | no | yes | 
| support ERB within `config/app.yml` | yes | no |
| raise errors if file doesn't exist | yes | no |

But the main advantage of Anyway::Config is that it's supported in Rails >= 3.2, Ruby >= 1.9.3.

## How to set env vars

Environmental variables for your config should start with your module name (or config name if any), uppercased and underscore-free.

For example, if your module is called "MyCoolGem" then your env var "MYCOOLGEM_PASSWORD" is used as `config.password`.

*Anyway Config* supports nested (_hashed_) environmental variables. Just separate keys with double-underscore.
For example, "MYCOOLGEM_OPTIONS__VERBOSE" is transformed to `config.options.verbose`.


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request