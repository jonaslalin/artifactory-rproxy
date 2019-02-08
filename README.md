# JFrog Artifactory -> Font Awesome NPM Registry

## Traffic Flow

```

             XXXXX
             X   X
             XXXXX
               X
           XXXXXXXXX
               X
              XXX
             XX XX
            XX   XX

               +
               |
               |
               |
    HTTP:80    |  HTTPS:443
               |
    +----------v----------+
    |                     |
    |  JFrog Artifactory  |
    |                     |
    +----------+----------+
               |
               |
               |
               |
    HTTP:80    |  HTTPS:443
               |
    +----------v----------+
    |                     |
    |  OpenShift HAProxy  |
    |                     |
    +----------+----------+
               |
               |
               |
               |
   HTTP:8080   |
               |
   +-----------v-----------+
   |                       |
   |  NGINX Reverse Proxy  |
   |                       |
   +-----------+-----------+
               |
               |
               |
               |
   HTTP:9080   |
               |
   +-----------v-----------+
   |                       |
   |  Socat Forward Proxy  |
   |                       |
   +-----------+-----------+
               |
               |
               |
               |
               |  HTTPS:9443
               |
   +-----------v-----------+
   |                       |
   |  Socat Forward Proxy  |
   |                       |
   +-----------+-----------+
               |
               |
               |
               |
               |    HTTPS:8080
               |
 +-------------v-------------+
 |                           |
 |  Corporate Forward Proxy  |
 |                           |
 +-------------+-------------+
               |
               |
               |
               |
               |      HTTPS:443
               |
+--------------v--------------+
|                             |
|  Font Awesome NPM Registry  |
|                             |
+-----------------------------+

```


## OpenShift Deployment

```

(gc nginx-114-rhel7.imagestream.yaml).replace(
    "REPLACE_WITH_DOCKER_REPOSITORY", "your_docker_repository"
) | oc create -f -

oc create -f nginx-114-socat-rhel7.imagestream.yaml

(gc nginx-114-socat-rhel7.buildconfig.yaml).replace(
    "REPLACE_WITH_PYTHON_REPOSITORY", "your_python_repository"
) | oc create -f -

(gc artifactory-rproxy.configmap.yaml).replace(
    "REPLACE_WITH_CORPORATE_PROXY", "your_corporate_proxy"
).replace(
    "REPLACE_WITH_FONTAWESOME_TOKEN", "your_secret_bearer_token"
) | oc create -f -

oc create -f artifactory-rproxy.deploymentconfig.yaml

(gc artifactory-rproxy.route.yaml).replace(
    "REPLACE_WITH_HOSTNAME", "your_preferred_hostname"
) | oc create -f -

oc create -f artifactory-rproxy.service.yaml

```
