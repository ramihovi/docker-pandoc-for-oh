Docker Pandoc for Law Office Oikeushovi's SFS 2487 template
================================================================================

This repository (forked from
[pandoc/dockerfiles](https://github.com/pandoc/dockerfiles)) contains a
collection of Dockerfiles to build a [Pandoc](https://pandoc.org/) container
images for Law Office Oikeushovi's SFS 2487 template that generates an
[SFS 2487](https://sales.sfs.fi/fi/index/tuotteet/SFS/SFS/ID2/2/850.html.stx)
formatted official document from given
[markdown](https://pandoc.org/MANUAL.html#pandocs-markdown) and
[yaml](https://yaml.org/) files.

Building docker images
================================================================================

```{#build .sh}
git clone https://github.com/ramihovi/docker-pandoc-for-oh.git
cd docker-pandoc-for-oh
STACK=ubuntu make latex
```

The resulting images are:

- pandoc/latex:edge-ubuntu
- ubuntu:jammy

They are both needed for Law Office Oikeushovi's SFS 2487 template.

Usage
================================================================================

> **Note**: this section describes how to use the docker images.  Please refer
> to the [`pandoc` manual](https://pandoc.org/MANUAL.html) for usage information
> about `pandoc`.

Docker images are pre-provisioned computing environments, similar to virtual
machines, but smaller and cleverer. You can use these images to convert document
wherever you can run docker images, without having to worry about pandoc or its
dependencies. The images bring along everything they need to get the job done.

Basic Usage
--------------------------------------------------------------------------------

1. Install [Docker](https://www.docker.com) if you don't have it already.

2. Start up Docker. Usually you will have an application called "Docker" on your
   computer with a rudimentary graphical user interface (GUI). You can also run
   this command in the command-line interface (CLI):

   ```sh
   open -a Docker
   ```

3. Open a shell and navigate to wherever the files are that you want to convert.

   ```sh
   cd path/to/source/dir
   ```

   You can always run `pwd` to check whether you're in the right place.

4. [Run docker](https://docs.docker.com/engine/reference/run/) by entering the
   below commands in your favorite shell.

   Let's say you have a `README.md` in your working directory that you'd like to
   convert to HTML.

   ```sh
   docker run --rm --volume "`pwd`:/data" --user `id -u`:`id -g` pandoc/latex:edge-ubuntu README.md
   ```

   The `--volume` flag maps some directory on *your machine* (lefthand side of
   the colons) to some directory *in the container* (righthand side), so that
   you have your source files available for pandoc to convert. `pwd` is quoted
   to protect against spaces in filenames.

   Ownership of the output file is determined by the user executing pandoc *in
   the container*. This will generally be a user different from the local user.
   It is hence a good idea to specify for docker the user and group IDs to use
   via the `--user` flag.

   `pandoc/latex:edge-ubuntu` declares the image that you're going to run. It's always a
   good idea to hardcode the version, lest future releases break your code.

   It may look weird to you that you can just add `README.md` at the end of this
   line, but that's just because the `pandoc/latex:edge-ubuntu` will simply prepend
   `pandoc` in front of anything you write after `pandoc/latex:edge-ubuntu` (this is
   known as the `ENTRYPOINT` field of the Dockerfile). So what you're really
   running here is `pandoc README.md`, which is a valid pandoc command.

   If you don't have the current docker image on your computer yet, the
   downloading and unpacking is going to take a while. It'll be (much) faster
   the next time. You don't have to worry about where/how Docker keeps these
   images.

Pandoc Scripts
--------------------------------------------------------------------------------

Pandoc commands have a way of getting pretty long, and so typing them into the
command line can get a little unwieldy. To get a better handle of long pandoc
commands, you can store them in a script file, a simple text file with an `*.sh`
extension such as

```sh
#!/bin/sh
pandoc README.md
```

The first line, known as the [*shebang*](https://stackoverflow.com/q/7366775)
tells the container that the following commands are to be executed as shell
commands. In our case, we really don't use a lot of shell magic, we just call
pandoc in the second line (though you can get fancier, if you like). Notice that
the `#!/bin/sh` will *not* get you a full bash shell, but only the more basic
ash shell that comes with Alpine linux on which the pandoc containers are based.
This won't matter for most uses, but if you want to write writing more
complicated scripts you may want to refer to the [`ash`
manual](https://linux.die.net/man/1/ash).

Once you have stored this script, you must make it executable by running the
following command on it (this may apply only to UNIX-type systems):

```sh
chmod +x script.sh
```

You only have to do this once for each script file.

You can then run the completed script file in a pandoc docker container like so:

```sh
docker run --rm --volume "`pwd`:/data" --entrypoint "/data/script.sh" pandoc/latex:edge-ubuntu
```

Notice that the above `script.sh` *did* specify `pandoc`, and you can't just
omit it as in the simpler command above. This is because the `--entrypoint` flag
*overrides* the `ENTRYPOINT` field in the docker file (`pandoc`, in our case),
so you must include the command.


License
================================================================================

Code in this repository is licensed under the
[GNU General Public License, version 2.0 or later](LICENSE).
