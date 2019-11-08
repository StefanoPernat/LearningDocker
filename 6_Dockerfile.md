# Dockerfile

The Dockerfile is a recipe for creating an image

Dockerfile structure sample

```Dockerfile
FROM <base_image>

# Optional ENVIROMENT VARIABLE
ENV <VARIABLE> <VALUE>

# If you need to install software o run shell commands
# in general
RUN <SHELL COMMAND>

# expose this ports on docker container
EXPOSE <PORT> <PORT> ....

# last command to run
CMD [<CMD1>, <SUB-COMMAND>, <PARAMETER>]

# every instruction is an actual layer in our docker
# image, the order matters because it works top down
```
The command to build a dockerfile is 
- `docker image build <directory>`

For avoiding the needs to rebuild the entire dockerfile

> Keep the things that changes the least on the top of the dockerfile
> and the things that changes the most at the bottom 

## Other Dockerfile commands

```dockerfile
WORKDIR /usr/share/nginx/html 
# it's like a cd on bash
```

```dockerfile
COPY index.html index.html
```