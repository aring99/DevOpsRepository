# DevOpsRepository

"ADD" supports remote URLs and tar files, but doesn't support error handling, so it is not used most of the time.

Creating a tar file, adding a "." at the end makes it so that only the content of the folder is copied and not the folder with the content inside:
tar -czvf tarfilename.tar.gz -C inputfoldername . 