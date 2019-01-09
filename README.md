# PHP App Demo Which Has Static Files on NFS

Instructions:
 - `git clone` this repo
 - cd into `php-nfs-demo`
 - run `cf push --no-start` to deploy the app
 - run `cf create-service nfs Existing nfs-mount -c '{"share":"<ip>/nfs/server/mount"}'` to create the NFS service
 - run `cf bind-service nfs-test nfs-mount -c '{"mount":"/nfs/volume","readonly":true}'` to bind the service to our app
 - run `cf start nfs-test` to start the app

It should start and run. You should then be able to access files off of the nfs, which is mounted to `/nfs/volume` in the container. The path to files will be `http://nfs-test.apps-domain/static/path/under/nfs/volumes/to/file.txt`. So if I have a file `/nfs/volume/test.txt` it would be at `http://nfs-test.apps-domain/static/test.txt`.

## How this Works

There is a file `.bp-config/httpd/extra/httpd-modules.conf` which is included with the app. This override the Apache Web Server configuration file of the same name that is provided by the buildpack. It does everything the default file provided by the buildpack file does, but additionally load mod_alias and configures an `Alias` for `/static` which points to `/nfs/volume`.

If you use a different `mount` argument to `cf bind-service`, you'll need to update the `httpd-modules.conf` file and change the location of the `Alias`. That file has been commented to further explain what it's doing.
