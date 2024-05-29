# Temper on Docker

> And podman!

This repository contains releases only; the source will be available within the main temper repo.

### Getting started, quickly

A great thing about Temper is that it integrates with many languages, but if you're trying it out, you'd probably prefer not to install half a dozen build systems.

The `temper-docker` tool manages the Temper toolchain, including backend build systems, via containers.

By running the `temper` CLI inside a container, you get the correct versions of the supporting tools ready to go. It mounts your local filesystem into the container to integrate with your regular workflow.

#### Docker or Podman

You'll need to [install Docker](https://docs.docker.com/get-docker/) or [install Podman](https://podman.io/docs/installation) and make sure they're running. The `temper-docker` tool will detect which is installed using the normal environment variables and default settings.

(TODO: verify which versions are required)

#### Download `temper-docker`

Download the distribution for your OS from [the releases page](https://github.com/temperlang/temper-docker/releases) and unzip, placing the binary some place convenient.

You may need to mark the binary as executable / safe:

- `chmod +x ./temper-docker` on Linux, Mac, WSL
- `xattr -d com.apple.quarantine ./temper-docker` on Mac

> For clarity, we'll call it `./temper-docker`, but it's a single statically-linked binary and it does not matter what it's named or where it's installed.

#### Running

We'll assume you've named the binary `temper-docker`.

``` bash
./temper-docker help
```

It will download the container in the background, and then call the `temper` CLI with the help command. (TODO: make the progress more consistent and prettier.)

(TODO: replace auto-download with a less magical scheme.)

#### Support commands

While the `temper-docker` command is designed to pass its arguments to the `temper` CLI, it provides a few features to help manage the container.

``` sh
# display help for the temper-docker command itself
./temper-docker --runner-help

# log into the container as a regular user
./temper-docker --shell

# log into the container as root
./temper-docker --root
```

### Uninstallation

The uninstallation process is:

- stop and delete all containers whose name starts with `temper_`
- delete all `ghcr.io/temperlang/temper` images
- delete the `./temper-docker` binary
- uninstall Docker or Podman if you don't need them

## Additional notes

### Containers and work roots

If you're less familiar with how containers work, briefly:

* an **image** contains an OS and applications preinstalled
* a **container** is an isolated set of processes running on that image
* a **mount** gives a container access to the host filesystem

The `temper-docker` command determines Temper work root by:

* (TODO: use the `--workroot` argument to `temper build`)
* (TODO: add the `temper.out` rule)
* looking for `config.temper.md` in an ancestor directory
* falling back to the working directory

The work root is translated into a container name, and `./temper-docker` either reuses or creates a new container. That container will have a mount placed at the work root.

To forcibly recreate a container, use `./temper-docker --new-container`.

### Similarities to the `docker` and `podman` CLIs

Generally, `temper-docker` is very similar to `docker run`. Containers are typically optimized for running services like databases or web servers rather than integrating command-line applications, so there are a few differences:

* directly wraps the `temper` CLI so it can be invoked without special arguments
* automates mounting your local file system
* the container will shut itself down after a period of time if no process is running
* detects an interactive terminal session and does some work to make sure your terminal is configured
* (TODO: pass host filesystem watch events into the container)
* (TODO: allow persistent custom tool installs)
