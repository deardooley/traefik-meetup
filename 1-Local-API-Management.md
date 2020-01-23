## Local API Management

I work in a hybrid development environment where some services run in the cloud, some run locally, and some run containerized on one of a few local VM.  

#### Challenges 
* No static IP 
* Limited resources
* Service discovery across runtime environments
* I frequently write in Java (don't laugh), Go, and Typescript, which means I am always compiling code. Recompiling slows everything down, so I'd rather use my IDE for daily sprints. 

### How does Traefik help?
I run Traefik locally on my development machine to provide a bare bones api management solution at a fraction of the operational cost.

* Configuration files for discovery of persistent local services
* Docker provider for discovery of local containers 
* Use local `/etc/hosts` file to handle DNS. Manage multiple files with [Gas Mask](https://www.clockwise.ee).
