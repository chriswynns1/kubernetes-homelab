# Hashicorp Vault
## Installation Using Rancher

1. Go to Apps > Charts > Search for "vault"
2. Install it, but configure the ingress in the .yaml to use traefik:
```
ingress:
    activeService: true
    annotations:
      traefik.ingress.kubernetes.io/router.entrypoints: web,websecure
      traefik.ingress.kubernetes.io/router.tls: 'true'
    enabled: true
    extraPaths: []
    hosts:
      - host: vault.wynnz.fyi
        paths: []
    ingressClassName: ''
    labels: {}
    pathType: Prefix
    tls:
      - hosts:
          - vault.wynnz.fyi
        secretName: letsencrypt-prod
```
3. Add an A record to Cloudflare to point to your load balancer (10.0.0.100 in my case, which is the keepalived IP)
4. Add an ingress route in Rancher: Service Discovery > Ingresses > Create.
5. Target the ```vault``` service on port 8200, using the letsencrypt-prod secret we created earlier.
