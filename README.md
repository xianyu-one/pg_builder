## Build

```
docker build -t pg:ubuntu24 -f Dockerfile .
```

## Use

```
docker run --rm -v /PG_Builder/test:/export-path pg:ubuntu24
```