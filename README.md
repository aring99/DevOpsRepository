# Docker Notes

docker:
Image, blueprint of a container. If you run a template, it creates a container. Running instance of the image
Process id number 1 is always the Kernel, however, the inside of the container is considered 
A different environment thus process id 1 is actually the deployed app in containers. 
If process id 1 shuts down/crashes, the whole container would stop

Use docker container run.
Specify version, if it is set to latest, with a new version it can break existing working container.

!! "ADD" supports remote URLs and tar, but No error handling so don't use it.
Creating a tar file, the "." at the end is important, it makes it so that the content of the folder is copied and not the folder with the content inside:
tar -czvf tarfilename.tar.gz -C inputfoldername . 
!! RUN vs CMD vs Entrypoint
Run executes when building the image. 
CMD and Entrypoint execute when the container starts. Entrypoints determines the main process, cmd is for additional parameters to pass into Entrypoint.
You can overwrite CMDs and you also don't need to rebuild the image(you can give it in runtime docker container run imageName:tag overwritethecommandhere)!
Docker build gitpath -t tag:number

Expose, to expose ports.
Docker container stop to stop container. Ls to show the list: docker image ls

How to change the name of the docker file: docker build -f filename . -t custom:1 instead of -f you can use --file="myfile" 
Nginx: open source proxy also used as a web server

docker Image rm imageID, to delete an image. Can use it with the first 2 characters or names. To delete every image, docker image rm $(docker image ls -aq)

Docker container ls (or ps) shows RUNNING containers, docker container ps shows all containers.

!!Pruning: Deleting Containers and the related images that are not used. Command is: Docker container prune

--name=name to name a container while running. Example: docker container run -d -p 8080:80 --name=mycontainername httpd:2.4 (-d to run in detached mode, means it runs in the background)

-v to add volume mount. Used as -v source:destination

Image tagging expanded: docker tag currentName:currentTag newName:newTag

EXEC into container, most common use case is setting up a bash session inside a running container.s

command: docker container exec -it imageID command(for example: /bin/bash)

The i stands for interactive mode, we are getting output out. The t stands for tty, like terminal. Which allows us to use keyboard input such as ls

Docker repositories, such as docker hub. If you are creating a docker repository using a service, such as aws, you should use the service name.
Name/reponame. Also, don't forget to tag the image accordingly before pushing.

Docker Layers:
https://jessicagreben.medium.com/digging-into-docker-layers-c22f948ed612

Passwords and tokens should be used during runtime, because using docker image history, they can be found.
Another command, docker image inspect imagename:tag provides extra information and layers.

**We want to use the least amount of layers to conserve space.
For example instead of:
Run ...
Run ...

We should use Run ... && \...

Another way is to use docker import/export. This will remove the layer history.

Docker save and load works similarly, however it retains layer history. Import and export doesn't retain history and exports as a tar ball.

Docker save is like doing a full backup of the image,
Docker export takes only a snapshot of the image, which is much smaller.

C: docker export imgID > export.tar
To import it: cat export.tar | docker import - imagename:imageid

**using FROM alpine

İs a good way to reduce size a lot, this is because alpine is a very small base image

Docker multistage images:
https://github.com/katherinelim/docker-multistage-example
https://docs.docker.com/develop/develop-images/multistage-build/

From ... AS build

If AS is not provided, it can be referred to as 0, such as COPY --from=0

To access a container use the exec flag.

What's the difference between a virtual machine and a container?

VM is the complete OS, whereas a container is only using the underlying host operating system's kernel.

We use Docker as the container runtime environment and we use Kubernetes as the tool or the platform

