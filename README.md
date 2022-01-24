# SEP 1 DB Sandbox

## What the hell is this?

This is a compilation of all solutions for tasks we had to complete during the first semester. To verify that theses solutions actually work and to try out different solutions, I created this pgsql image.

## Ok. But how do I use this?

It's not too hard. Here's what you have to do:  

1) Download the latest DB image from the [Releases page](https://github.com/ChiliEater69/zhaw_db/releases)
2) While you wait for that to download, download [Docker Desktop](https://www.docker.com/products/docker-desktop) (or whatever docker thing you use if you're not on Windows or MacOS)
3) Make sure to follow the Docker installation "guide" carefully and heed any warnings you see.
4) Clone this repo somewhere. (If you don't know how to this by now, I've got bad news for ya...)
5) Copy the two folders containing the DB images to your repo folder
6) Now open a terminal at your repo folder
7) (Windows only) type `wsl` and hit enter
8) Enter the following command: `chown -R 5050:5050 pgadmin` (you may have to prepend with `sudo`)
9) (Windows only) type `exit` and hit enter
10) Enter the following command and wait: `docker-compose up -d` (depending on your system this may take several minutes)
11) Access your control panel under [http://localhost:8080](http://localhost:8080) and login with `a@a.xyz`/`amogus`
12) Right-click on whichever DB you want to work on and select 'Open query tool'
13) Press on the folder thingie in the top right and select the matching SQL File
14) Once your done type `docker-compose down` in your terminal to shutdown the instance
15) If something goes wrong and you don't know what, type `docker-compose logs --follow` to get a live view of the log files (idk might help)

## Neat. And what now?

I don't know. You tell me.