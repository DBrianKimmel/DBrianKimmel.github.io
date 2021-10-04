# MkDocs

[Docs](https://www.mkdocs.org/getting-started/#theming-our-documentation)

## Docker

Image for AMD64

``` bash
docker pull squidfunk/mkdocs-material
```

Image for ARM64 and others

``` bash 
docker pull ghcr.io/afritzler/mkdocs-material:latest
```

## run

``` bash
docker run --rm -it -v ${PWD}:/docs squidfunk/mkdocs-material new .
```

``` bash
docker run --rm -it -v ${PWD}:/docs ghcr.io/afritzler/mkdocs-material:latest .
```

