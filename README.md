= prowly

Yet another Ruby interface to Prowl with command line utility

== Installation
	
	gem install prowly

== Usage as a Gem
	
Sending a notification via prowly gem

```ruby
	Prowly.notify do |n| # :notify is an alias of :add
	  n.apikey = "apikey" or n.apikeys = ["apikey_1", "apikey_2", "apikey_n"]
	  n.priority = Prowly::Notification::Priority::MODERATE
	  n.application = "Prowly"
	  n.event = "Notification"
	  n.description = "Your server is under attack!!!"
	  n.url = "http://www.myserver.com"
	end
	
	notification = Prowly::Notification.new(:apikey => "apikey", :application => "Prowly", :description => "Testing...")
	notification = Prowly::Notification.new(:apikeys => ["apikey_1", "apikey_2", "apikey_n"], :application => "Prowly", :description => "Testing...")
	
	result = Prowly.notify(notification)
```

Obtaining a user's API key with the prowly gem is a three step process:

- provider gets a token with their provider key via the retrieve_token call
- user approves the request at a special web URL (in their browser)
- provider gets user's API key with their provider key and the token via retrieve_apikey

This is somewhat similar to the OAuth scheme.

```
  # obtain a token first with your provider key first
  provider_key = "2b33d3f3f8522aaed4b2fc44382f1519791db6a2"
  token_result = Prowly.retrieve_token(provider_key)
  if token_result.succeeded?
    # if the call succeeded, the user will need to approve the request in their browser
    puts "go to this #{token_result.url} to approve this request"
    puts "then press <ENTER> to continue "
    dummy = STDIN.gets

    apikey_result = Prowly.retrieve_apikey(options[:provider], token_result.token)

    if apikey_result.succeeded?
      puts "The user apikey is #{apikey_result.apikey}"
    end
  end
```

== Usage in the command line

```bash	
	$prowly -h # help
		
	$prowly -k one_apikey -a "the application" -e "the event" -d "the description" -p NORMAL
	$prowly -k key_1,key_2,key_n -a "the application" -e "the event"
	
	Options:
	  -k, --api-key APIKEY             Prowl API Key
	  -d, --description DESCRIPTION    Description
	  -e, --event EVENT                Event
	  -p, --priority PRIORITY          VERY_LOW, MODERATE, NORMAL (default), HIGH, EMERGENCY
	  -u, --url                        URL for redirect (v1.2 feature)
	  -v, --version                    Print the version number and exit

	  If you only specify the apikey prowly will use the defaults: 

	  :description => "I am testing Prowly, and it works!"
	  :event => "Prowly notification"
	  :application => "Prowly"
	  :priority => NORMAL
	  :url => nil
```

== Handling the Prowl API response

```ruby
	
	ON SUCCESS
	result.status # => "success"
	result.code # => "200"

	if result.succeeded?
	  result.remaining # => "977"
	  result.resetdate # => "1266272588"
	else
	  result.message # => 
	end
	
	ON ERROR
	result.status # => "error"
	result.code # => "401" <= This depends on the error code sent by the prowl API

	if result.succeeded?
	  result.remaining # =>
	  result.resetdate # =>
	else
	  result.message # => "Invalid API Key(s)" <= This depends on the message sent by the prowl API
	end
```

== Note on Patches/Pull Requests
 
* Fork the project.
* Make your feature addition or bug fix.
* Add tests for it. This is important so I don't break it in a
  future version unintentionally.
* Commit, do not mess with rakefile, version, or history.
  (if you want to have your own version, that is fine but bump version in a commit by itself I can ignore when I pull)
* Send me a pull request. Bonus points for topic branches.

See LICENSE for details