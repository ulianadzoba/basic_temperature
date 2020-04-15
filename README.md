## Basic Temperature ##

[![Gem Version](https://badge.fury.io/rb/basic_temperature.svg)](https://rubygems.org/gems/basic_temperature) [![Build Status](https://travis-ci.com/marian13/basic_temperature.svg?branch=master)](https://travis-ci.com/marian13/basic_temperature) [![Maintainability](https://api.codeclimate.com/v1/badges/21dc5d50cf5de8346a3c/maintainability)](https://codeclimate.com/github/marian13/basic_temperature/maintainability) [![Coverage Status](https://coveralls.io/repos/github/marian13/basic_temperature/badge.svg)](https://coveralls.io/github/marian13/basic_temperature) [![Inline docs](http://inch-ci.org/github/marian13/basic_temperature.svg?branch=master)](http://inch-ci.org/github/marian13/basic_temperature)
[![Patreon](https://img.shields.io/badge/patreon-donate-orange.svg)](https://www.patreon.com/user?u=31435716&fan_landing=true)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

![alt text](https://raw.githubusercontent.com/marian13/basic_temperature/master/logo.png)

`basic_temperature` is a Ruby library which provides a simple [value object](https://martinfowler.com/bliki/ValueObject.html) to work with temperatures and 
allows to perform basic operations like conversion from Celsius to Kelvin, from Kelvin to Fahrenheit etc.


### Features
- Provides a `BasicTemperature` class which encapsulates all information about a certain  temperature, such
as its amount of degrees and its scale.
- Provides APIs for exchanging temperatures from one scale to another (currently Celsius, Fahrenheit, Kelvin and
Rankine).

- Allows comparing temperatures between each other.
- Supports basic math operations like addition and subtraction.
- Tested against Ruby 2.3, 2.4, 2.5, 2.6 & 2.7. See
[.travis-ci.yml](https://github.com/marian13/basic_temperature/blob/9b13cb9909b57c51bb5dc05a8989d07a314e67d6/.travis.yml)
for the exact versions.

### Dependecies

* None.

### Documentation

* Visit https://marian13.github.io/basic_temperature/ to view the documentation.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'basic_temperature'
```

#### Warning

Since this gem is under active development and there are often backward-incompatible changes between minor releases, you might want to use a conservative version lock in your Gemfile:

```ruby
gem 'basic_temperature', '~> 0.2.1'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install basic_temperature

## Usage

### Creating Temperatures

A new temperature can be created in multiple ways:

- Using keyword arguments:

```ruby
Temperature.new(degrees: 0, scale: :celsius)
```

- Using positional arguments:

```ruby
Temperature.new(0, :celsius)
```

- Even more concise way using `Temperature.[]` (an alias of `Temperature.new`):

```ruby
Temperature[0, :celsius]
```

### Creating Temperatures from already existing temperature objects

Sometimes it is useful to create a new temperature from already existing one.

For such cases, there are `set_degrees` and `set_scale`.

Since temperatures are [value objects](https://martinfowler.com/bliki/ValueObject.html), both methods returns
new instances.

Examples:

```ruby
temperature = Temperature[0, :celsius]
# => 0 °C

new_temperature = temperature.set_degrees(15)
# => 15 °C

temperature = Temperature[0, :celsius]
# => 0 °C

new_temperature = temperature.set_scale(:kelvin)
# => 0 K
```

### Conversions

Temperatures can be converted to diffirent scales.

Currently, the following scales are supported: `Celsius`, `Fahrenheit`, `Kelvin` and `Rankine`.

```ruby
Temperature[20, :celsius].to_celsius
# => 20 °C

Temperature[20, :celsius].to_fahrenheit
# => 68 °F

Temperature[20, :celsius].to_kelvin
# => 293.15 K

Temperature[20, :celsius].to_rankine
# => 527.67 °R
```

If it is necessary to convert scale dynamically, `to_scale` method is available.

```ruby
Temperature[20, :celsius].to_scale(scale)
```

All conversion formulas are taken from
[RapidTables](https://www.rapidtables.com/convert/temperature/index.html).

Conversion precision: 2 accurate digits after the decimal dot.

### Comparison

Temperature implements idiomatic [<=> spaceship operator](https://ruby-doc.org/core/Comparable.html) and mixes in [Comparable](https://ruby-doc.org/core/Comparable.html) module.

As a result, all methods from [Comparable](https://ruby-doc.org/core/Comparable.html) are available, e.g:

```ruby
Temperature[20, :celsius] < Temperature[25, :celsius]
# => true

Temperature[20, :celsius] <= Temperature[25, :celsius]
# => true

Temperature[20, :celsius] == Temperature[25, :celsius]
# => false

Temperature[20, :celsius] > Temperature[25, :celsius]
# => false

Temperature[20, :celsius] >= Temperature[25, :celsius]
# => false

Temperature[20, :celsius].between?(Temperature[15, :celsius], Temperature[25, :celsius])
# => true

# Starting from Ruby 2.4.6
Temperature[20, :celsius].clamp(Temperature[20, :celsius], Temperature[25, :celsius])
# => 20 °C
```

Please note, if the second temperature has a different scale, the first temperature is automatically
converted to that scale before comparison.

```ruby
Temperature[20, :celsius] == Temperature[293.15, :kelvin]
# => true
```

#### IMPORTANT !!!

`degrees` are rounded to the nearest value with a precision of 2 decimal digits before comparison.

This means the following temperatures are considered as equal:

```ruby
Temperature[20.020, :celsius] == Temperature[20.024, :celsius]
# => true

Temperature[20.025, :celsius] == Temperature[20.029, :celsius]
# => true
```

while these ones are treated as NOT equal:

```ruby
Temperature[20.024, :celsius] == Temperature[20.029, :celsius]
# => false
```

### Math

#### Addition/Subtraction.

```ruby
Temperature[20, :celsius] + Temperature[10, :celsius]
# => 30 °C

Temperature[20, :celsius] - Temperature[10, :celsius]
# => 10 °C
```

If second temperature has a different scale, first temperature is automatically converted to that scale
before `degrees` addition/subtraction.

```ruby
Temperature[283.15, :kelvin] + Temperature[10, :celsius]
# => 10 °C
```

Returned temperature will have the same scale as the second temperature.

It is possible to add/subtract numerics.

```ruby
Temperature[20, :celsius] + 10
# => 30 °C

Temperature[20, :celsius] - 10
# => 10 °C
```

In such cases, returned temperature will have the same scale as the first temperature.

Also [Ruby coersion mechanism](https://ruby-doc.org/core/Numeric.html#method-i-coerce) is supported.

```ruby
10 + Temperature[20, :celsius]
# => 30 °C

10 - Temperature[20, :celsius]
# => -10 °C
```

#### Negation

```ruby
-Temperature[20, :celsius]
# => -20 °C
```

### Queries
```ruby
Temperature[0, :celsius].boil_water?
# => false

Temperature[0, :celsius].freeze_water?
# => true
```

## Versioning
Basic Temperature follows the [Semantic Versioning](https://semver.org/) standard.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/marian13/basic_temperature.

## Development

* Check specs: `bundle exec rspec`.

* Check linter: `bundle exec rubocop`.

* Update docs: `bundle exec sdoc lib -T rails -o docs`.

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

Copyright (c) 2020 [Marian Kostyk](http://mariankostyk.com).
