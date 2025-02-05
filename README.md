## Build

```
docker build -t pg:ubuntu24 -f Dockerfile_for_Ubuntu24.04 .
```

## Use

```
docker run --rm -v /PG_Builder/test:/export-path pg:ubuntu24
```