# docker-walkthrough



## Docker Environment Setup

To install on Linux or Mac, use sdkman (or brew on Mac: ```brew install --cask docker```)
- https://docs.docker.com/desktop/install/mac-install/
- https://www.youtube.com/watch?v=6Rjf2fpuoPA

To install on Windows, we need WSL2 or Hyper-V according to https://docs.docker.com/desktop/install/windows-install/.
But this conflicts with VirtualBox.
So we install it on the VirtualBox running Linux. The limitation is that we can't
install Docker Desktop inside the VirtualBox.

Follow the following posts:
- [How to Install Docker on Fedora 36 / Fedora 35](https://www.itzgeek.com/how-tos/linux/fedora-how-tos/install-docker-on-fedora.html)
- [How to Install Docker Desktop on Fedora Linux](https://linuxiac.com/how-to-install-docker-desktop-on-fedora-linux/)
- [Docker Installation In Windows System Through Oracle Virtual Box](https://www.c-sharpcorner.com/article/docker-installation-in-windows-system-through-oracle-virtual-box/)
- https://blog.csdn.net/weixin_44385419/article/details/127738868
- https://blog.csdn.net/m0_68211831/article/details/128161766

here are the installation steps:
```shell
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```
We install all docker components here, which are included in the docker desktop.

To test the installation
```shell
docker -v
docker run -it fedora echo Hello-World
docker run hello-world
docker ps  # show what’s running
docker run -p 8989:80 -d nginx  # to run nginx
docker stop <container id>
```

### Getting Along
To check VirtualBox IP address: https://tecadmin.net/check-ip-address-fedora-desktop/
```shell
ip addr
```

Docker container logs are here: https://sematext.com/blog/docker-logs-location/

Docker commands
```shell
docker -v
docker ps
docker ps -a
docker images
docker pull nginx
docker logs <container id>
docker stop <ID>
docker rm <ID>
docker rmi <ID>
```

Permission: https://blog.csdn.net/wo541075754/article/details/127915498

Docker intro (Chinese): https://blog.csdn.net/cccccccmmm/article/details/127948916
https://blog.csdn.net/fertiland/article/details/128048496

### Docker Desktop
To Check whether docker-desktop can be installed, if there is no output from below,
then we can't install it.
```shell
sudo modprobe kvm
egrep '^flags.*(vmx|svm)' /proc/cpuinfo
```
Or this shows it's supported or not
```shell
dmesg | grep kvm
```

If kvm is supported, we need to turn on Hyper-V for VirtualBox (My PC is old and doesn't support it)
```commandline
D:\0dev\VirtualBox\VBoxManage.exe modifyvm D:\VMs\Fedora_VM\CENTOS\Fedora_WorkStation\Fedora_WorkStation.vbox --nested-hw-virt on
```
See more:
- https://www.linkedin.com/pulse/docker-desktop-ubuntu-oracle-virtualbox-roberto-raimondo/?trk=pulse-article_more-articles_related-content-card
- https://www.cyberithub.com/how-to-enable-nested-vt-x-amd-v-in-virtualbox-step-by-step/
- https://github.com/docker/desktop-linux/issues/2
- https://github.com/docker/desktop-linux/issues/52


## Simple Spring Boot Application
Follow: https://spring.io/guides/gs/spring-boot/
- Refresh gradle in IDE: gradle should be setup during the refresh
- ```.\gradlew bootRun``` to run the app
- ```curl localhost:8080``` should display Hello World!

To build jar  
```.\gradlew clean build```

To test  
```java -jar build\libs\docker-walkthrough-1.0.0-SNAPSHOT.jar```  
and then ```curl localhost:8080``` as above.


## Build Docker Images

- Add a Dockerfile to specify the image build
- **Need to remove the version in the jar name later on.** https://github.com/spotify/dockerfile-maven/issues/20
- In build.gradle, add the plugin and specify docker's name and tag.
- check the previous build ```docker iamges``` 
- remove the previous build ```docker rmi -f 1e8d2411917f```
- start the build with ```./gradlew docker```
- Run ```docker images``` to verify the new build
- If needed, get into image: ```docker run --rm -it --entrypoint=/bin/sh 1e8d2411917f```

We need to pick the simplest plugin that don't enclose Dockerfile details.

https://www.toutiao.com/article/7167664584869003808/

## Run Docker Images

docker rm hellow  
docker run -d -p 8080:8080 --name hellow 192.168.1.206:5000/docker-walkthrough:1.0.0-SNAPSHOT
docker ps  
docker stop 71428877674a  # container id from docker ps

There is a docker-run plugin: https://tomgregory.com/automating-docker-builds-with-gradle/.
We use docker command here.

There is a comparison of plugins: https://jozala.com/posts/2019-11-21-docker-image-with-gradle/.
We pick the one that lets us write Dockerfiles.

Docker best practices:
- https://mydeveloperplanet.com/2022/11/30/docker-best-practices/
- https://dzone.com/articles/spring-boot-docker-best-practices

## Docker Compose

- https://github.com/docker/compose
- https://www.baeldung.com/ops/kafka-docker-setup
- https://www.educative.io/blog/docker-compose-tutorial
- https://www.toutiao.com/article/7167634243265430054/
- https://www.makeuseof.com/install-docker-compose-on-linux/
- https://blog.csdn.net/qq_41890624/article/details/128389234


## Local Docker Registry/Repository

If needed, disable local HTTPS. add a new file /etc/docker/daemon.json with the content
```json
{
    "insecure-registries": ["192.168.1.206:5000"]
}
```

Then create a local folder to hold images:
```shell
mkdir docker-registry-data
```
Now start the local registry
```shell
docker run -d -p 5000:5000 --name dockerregistry --restart=always -v $(pwd)/docker-registry-data:/var/lib/registry registry:latest
systemctl status docker
curl -X GET http://192.168.1.206:5000/v2/_catalog  # returns {"repositories":[]}
```

To test it
```shell
docker pull nginx
docker tag nginx 192.168.1.206:5000/nginx
docker images
docker push 192.168.1.206:5000/nginx  # push to local registry at port 5000
curl -X GET http://192.168.1.206:5000/v2/_catalog  # returns {"repositories":["nginx"]}
docker rmi -f 1403e55ab369  # Now remove both nginx from docker images
docker pull 192.168.1.206:5000/nginx  # download from local registry
docker images  # we should see it
ls -l docker-registry-data/docker/registry/v2/repositories/  # see it in the folder as well
```

https://www.youtube.com/watch?v=O_NMIZJ1qvw


[Docker & Kafka](https://blog.csdn.net/yuanchun05/article/details/128423824)
https://github.com/itmuch/docker-book