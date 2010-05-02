!SLIDE
# Previously on the Ruby Meetup... #
	06/2009: Hárdi János - JRuby on Appengine
	http://to./3nv9

!SLIDE bullets
# 1 - 2 - 3 #
	* (sudo) gem install google-appengine
	* appcfg.rb generate_app helloworld
	* dev_appserver.rb helloworld

!SLIDE smaller
# Gemfile #
	@@@ ruby
	# Critical default settings:
	disable_system_gems
	disable_rubygems
	bundle_path ".gems/bundler_gems"

	# List gems to bundle here:
	gem "appengine-rack"
	gem "appengine-apis"
	gem "sinatra"

	
!SLIDE smaller
# config.ru #
	@@@ ruby
	require 'appengine-rack'
	AppEngine::Rack.configure_app(          
	    :application => "ireketmondunk",           
	    :precompilation_enabled => true,
	    :version => "1")
	require 'irishpeople'
	run Sinatra::Application

!SLIDE smaller
# cron.yaml #
	@@@ yaml
	cron:
	 - description: deli irek
	   url: /update
	   schedule: every day 12:00
	   timezone: Europe/Budapest
	 - description: esti irek
	   url: /update
	   schedule: every day 18:30
	   timezone: Europe/Budapest
	 - description: keepalive
	   url: /ping
	   schedule: every 1 minutes

!SLIDE smaller
# irishpeople.rb #
	@@@ ruby
	%w(sinatra base64 yaml).each {|l| require l}

	get '/' { redirect 'http://twitter.com/ireketmondunk' }
	get '/ping' { "pong" }

	get '/update' do
	  @irek = YAML::load(File.open('WEB-INF/irek.yaml'))
	  @auth = YAML::load(File.open('WEB-INF/auth.yaml'))
	  url = URI.parse("http://twitter.com/statuses/update.xml")
	  http = AppEngine::URLFetch::HTTP.new(url.host, url.port)
	  headers = { 
	    "Content-Type" => "application/x-www-form-urlencoded", 
	    "Authorization" => "Basic #{Base64::encode64(@auth['user'] + ':' + @auth['pass'])}"
	  }
	  http.post(url.path, "status=#{@irek[rand(@irek.size)]}", headers)
	  "yay" # we need to return something
	end

!SLIDE smaller code
	├── bin
	│   └── rackup
	├── config.ru
	├── cron.yaml
	├── Gemfile
	├── irishpeople.rb
	├── public
	│   ├── favicon.ico
	│   └── robots.txt
	├── README
	└── WEB-INF
	    ├── appengine-generated
	    │   └── build_status.yaml
	    ├── appengine-web.xml
	    ├── auth.sample.yaml
	    ├── cron.xml
	    ├── irek.yaml
	    ├── lib
	    │   └── *.jar
	    └── web.xml

!SLIDE bullets incremental
# + #
* Datamapper
* Rails
* ...

!SLIDE
# Deployment #
	appcfg.rb update .

!SLIDE
# Over and out #
* http://code.google.com/p/appengine-jruby/
* http://github.com/ktamas/ireketmondunk
* http://twitter.com/ireketmondunk
* http://ktamas.blog.hu/
