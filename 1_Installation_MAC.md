# Docker Course Part I

## Installation on MAC

Installing Docker on a MAC is really easy
the only step you need to do is download **dmg** file
from [here](https://hub.docker.com/editions/community/docker-ce-desktop-mac),
install it as usual on mac.

For see if all is ok you should run `docker version`, you should see something like this:

```bash
Client: Docker Engine - Community
 Version:           19.03.4
 API version:       1.40
 Go version:        go1.12.10
 Git commit:        9013bf5
 Built:             Thu Oct 17 23:44:48 2019
 OS/Arch:           darwin/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.4
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.10
  Git commit:       9013bf5
  Built:            Thu Oct 17 23:50:38 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.2.10
  GitCommit:        b34a5c8af56e510852c35414db4c1f4fa6172339
 runc:
  Version:          1.0.0-rc8+dev
  GitCommit:        3e425f80a8c931f88e6d94a8c831b9d5aa481657
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

Last step that needs to be done is to clone Brett's repo
available [here](https://github.com/BretFisher/udemy-docker-mastery)