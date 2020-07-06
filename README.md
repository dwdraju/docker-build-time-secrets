# Ways for Managing Secrets in Docker Build Time

We might need build time secrets for 
* pulling private packages(npm, gradle etc.)
* git clone or any actions

Ways for using secrets:
* environment variable
* file

## Way 1(Passing environment variable on Dockerfile)
```Dockerfile
# Dockerfile.1
FROM alpine:3.12
ENV MY_SECRET superSecret
CMD tail -f /dev/null
```
Build the docker and explore:
`docker run -it secret1 /bin/sh`
```
/ # env
....
MY_SECRET=superSecret
```

Inspect image: `docker inspect secret1`
```
...
"Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "MY_SECRET=superSecret"
            ],
...
```
This is the simplest but absolutely not prefered way as the secret is exposed not only on version control but also anyone who can access the image can clearly see the secret.
The secret is visible even on inspecting the image

## Way 2(Passing build time argument and global env)
```Dockerfile
# Dockerfile.2
FROM alpine:3.12
ARG SECRET
ENV MY_SECRET $SECRET
CMD tail -f /dev/null
```
Build the docker: `docker build -t secret2 . -f Dockerfile.2 --build-arg SECRET=superSecret`

And explore:
`docker run -it secret2 /bin/sh`
```
/ # env
....
MY_SECRET=superSecret
```

Inspect image: `docker inspect secret1`
```
...
"Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "MY_SECRET=superSecret"
            ],
...
```
In this way, the secret is out of version control but still is clearly visible for image user as well as on inspecting.
