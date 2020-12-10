# Image Preloading

On boot, BurmillaOS scans `/var/lib/burmilla/preload/docker` and `/var/lib/burmilla/preload/system-docker` directories and tries to load container image archives it finds there, with `docker load` and `system-docker load`.

The archives are `.tar` files, optionally compressed with `xz` or `gzip`. These can be produced by `docker save` command, e.g.:

```
$ docker save my-image1 my-image2 some-other/image3 | xz > my-images.tar.xz
```

The resulting files should be placed into `/var/lib/burmilla/preload/docker` or `/var/lib/burmilla/preload/system-docker` (depending on whether you want it preloaded into Docker or System Docker).

Pre-loading process only reads each new archive once, so it won't take time on subsequent boots (`<archive>.done` files are created to mark the read archives). If you update the archive (place a newer archive with the same name) it'll get read on the next boot as well.

Pre-loading process is `asynchronous` by default, optionally this can be set to `synchronous` through the cloud-config file or `ros config set` command. In the following example, we’ll use the cloud-config file and `ros config set` command to set BurmillaOS pre-loading process to `synchronous`.

_Available as of v1.4_

cloud-config file, e.g.:
```
#cloud-config
burmilla:
  preload_wait: true
```

`ros config set` command, e.g.:
```
$ ros config set burmilla.preload_wait true
```

Pre-packing docker images is handy when you're customizing your BurmillaOS distribution (perhaps, building cloud VM images for your infrastructure).
