# Run, CMD, Entrypoint

- Run executes when building the image. 
- CMD and Entrypoint execute when the container starts. 

Entrypoints determines the main process, cmd is for additional parameters to pass into Entrypoint.

You can overwrite CMDs and you also don't need to rebuild the image (you can give it in runtime: docker container run imageName:tag overwritethecommandhere)
