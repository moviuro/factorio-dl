# Factorio-DL

## Jumpstart

This (more-or-less) POSIX-compliant shell script will download the specified
Factorio version for a target platform.

    % ./factorio-dl -l login -p password -t linux64 0.16.12
    % ./factorio-dl -l login -p password -t osx 0.15.40

## Configuration

### Simple

This shell script will **source** the `~/.config/factorio-dl.conf` file if it
exists. This file is a shell script, and it should hold 1-3 lines at most:

    FACTORIO_TARGET="your target" # if unspecified, defaults to linux64
    FACTORIO_LOGIN="your login here" # if unspecified, defaults to $(whoami)
    FACTORIO_PASSWORD="your password here"

If the configuration file contains a password, the following command should
work:

    % ./factorio-dl -t win64 0.16.12

### "Secure"

You can run shell commands in the config file (`~/.config/factorio-dl.conf`):

    FACTORIO_PASSWORD="$(pass show games/factorio | head -n1)"

## Archlinux-specific

### Integration to `makepkg.conf(5)`

Add the `DLAGENT` to your `makepkg.conf(5)`:

    DLAGENTS=([...]
    +         'factorio::/usr/bin/factorio-dl -t linux64 -o %o %u'
              [...])

Use the `factorio://` URL scheme in the `PKGBUILD`:

    source=(factorio_alpha_x64_$pkgver.tar.xz::factorio://$pkgver
            factorio.desktop
            LICENSE)

This will cause `makepkg(1)` to invoke the following command, which happens to
do exactly what we expect it to do!

    % /usr/bin/factorio-dl -t linux64 -o factorio_alpha_x64_$pkgver.tar.xz factorio://$pkgver

### Magical `PKGBUILD`

We can use the `PKGBUILD` to temporarily add a `DLAGENT`:

    DLAGENT+=('factorio::/usr/bin/factorio-dl -t linux64 -o %o %u')
    source=("factorio_alpha_x64_$pkgver.tar.xz::factorio://$pkgver")

There is [an example of a `PKGBUILD` using this
technique](https://gitlab.com/moviuro/factorio-dl/snippets/1691785).

## Bugs?

Open an issue, append the output of the offending command, with the `-d` switch
added to the command line. Make sure to hide your credentials.
