<img src=stan-logo.png width=50% />

* [Installation](#installation)
* [Usage](#usage)
  + [Server](#server)
    - [Example Nginx configuration](#example-nginx-configuration)
  + [Client](#client)
* [Development](#development)
* [Contributing](#contributing)
* [License](#license)

Stan is a little tool that helps you to deploy static sites to a centralized host.

## Installation
Install Stan with

    $ gem install stan

In order to build the dependencies please make sure you installed the ruby headers.

    $ apt install ruby-dev

## Usage

```
Commands:
  stan compress DIRECTORY     # compress given directory
  stan deploy DIRECTORY NAME  # deploys given directory to stan server
  stan help [COMMAND]         # Describe available commands or one specific command
  stan server                 # start the server to receive and serve pages
  stan version                # display the stan version
```

There are a few variables you have to set:

### Server

* `STAN_UPLOAD_DIR=/tmp/stan/upload` is the directory where Stan will temporarly store sites before deploying them.
* `STAN_PUBLIC_DIR=/srv/stan` is the directory where the final site will be deployed.
* `STAN_BIND=127.0.0.1` is the ip to bind to.
* `STAN_PORT=4567` is the port to use.

Please note that Stan will create a directory for each deployed site within that folder.
The final site will then be deployed to `/srv/stan/my-site` for example.

#### Example Nginx configuration

```
upstream pages {
  server localhost:4567 fail_timeout=0;
}

server {
  listen 443;
  listen [::]:443;

  # replace with server name config and ssl settings

  client_max_body_size 100m;

  location / {
    root /srv/stan;
  }

  location /upload {
    proxy_read_timeout      300;
    proxy_connect_timeout   300;
    proxy_redirect          off;

    proxy_http_version 1.1;

    proxy_set_header    Host                $host;
    proxy_set_header    X-Real-IP           $remote_addr;
    proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for;
    proxy_set_header    X-Forwarded-Proto   https;

    proxy_pass http://pages/upload;
  }
}

```

### Client

* `STAN_SERVER=pages.example.com` is the URL of the remote Stan server which must expose the `/upload` route. Define ports like usual.
* `STAN_TEMP_DIR=/tmp/stan` is the directory where Stan will store the site after compressing it. The archive will be removed after upload.


## Development

After checking out the repo, run `bin/setup` to install dependencies. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/Flipez/static-stan.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

Look at the awesome logo I made at: <a href='https://onlinelogomaker.com' title='Online Logo Maker'>onlinelogomaker.com</a>
