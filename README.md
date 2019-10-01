# JFrog Artifactory -> Font Awesome npm Registry

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
    |  OpenShift Router   |
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
|  Font Awesome npm Registry  |
|                             |
+-----------------------------+
```

## OpenShift Deployment

```powershell
(Get-Content nginx-114-rhel7.imagestream.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
).Replace(
    "REPLACE_WITH_DOCKER_REPOSITORY", "your_docker_repository"
) |
oc apply -f -

(Get-Content nginx-114-socat-rhel7.imagestream.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
) |
oc apply -f -

(Get-Content nginx-114-socat-rhel7.buildconfig.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
).Replace(
    "REPLACE_WITH_HTTP_PROXY", "your_http_proxy"
).Replace(
    "REPLACE_WITH_HTTPS_PROXY", "your_https_proxy"
).Replace(
    "REPLACE_WITH_NO_PROXY", "your_no_proxy"
).Replace(
    "REPLACE_WITH_PYTHON_REPOSITORY", "your_python_repository"
) |
oc apply -f -

(Get-Content artifactory-rproxy.configmap.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
).Replace(
    "REPLACE_WITH_CORPORATE_FORWARD_PROXY_HOSTNAME", "your_corporate_forward_proxy_hostname"
).Replace(
    "REPLACE_WITH_CORPORATE_FORWARD_PROXY_PORT", "your_corporate_forward_proxy_port"
).Replace(
    "REPLACE_WITH_BASIC_AUTH_PASSWORD", "your_basic_auth_password"
).Replace(
    "REPLACE_WITH_FONT_AWESOME_BEARER_TOKEN", "your_font_awesome_bearer_token"
) |
oc apply -f -

(Get-Content artifactory-rproxy.deploymentconfig.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
) |
oc apply -f -

(Get-Content artifactory-rproxy.route.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
).Replace(
    "REPLACE_WITH_PROXY_HOSTNAME", "your_proxy_hostname"
) |
oc apply -f -

(Get-Content artifactory-rproxy.service.yaml).Replace(
    "REPLACE_WITH_NAMESPACE", "your_namespace"
) |
oc apply -f -
```
