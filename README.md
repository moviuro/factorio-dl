# Factorio-DL

## Jumpstart

This (more-or-less) POSIX-compliant shell script will download the specified
Factorio version for a target platform.

    % ./factorio-dl -l login -p password -t linux64 0.16.12
    % ./factorio-dl -il login -t osx 0.15.40

## Configuration

This shell script will use your `player-data.json` file if it exists. On Linux,
you probably don't need to do anything. On macOS, specify a path to that file
with `-y`.

If `player-data.json` file does not exist or does not contain the necessary
data, try `-il YOUR_LOGIN`.

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
