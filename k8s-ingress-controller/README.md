## Ingress Controller

## Standard 
We can directly translate our previous examples into kubernetes ingress rules. 

```
# clone the chart from the release repo
git clone https://github.com/containous/traefik-helm-chart

# Install in the namespace "traefik"
helm install --namespace traefik \
             --name traefik \
             ./traefik-helm-chart
```

## Isolated devops environments

A/B testing, implicit ci/cd isolation, etc.
```

```