# ActiveSpan

Current span management for OpenTracing in Ruby.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'activespan'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install activespan

## Usage

The library exposes a convenience `OpenTracing::Tracer` that automates managing current span.

It's a wrapper that forwards all call to another `OpenTracing::Tracer` implementation e.g. Lightstep, Jaeger etc.
Spans which you will create through this tracer will be automatically activated when started, and
deactivated when they finish.

```ruby
require 'activespan'

OpenTracing.global_tracer = ActiveSpan::Tracer.new(Jaeger::Client.build, ActiveSpan::ThreadLocalSpanSource.new)
```

To start a new active span, use the regular and known `start_span` method. If you use the default argument for `child_of` argument
then the currently active span becomes an implicit parent of a newly-started span. It means that you no longer have to 
pass the current span through the code.

When you finish the span, and if not yet deactivated, marks the end of the active period for the span. For `ThreadLocalSpanSource`
it means it's removed from the top of the stack.

```ruby
root_span = tracer.start_span("/GET users")
  db_span = tracer.start_span("GetUsers") # root_span became an implicit parent of db_span
  db_span.finish
root_span.finish
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/iaintshine/ruby-activespan. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

