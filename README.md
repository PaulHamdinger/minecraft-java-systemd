# systemd files for Java Minecraft Server

A collection of systemd files to run a Java Minecraft server on a Linux PC.

Bring the server up only when traffic is detected. Automatically shut it down when no one is playing.

Use a "FIFO" socket to send commands to the running server as it runs in the background.

## How to use

After customizing the files, run

`sudo systemctl enable --now minecraft-proxy.socket`

The other .socket and two .services files will be activated on-demand, and shouldn't need to be manually enabled or started.

The Minecraft server will start once someone connects to port **25566** (which will then be proxied to port 25565).

Server output will be written to the system log (monitor it with `journalctl -f -u minecraft.service`).

**Send messages to the server** by pushing text to `/run/minecraft.FIFO`, for example

`echo 'list' > /run/minecraft.FIFO`

## Extra notes...

The .socket and .service files for this setup are a little tricky. Hopefully these can serve as guideposts for someone else struggling to make it work.

The Minecraft server doesn't naturally support systemd's method of handing off activated ports to services. So it's necessary to use systemd's `systemd-socket-proxyd` command as a work-around. This is why we have the dumb thing where the server it listening on port *25565* but the client must connect to port *25566*.

In systemd's world, `.socket` and `.service` files should share the same base filename. When you do this, activity on the socket automatically kicks off the service. In our case, this is exactly the behaviour we want for `minecraft-proxy.socket` and `minecraft-proxy.service`.

(`minecraft.service` and `minecraft.socket` both get launched by dependencies, and so don't actually take advantage of the "socket-starts-service" mechanic)

If you see room for improvement, leave a comment under Issues.
