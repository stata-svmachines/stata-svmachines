# Testing Container

This builds a container with CLI Stata installed for use as a testing environment.

This is a **manual process** because it requires downloading a copy of Stata from behind a paywall using your human credentials.

## Prerequisites

You must have a copy of the Stata linux installer from https://download.stata.com. As this builds a _Linux_ container you need the _Linux_ installer. It should be named Stata15Linux64.tar.gz.

> [!WARNING]
> This has only been tested with Stata 15, though it should adapt easily to others.

## Building

```
cp -l ~/Downloads/Software/uwaterloo/Stata/Stata15Linux64.tar.gz .
```

(if `-l` fails just use `cp` by itself)

Then

```
make container
```

## Licensing

The build does not embed a license. For that, launch the container once and run `stinit` in it:

```
$ podman run --rm -it --name stata-init stata
root@stata-init:/# (cd /usr/local/stata/; ./stinit)
```

Follow the prompts and provide your Stata license information. Extract the generated license with

```
$ docker cp stata-init:/usr/local/stata/stata.lic .
```

Put `stata.lic` in your password manager/secrets storage/keychain.

## Usage

To use the image, bind-mount the license file in:

```
$ podman run --rm -it -v `pwd`/stata.lic:/usr/local/stata/stata.lic stata
```

or create the file as the first step after launching the container:

```
podman run --rm -it -d --name stata stata
podman cp stata.lic stata:/usr/local/stata/
podman exec -it stata bash -l
```

or

```
podman run --rm -it -d --name stata stata
cat stata.lic | podman exec stata tee /usr/local/stata/stata.lic
podman exec -it stata bash -l
```

Then you should be able to use Stata:

```
root@568e64e0888b:/# stata

  ___  ____  ____  ____  ____ (R)
 /__    /   ____/   /   ____/
___/   /   /___/   /   /___/   15.1   Copyright 1985-2017 StataCorp LLC
  Statistics/Data Analysis            StataCorp
                                      4905 Lakeway Drive
                                      College Station, Texas 77845 USA
                                      800-STATA-PC        http://www.stata.com
                                      979-696-4600        stata@stata.com
                                      979-696-4601 (fax)

Single-user Stata perpetual license:
       Serial number:  999999999999
         Licensed to:  Firstname Lastname
                       University of Institution

Notes:
      1.  Unicode is supported; see help unicode_advice.
```

## Publishing

Upload this to GitHub so it can be used in CI. Make sure to upload it _privately_.

[Generate a new *classic* token](https://github.com/settings/tokens) with `write:packages`.

```
export GITHUB_OWNER=your-username
export GITHUB_TOKEN=ghp_xxxx....
make container-publish
```

Go to https://github.com/your-username?tab=packages and double-check it is there and is, in fact, Private.

It will then be available to `docker pull ghcr.io/your-username/stata:15`,
but you need to `docker login` with your `$GITHUB_TOKEN`

> [!TIP]
> Generate a GITHUB_TOKEN limited to `read:packages` for services that use the image.
