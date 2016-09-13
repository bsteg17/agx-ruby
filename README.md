# agX Platform API Client

**(pre-alpha)** Ruby client for accessing SST Software's [agX Platform APIs](http://www.agxplatform.com/agx-apis/).

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'agx'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install agx

## Usage

### agX Content API

Setup agX Content Client (OAuth 2 Client Credentials Flow)
```ruby
@agx_content_client = Agx::Content::Client.new(
  client_id: "your_client_id",
  client_secret: "your_client_secret",
  site: "content-api-endpoint-url", # optional
  token_url: "auth-token-endpoint-url", # optional
  version: "v1"  # optional
)
```

Make get requests for Content API resources
```ruby
# @agx_content_client.get("ResourceName", params_hash)
# => { status: 'http status code', message: 'error message', body: 'parsed_json_object' }

crops = @agx_content_client.get("Crop")

# Passing in publishDate as param
weeds = @agx_content_client.get("Weed", {publishDate: date.to_s})
```

### agX Sync API

Setup agX Sync Client (OAuth 2 Authorization Code Flow)

***This requires that you have already previously authenticated and authorized
a user to agX through the authorization code grant flow process and have
persisted their sync ID, access token, refresh token, and access token
expiration timestamp.***

```ruby
@agx_sync_client = Agx::Sync::Client.new(
  client_id: "your_client_id",
  client_secret: "your_client_secret",
  site: "sync-api-endpoint-url", # optional
  host: "sync-api-endpoint-without-protocol", # optional
  authorize_url: "authorize-endpoint-url", # optional
  token_url: "auth-token-endpoint-url", # optional
  version: "v3",  # optional
  sync_id: "agx_user_sync_id",
  access_token: "agx_user_agx_token",
  refresh_token: "agx_user_refresh_token",
  token_expires_at: "access_token_expiration_timestamp",
  transaction_id: "agx_user_previous_transaction_id" # optional
)
```

Initiate a sync transaction, make get requests for Sync API resources, and end transaction
```ruby
# @agx_sync_client.get("Resource", start_time)
# => { status: 'http status code', message: 'error message', body: 'parsed_json_object' }

# You should persist transaction ID per user until transaction is successfully
# ended by call to end_transaction
transaction_id = @agx_sync_client.start_transaction

growers = @agx_sync_client.get("Grower")

# Get all farms accessible for a grower
farms = @agx_sync_client.get("Grower/#{grower.guid}/Farm")

# Get all server changes on farms accessible for a grower since start_time
farms = @agx_sync_client.get("Grower/#{grower.guid}/Farm", last_sync_date.to_s)

# etc...

@agx_sync_client.end_transaction

# clear the persisted transaction ID for user after ending sync transaction
user_transaction_id = nil
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment. To install this gem onto your local machine, run `bundle exec rake install`.

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/CropQuest/agx-ruby.


## Copyright

Copyright (c) 2016 [Crop Quest, Inc.](http://www.cropquest.com) See LICENSE.txt for details.

[agX](http://www.agxplatform.com/) is a registered trademark of [SST Software](http://www.sstsoftware.com/).
