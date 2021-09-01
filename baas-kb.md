
### BaaS Charts
https://stash.backbase.com/projects/BAAS/repos/baas-charts/browse/charts/baas-reference-stack
https://repo.backbase.com/webapp/#/artifacts/browse/tree/General/baas-charts/baas-reference-stack/baas-reference-stack-3.1.8.tgz


# OOTB Capability config
Example with limits/limit service

dbs:
  capabilities:
    limits:
      services:
        limit:
          enabled: true
          env:
            "spring.cloud.loadbalancer.ribbon.enabled": "false"
            "spring.autoconfigure.exclude": "org.springframework.cloud.netflix.eureka.loadbalancer.LoadBalancerEurekaAutoConfiguration"
            "backbase.security.mtls.enabled": "false"
            BACKBASE_COMMUNICATION_HTTP_ACCESS-TOKEN-URI: "http://token-converter:8080/oauth/token"
            LOGGING_LEVEL_COM_BACKBASE: INFO
            LOGGING_LEVEL_ORG_SPRINGFRAMEWORK: INFO
            LOGGING_LEVEL_ROOT: INFO
            LOGGING_LEVEL_COM_SECURITY: INFO

# access-group-integration-service DBS 2.19 issues
Add:
backbase.accessgroup.token.key: Bar12345Bar12345
backbase.accessgroup.token.initPhrase: RandomInitVecto2

# Build issues

- Bump version so there isn't errors with Docker repository and/or Github packages
-- https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-tag-mutability.html


- Docker repository is immutable
Error:  Failed to execute goal com.google.cloud.tools:jib-maven-plugin:2.4.0:build (distroless-image-jar-latest) on project bnp-communications-outbound-integration-service: Tried to push image manifest for ***/bnp-communications-outbound-integration-service:1.0.2 but failed because: Registry may not support pushing OCI Manifest or Docker Image Manifest Version 2, Schema 2 | If this is a bug, please file an issue at https://github.com/GoogleContainerTools/jib/issues/new: 400 Bad Request
Error:  {"errors":[{"code":"TAG_INVALID","message":"The image tag '1.0.2' already exists in the 'bnp-communications-outbound-integration-service' repository and cannot be overwritten because the repository is immutable."}]}

-- Add maven profiles no-scs or no-scs,no-latest-tag in Build docker image
E.g.:
<code>
- name: Build docker image
  run: mvn clean package -Pdocker-image,no-scs,no-latest-tag -Djib.from.auth.username=${{ steps.ecr-credentials.outputs.username }}  -Djib.from.auth.password=${{ steps.ecr-credentials.outputs.password }} -Djib.to.auth.username=${{ steps.ecr-credentials.outputs.username }}  -Djib.to.auth.password=${{ steps.ecr-credentials.outputs.password }}
</code>


- Release name too long
> kubectl describe helmrelease bnp-communications-outbound-integration-service -n backbase
Events:
  Type     Reason             Age                From           Message
  ----     ------             ----               ----           -------
  Warning  FailedReleaseSync  97s (x4 over 12m)  helm-operator  synchronization of release 'bnp-communications-outbound-integration-service' in namespace 'backbase' failed: installation failed: render error in "backbase-app/templates/service.yaml": template: backbase-app/templates/service.yaml:8:3: executing "backbase-app/templates/service.yaml" at <include "backbase-app.labels" .>: error calling include: template: backbase-app/templates/helpers/_labels.tpl:34:3: executing "backbase-app.labels" at <include "backbase-app.match-labels" .>: error calling include: template: backbase-app/templates/helpers/_labels.tpl:6:31: executing "backbase-app.match-labels" at <include "backbase-app.fullname" .>: error calling include: template: backbase-app/templates/helpers/_chart.tpl:37:6: executing "backbase-app.fullname" at <fail "\n\nFull name too long, try to reduce release name!">: error calling fail:
Full name too long, try to reduce release name!

-- Unknown max length, seen one working with 35 chars so use 35 or less


# Liveness check issues
1. scale down the deployment:
kubectl -n backbase scale deploy dbs-access-control-usermanager --replicas=0

2.  connect to db and remove the lock:
UPDATE DATABASECHANGELOGLOCK
SET LOCKED = 0,
    LOCKGRANTED = null,
    LOCKEDBY = null
where ID = 1;

3.  scale up the service once again:
kubectl -n backbase scale deploy dbs-access-control-usermanager --replicas=1
