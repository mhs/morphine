# Morphine

Morphine is a lightweight dependency injection framework for Ruby. It uses a simple Ruby DSL to ease the pain of wiring your dependencies together.

## Usage

Create a container for your dependencies and include the `Morphine` module.

    ```ruby
    class Application
      include Morphine

      register :track_service do
        KestrelTrackService.new(kestrel_client, config.tracking_queue)
      end

      register :track_processor do
        KestrelTrackProcessor.new(blocking_kestrel_client, config.tracking_queue)
      end

    private

      register :kestrel_client do
        c = config['kestrel'].dup
        Kestrel::Client.new(c.delete('servers'), c.symbolize_keys)
      end

      register :blocking_kestrel_client do
        Kestrel::Client::Blocking.new(kestrel_client)
      end
    end
    ```

Create an instance of your container, and use that to load your dependencies

    ```ruby
    $app = Application.new

    get '/track.gif' do
      $app.track_processor.record(params['h'])
    end
    ```

# But I don't need dependency injection in Ruby!

Many [argue](http://weblog.jamisbuck.org/2008/11/9/legos-play-doh-and-programming) that you [don't](http://davybrion.com/blog/2010/10/why-you-dont-need-dependency-injection-in-ruby/) [need](http://fabiokung.com/2010/05/06/ruby-and-dependency-injection-in-a-dynamic-world/) dependency injection in dynamic languages like Ruby. What they are really saying is you don't need a complicated dependency injection framework, and *they're right*.

That's why Morphine is an extremely simple library.