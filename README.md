# Example Project

https://github.com/doherty-labs/django-rest-api

# Terraform Modules

https://github.com/doherty-labs/terraform-module-repo

# Delete Old Jobs 

```
kubectl delete -n default job collectstatic --cascade=background --ignore-not-found=true
kubectl delete -n default job django-migrate --cascade=background --ignore-not-found=true
kubectl delete -n default job elastic-migrate --cascade=background --ignore-not-found=true
kubectl delete -n default job django-create-superuser --cascade=background --ignore-not-found=true
```


# Add Repo

`helm repo add doherty-labs https://doherty-labs.github.io/helm-charts/`

# Install Chart
`helm upgrade --install django-celery-api doherty-labs/django-celery-api -f ./infrastructure/helm-values.yaml --debug --wait`


# Example Values File

```

service:
  name: rest-api
  namespace: default
  hostName: "${REST_API_HOSTNAME}"
  port: 8000
  image:
    repository: "${DOCKER_REG_URL}/${DOCKER_REG_NAME}/${REST_API_IMAGE_NAME}"
    tag: "${SHA_VERSION}"
  autoscaling:
    minScale: 1
    maxScale: 10
    targetUtilizationPercentage: 60
  env:
    - name: GUNICORN_WORKERS
      value: "2"
    - name: DJANGO_SETTINGS_MODULE
      value: "${DJANGO_SETTINGS_MODULE}"
  resources:
    requests:
      cpu: "1000m"
      memory: "2Gi"

celery:
  scheduler:
    name: celery-scheduler
    namespace: default
    image:
      repository: "${DOCKER_REG_URL}/${DOCKER_REG_NAME}/${CELERY_SCHEDULER_IMAGE_NAME}"
      tag: "${SHA_VERSION}"
    env:
      - name: DJANGO_SETTINGS_MODULE
        value: "${DJANGO_SETTINGS_MODULE}"
    resources:
      requests:
        cpu: "1000m"
        memory: "2Gi"
  flower:
    name: flower
    namespace: default
    hostName: "${FLOWER_HOSTNAME}"
    port: 5555
    image:
      repository: "${DOCKER_REG_URL}/${DOCKER_REG_NAME}/${FLOWER_IMAGE_NAME}"
      tag: "${SHA_VERSION}"
    autoscaling:
      minScale: 1
      maxScale: 10
      targetUtilizationPercentage: 60
    env:
      - name: DJANGO_SETTINGS_MODULE
        value: "${DJANGO_SETTINGS_MODULE}"
    resources:
      requests:
        cpu: "1000m"
        memory: "2Gi"
  worker:
    name: celery-worker
    namespace: default
    image:
      repository: "${DOCKER_REG_URL}/${DOCKER_REG_NAME}/${CELERY_WORKER_IMAGE_NAME}"
      tag: "${SHA_VERSION}"
    autoscaling:
      minScale: 1
      maxScale: 10
      queueLengthTarget: 10
      queueName: celery
    env:
      - name: DJANGO_SETTINGS_MODULE
        value: "${DJANGO_SETTINGS_MODULE}"
    resources:
      requests:
        cpu: "1000m"
        memory: "2Gi"


```


