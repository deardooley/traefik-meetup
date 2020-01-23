## Dynamic load balancer with Docker Swarm and Docker Compose

The feature of Traefik that I use the most is its dynamic service discovery. This means you can simply add a few labels to your Docker containers and Traefik will discover the new services and load balance across them as you scale them up and down.

### Dynamic discovery with Docker Compose

```
cd dynamic-load-balancer/docker-compose
# start up Treafik and a simple whoami service
docker-compose up -d
```

You can view the newly launched service recognized by Traefik from the dashboard [http://localhost:28080/dashboard/#/http/services/thing1-docker-compose@docker]. Any changes to the service will be automatically detected and reflected by Traefik. 

Let's scale the `thing1` service up to 3 instances.

```
docker-compose scale thing1=3
```

You should see the new services show up in the Traefik dashboard as soon as you refresh your browser. 

Scaling the `thing1` service down to zero will remove it from the Traefik dashboard entirely.

```
docker-compose scale thing1=0
```

### Dynamic discovery with Docker Swarm

Migrating from Docker Compose to Docker Swarm is usually just an additional line in the Traefik config. We can easily demonstrate the same behavior we saw in the previous Docker Compose auto-discovery example on a Swarm.

If you're running Swarm, it's probably not locally, so security becomes a more prevalent concern. Up until now, we have been using self-signed certs for everything. When we have a top level domain name with a static, publicly accessible IP address, we can tell Traefik to fetch certificates from Let's Encrypt so our application is both secured and trusted.

We use these features in the [Agave project](https://agaveplatform.org/) to dynamically assign domain names with valid top-level certificates for each user at our training events.   

### Some gotchas when migrating from v1 to v2
You might notice that a couple of examples are using the v1 toml syntax. We are actively migrating all our instances to the new v2 yaml syntax, but it has not been quite as smooth as we hoped. 

#### Migration tool gets you most of the way there
The Traefik team provides a migration tool to convert v1 toml files into yaml and v2 toml. In my experience, for static configurations, it is a pretty good solution. We can test out the tool on the v1 toml file for the Swarm demo we just showed. 

```
cd dynamic-load-balancer/traefik-toml-v1
docker run --rm -v ${PWD}:/data containous/traefik-migration-tool static -i /data/traefik.toml -d /data
```

The output of that command will be two new files, `new-traefik.toml` and `new-traefik.yml`. 

#### Migrated acme confgs by hand
Looking at the migrated files you will see that the bulk of the original config has been converted. The noticeable exception being our acme config. We'll have to do that by hand. There is a command in the tool to migrate legacy acme.json files, but nothing I could find to migrate acme configs within a Traefik toml file.

#### Migrating labels is not supported
If you're making heavy use of dynamic discovery, you will have to work a bit harder to upgrade as there is no tool currently in place to address this conversion. 

#### Downtime required when using dynamic discovery
Additionally, if you are relying on labels to alert and configure Traefik, you may need to bounce your containers for the change to take place. While bouncing Traefik is almost instantaneous, bouncing your app may not be, so consider that when you're upgrading.  
