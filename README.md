[hub]: https://hub.docker.com/r/spritsail/plex-media-server
[git]: https://github.com/spritsail/plex-media-server/tree/nofonts
[drone]: https://drone.spritsail.io/spritsail/plex-media-server
[mbdg]: https://microbadger.com/images/spritsail/plex-media-server:nofonts

# [spritsail/plex-media-server][hub]

[![Layers](https://images.microbadger.com/badges/image/spritsail/plex-media-server:nofonts.svg)][mbdg]
[![Latest Version](https://images.microbadger.com/badges/version/spritsail/plex-media-server:nofonts.svg)][hub]
[![Git Commit](https://images.microbadger.com/badges/commit/spritsail/plex-media-server:nofonts.svg)][git]
[![Docker Pulls](https://img.shields.io/docker/pulls/spritsail/plex-media-server.svg)][hub]
[![Docker Stars](https://img.shields.io/docker/stars/spritsail/plex-media-server.svg)][hub]
[![Build Status](https://drone.spritsail.io/api/badges/spritsail/plex-media-server/status.svg?branch=nofonts)][drone]
[![Last Build](https://api.spritsail.io/badge/lastbuild/spritsail/plex-media-server:nofonts)][drone]

The _smallest*_ Plex Media Server docker image, built on barebones [spritsail/busybox](https://hub.docker.com/spritsail/busybox/) with glibc and libraries built from source. The container hosts a fully featured Plex Media Server, with almost all of the useless crap removed, resulting in the smallest container possible whilst maintaining full functionality.

You can find out more about the [spritsail/busybox](https://hub.docker.com/r/spritsail/busybox) base image [here](https://github.com/spritsail/busybox)

_*last we checked_

**NOTICE:** This build has changed the `/config/Plex Media Server` mountpoint inside the container to now be present at `/config`. If you previously used this container, please update your mountpoint to `/config`.

## Getting Started

Navigate to [plex.tv/claim](https://www.plex.tv/claim) and obtain a token in the form `claim-xxxx...`

Start the container, as demonstrated below, passing the claim token via the `PLEX_CLAIM` environment variable. This only has to be present on the first run (when the configuration is generated) and can be removed for subsequent runs. _The Plex claim token is optional however it will make the server available to your account immediately._

Setting the container hostname on first boot will set the Plex server name.

```shell
docker run -dt \
    --name=plex \
    --restart=unless-stopped \
    --hostname=my-plex-server \
    -p 32400:32400 \
    -e PLEX_CLAIM=claim-xxxx... \
    -v /config/plex:/config \
    -v /transcode:/transcode \
    -v /media:/media \
    spritsail/plex-media-server:nofonts
```

Finally, navigate to [app.plex.tv/desktop](https://app.plex.tv/desktop) or [your-ip:32400/web](http://localhost:32400/web) and you're done!


### Volumes

- `/config` - Configuration, logs, caches and other Plex crap. You should keep this
- `/transcode` - Transcoder temporary directory. This should be backed by fast storage, ideally tmpfs/RAM.
- Don't forget to mount your media (tv-shows/movies) inside the container too!

### Environment

- `$SUID`                 - User ID to run as _default: 900_
- `$SGID`                 - Group ID to run as _default: 900_
- `$ALLOWED_NETWORKS`     - IP/netmask entries which allow access to the server without requiring authorization. We recommend you set this only if you do not sign in your server. For example `192.168.1.0/24,172.16.0.0/16` will allow access to the entire `192.168.1.x` range and the `172.16.x.x` range.
- `$ADVERTISE_IP`         - This variable defines the additional IPs on which the server may be be found. For example: `http://10.1.1.23:32400`. This adds to the list where the server advertises that it can be found.
- `$DISABLE_REMOTE_SEC`   - 
- `$PLEX_CLAIM`           - The claim token for the server to obtain a real server token. If not provided, server will not be automatically logged in. If server is already logged in, this parameter is ignored.

### Network

The following ports are all used by Plex for various applications

- `32400/tcp`       Plex Web/Client Access 
- `5353/udp`        Bonjour/Avahi
- `3005/tcp`        Plex Home Theatre via Plex Companion
- `8324/tcp`        Plex for Roku via Plex Companion
- `1900/udp`        Plex DLNA Server
- `32469/udp`       Plex DLNA Server
- `32410/udp`       GDM network discovery
- `32412/udp`       GDM network discovery
- `32413/udp`       GDM network discovery
- `32414/udp`       GDM network discovery

See also: https://support.plex.tv/articles/201543147-what-network-ports-do-i-need-to-allow-through-my-firewall/

At the very least, you should expose `32400/tcp` to your network, and _port forward_ it through your router if you would like Plex access outside your home network.

If you wish, you can map the Plex port to any other port outside your network, just be sure to update the port in _Settings > Server > Remote Access_ (Show Advanced) under _Manually specify public port_.
