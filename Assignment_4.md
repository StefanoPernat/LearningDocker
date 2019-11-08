# Resulting Dockerfile

```dockerfile
# - you should use the 'node' official image, with the alpine 6.x branch
FROM node:6-alpine

# - this app listens on port 3000, but the container should launch on port 80
EXPOSE 3000

# - then it should use alpine package manager to install tini: 'apk add --update tini'
RUN apk add --update tini

# - then it should create directory /usr/src/app for app files with 'mkdir -p /usr/src/app'
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app

# - Node uses a "package manager", so it needs to copy in package.json file
COPY package.json package.json

# - then it needs to run 'npm install' to install dependencies from that file
# - to keep it clean and small, run 'npm cache clean --force' after above
RUN npm install && \
    npm cache clean --force

# - then it needs to copy in all files from current directory
COPY . .

# - then it needs to start container with command '/sbin/tini -- node ./bin/www'
CMD [ "/sbin/tini", "--", "node", "./bin/www" ]
```
## Other commands

- `docker build -t testnode .`
- `docker run -p 80:3000 --rm testnode`

## Tagging and pushing to docker hub

- `docker tag testnode wheelstep/testing_node`
- `docker push wheelstep/testing_node`