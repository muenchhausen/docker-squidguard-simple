# Introduction

this image is an [squidGuard](http://www.squidguard.org/) addition to [sameersbn/docker-squid](https://github.com/sameersbn/docker-squid). 
I find squidGuard very useful to limit access to certain internet pages and to reduce the risk for downloading dangerous software. A central filtering solution is preferred especially if you have a family with children and different devices.

**Important: This Docker image allows just local configuration within squidGuard.conf. If you want to use whitelists and blacklists from external sources, please use my other docker image [muenchhausen/docker-squidguard](https://hub.docker.com/r/muenchhausen/docker-squidguard/) and [source](https://github.com/muenchhausen/docker-squidguard) instead.**

This image includes also automatic proxy discovery based on WPAT and DHCP. Here a Webserver is required that serves wpat.dat.

# Installation

Pull the image from the docker registry e.g.

```docker pull muenchhausen/docker-squidguard-simple```

or build it

```git clone https://github.com/muenchhausen/docker-squidguard-simple.git```

```cd docker-squidguard-simple```

```docker build --tag="$USER/squidguard-simple" .```

run your build:
```docker run --name='squidguard-simple' -it --rm -p 3128:3128 "$USER/squidguard-simple" ```

Please refer to [sameersbn/docker-squid](https://github.com/sameersbn/docker-squid) for details!

# Quick Start

Run the downloaded image

```docker run --name='squidguard-simple' -it --rm -p 3128:3128 muenchhausen/docker-squidguard-simple:latest```

or as daemon

```docker run -d --name='squidguard-simple' -it -p 3128:3128 muenchhausen/docker-squidguard-simple:latest```

or run it including WPAT proxy autoconfig so your Operating system will find the settings automatically based on your DHCP settings:

```docker run --name='squidguard-simple' -it --env WPAT_IP=192.168.59.103 --env WPAT_NOPROXY_NET=192.168.59.0 --env WPAT_NOPROXY_MASK=255.255.255.0 --rm -p 3128:3128 -p 80:80 muenchhausen/docker-squidguard-simple:latest```

To use WPAT, add a cusom-proxy-server option 252 to your DHCP server. Use "http://${WPAT_IP}/wpat.dat" e.g. "http://192.168.59.103/wpat.dat" as your option value. See [squidGuard Wiki](http://wiki.squid-cache.org/SquidFaq/ConfiguringBrowsers#Automatic_WPAD_with_DHCP) for further details.

# Test it 
In this config, we just allow Wikipedia and some other Domains.

here curl should return the page:
```curl --proxy 192.168.59.103:3128 https://en.wikipedia.org/wiki/Main_Page```

here curl gets blocked:
```curl --proxy 192.168.59.103:3128 http://www.heise.de```

Finally configure docker host IP and port 3128 in your browser proxy settings or operating system proxy configuration.

Or - if you decided for the WPAT autoproxy variant, just do now a DHCP release and you get your proxy settings :)


# Configuration

For Squid basis configuration, please refer to the documentation of [sameersbn/docker-squid](https://github.com/sameersbn/docker-squid).

The central configuration file of squidGuard is `squidGuard.conf`. You can customize it either by building your own docker image or by specifying the `-v /path/on/host/to/squidGuard.conf:/etc/squidguard/squidGuard.conf` flag in the docker run command. A simple documentation of how to configure squidGuard blacklists can be found in the [squidGuard configuration documentation](http://www.squidguard.org/Doc/configure.html).

Most frequently used configuration files are
- /etc/squidguard/squidGuard.conf
- /var/lib/squidguard/db/WL/whiteUrls
- /var/lib/squidguard/db/WL/whiteDomains
- /var/www/html/wpat.dat

# Shell Access


For debugging and maintenance purposes you may want access the containers shell. Either add after the run command or tun e.g.

```docker exec -it "$USER/squidguard-simple" bash  ```

or
```docker ps```
```docker exec -it <container-id> bash   ```

# Autostart the container

add the parameter --restart=always to your docker run command.
