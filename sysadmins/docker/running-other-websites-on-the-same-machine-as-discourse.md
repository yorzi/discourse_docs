---
title: Running other websites on the same machine as Discourse
---

<small class="documentation-source">Source: [https://meta.discourse.org/t/running-other-websites-on-the-same-machine-as-discourse/17247](https://meta.discourse.org/t/running-other-websites-on-the-same-machine-as-discourse/17247)</small>

If you want to run other websites on the same machine as Discourse, you need to set up an extra NGINX proxy in front of the Docker container.

If you have not already, please read the [Advanced Troubleshooting with Docker](https://meta.discourse.org/t/advanced-troubleshooting-with-docker/15927/) guide, as it covers the basics on the separation between host and container.

This guide **assumes you already have Discourse working** - if you don't, it may be hard to tell whether or not the configuration is working.

## Install nginx outside the container
First, make sure the container is not running:

    cd /var/discourse
    ./launcher stop app

Then install nginx from the package manager:

    sudo apt-get install nginx

## Change the container definition

This is where we change how Discourse actually gets set up. We don't want the container listening on ports - instead, we'll tell it to listen on a special file.

Change your app.yml to look like this:

```yml
# this is the base templates used, you can cut it down to include less functionality per container
templates:
  - "templates/cron.template.yml"
  - "templates/postgres.template.yml"
  - "templates/redis.template.yml"
  - "templates/sshd.template.yml"
  - "templates/web.template.yml"
  # - "templates/web.ssl.template.yml" # if HTTPS
  - "templates/web.ratelimited.template.yml"
  - "templates/web.socketed.template.yml" # <-- Added
# which ports to expose?
expose:
  - "2222:22" # If you don't need to use ./launcher ssh app, you can remove this too

```


## Create a NGINX 'site' for the outer nginx

Put this in `/etc/nginx/conf.d/discourse`, making sure to change the `server_name`:

```
server {
	listen 80;
	# change this
	server_name forum.riking.org;

	location / {
        proxy_pass http://unix:/var/discourse/shared/standalone/nginx.http.sock:;
		proxy_set_header Host $http_host;
		proxy_http_version 1.1;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	}
}
```

For a HTTPS site, base the file off of this instead: <a class="attachment" href="//discourse-meta.s3-us-west-1.amazonaws.com/original/3X/8/3/836737a0f45d64a24bc6a176431a9698a65c10b2.txt">discourse.conf.txt</a> (1.3 KB) 

Then, in a shell:

```bash
cd /etc/nginx/sites-enabled
sudo ln -s ../sites-available/discourse discourse

# test configuration
sudo nginx -t
# Important: If nginx -t comes back with an error, correct the config before reloading!
sudo service nginx reload
```

## Create your other sites

You're done with the Discourse section!

Make other NGINX "sites", then link and enable them, as in the last step above.
