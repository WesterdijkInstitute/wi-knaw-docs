# General notes for managing things on a linux system

## Checking system info

Checking architecture of your system:

```bash
dpkg --print-architecture
# if `dpkg-dev` is installed
dpkg-architecture -q DEB_BUILD_ARCH
```

## Extract compressed files

```bash
tar -xvzf compressed.tar.gz
```
