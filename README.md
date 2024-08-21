# systemd files for Java Minecraft Server

A collection of systemd files to run a Java Minecraft server on a Linux PC.

Bring the server up only when traffic is detected. Automatically shut it down when no one is playing.

Use a "FIFO" socket to send commands to the running server as it runs in the background.

## More info...

The .socket and .service files for this setup are a little tricky. Hopefully these can serve as guideposts for someone else struggling to make it work.

The Minecraft server doesn't naturally support systemd's method of handing off activated ports to services. So it's necessary to use systemd's `systemd-socket-proxyd` command as a work-around.

If you see room for improvement, leave a comment under Issues.
