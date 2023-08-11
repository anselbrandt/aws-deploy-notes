# aws-deploy-notes

# Deploy

git-hooks/pre-push.sh

```
#!/bin/sh

docker build -t anselbrandt/go-notes:latest .
docker push anselbrandt/go-notes:latest
ssh root@anselbrandt.dev << HERE
docker pull anselbrandt/go-notes:latest
docker tag anselbrandt/go-notes:latest dokku/go-notes
dokku tags:deploy go-notes
docker system prune -a
y
HERE

exit 0
```

Copy the contents of `git-hooks/pre-push.sh` to `.git/hooks/pre-push.sample` and rename to `pre-push`

Modify the script with with your Docker Hub repo name, image name, Droplet domain/address, and Dokku app name.

Ensure your Droplet is configured and accessible via SSH, and you are logged in to Docker Hub.

Domain, proxying and SSL certificates should already be set up, and the application must be created.

The `create-app.sh` script can be run to set up your app. Don't forget to run `chmod +x create-app.sh` The script requires that SSL certs have already been uploaded, as below. If using the `letsencrypt plugin`, configure your app manually, or alter the script as needed.

This application deploys to the root domain.

```
dokku apps:create <app-name>
dokku domains:clear-global
dokku domains:set <app-name> <domain>
dokku proxy:ports-set <app-name> http:80:<port-exposed-by-docker-container>
```

### SSL Certs

If adding an existing cert/key pair, they must be named `server.crt` and `server.key` and put in a `.tar` file named `cert-key.tar` then uploaded to your Droplet.
