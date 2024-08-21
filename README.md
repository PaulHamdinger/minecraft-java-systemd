# Linux systemd files for a Java Minecraft Server

A collection of systemd files to run a Java Minecraft server on a Linux PC.

Bring the server up only when traffic is detected. Automatically shut it down when no one is playing.

Use a "FIFO" named-pipe to send commands to "stdin" of the running server.

## How to use

After customizing the files, start everything with...

`sudo systemctl enable --now minecraft-proxy.socket`

The other .socket and two .services files will be activated on-demand, and shouldn't need to be manually enabled or started.

In Minecraft, try connecting to `localhost:25566` or `127.0.0.1:25566`.

The Minecraft server will start up in the background as soon as someone connects to port **25566** (which gets proxied to the server's native port **25565**).

**Send messages to the server** like so...

`echo 'list' > /run/minecraft.FIFO`

Watch server output with `journalctl -f -u minecraft.service`.

## Extra notes...

The .socket and .service files for this setup are a little tricky. Hopefully these can serve as guideposts for someone else struggling to make it work.

The Minecraft server doesn't naturally support systemd's method of handing off activated ports to services. So it's *unfortunately* necessary to use systemd's `systemd-socket-proxyd` command as a work-around. This is why we have the dumb thing where clients must connect to port *25566*, while the server is actually listening on port *25565*. If you're sufficiently comfortable with what you're doing you can use any ports you want, of course.

In systemd's world, `.socket` and `.service` files should share the same base filename. When you do this, activity on the socket automatically kicks off the service. In our case, this is exactly the behaviour we want for `minecraft-proxy.socket` and `minecraft-proxy.service`.

(in the case of `minecraft.service` and `minecraft.socket`, both get launched by dependencies, and so they don't actually take advantage of the shared-base-filename mechanic)

If you see room for improvement, leave a comment under Issues.
