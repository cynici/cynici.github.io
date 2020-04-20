# Scan Docker Image

The easiest Docker Image security scanner to use by far, and free
https://github.com/aquasecurity/microscanner

Alternatively, you can use this wrapper to scan existing images, https://github.com/lukebond/microscanner-wrapper

## How I briefly got a taste of it

Sign up for a token, https://get.aquasec.com/microscanner

```

docker run -it alpine:3.3 sh
apk -U add wget ca-certificates && update-ca-certificates && \
wget https://get.aquasec.com/microscanner && chmod 755 microscanner && \
/microscanner {TOKEN}; echo "status: $?"
```

It's important to choose a supported base image otherwise you'll get a false negative.
