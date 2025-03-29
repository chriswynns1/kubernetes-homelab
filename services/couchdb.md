# CouchDB on Kubernetes
## Helm Chart
1. The easiest way to install CouchDB is by using the Helm chart, found here: ```https://artifacthub.io/packages/helm/couchdb/couchdb```.
2. Follow the instructions on the Helm chart page. Make sure to set a UUID.
## Ingress
I decided to go with ngrok to expose my CouchDB service. This allows me to access the database publicly without portforwarding. Here's an example of the ingress file:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: couchdb-ingress
spec:
  ingressClassName: ngrok
  rules:
    - host: cheetah-saving-albacore.ngrok-free.app
      http:
        paths:
          - path: /couchdb
            pathType: Prefix
            backend:
              service:
                name: my-couchdb-svc-couchdb
                port:
                  number: 5984
```
### NOTE: If you decide to use ngrok, you MUST have a paid ngrok account to use CouchDB with Obsidian.
Why? Ngrok puts a landing page in front of the service you expose, which causes problems when Obsidian LiveSync tries to retrieve information from the DB.

## ConfigMap
Hopefully by now everything is working. If you are able to access your CouchDB instance from the web at the ```/_utils``` path, you should be good.  

Next, we need to edit the ConfigMap created by the helm chart.
```
kubectl get configmap -n <namespace>
```
This will list the configmaps in the given namespace. We can edit it with:
```
kubectl edit configmap <configmap-name>
```
In my case it was named my-couchdb-couchdb. Edit it so the ini file section has these settings enabled:
```
[chttpd]
bind_address = any
#require_valid_user = true
max_http_request_size = 42949672960
authentication_handlers = {chttpd_auth, default_authentication_handler}

[chttpd_auth]
require_valid_user = true

[httpd]
enable_cors = true
WWW-Authenticate = Basic realm="Welcome to the Couch!"

[cors]
origins = app://obsidian.md,capacitor://localhost,http://localhost
credentials = true
methods = GET, PUT, POST, HEAD, DELETE
headers = accept, authorization, content-type, origin, referer, x-csrf-token

[couchdb]
admin = map[password:password username:admin]
uuid = <your-uuid-here>
max_document_size = 42949672960
```
Not sure why ```require_valid_user``` under ```chttpd``` was giving me issues, but LiveSync works fine without it enabled.  
## Delete / Restart Pods
Restart or delete all the pods related to CouchDB. The StatefulSet deployed by Helm will redeploy them with the updated config settings.
