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
  hostName: ""
  port: 8000
  image:
    repository: ""
    tag: ""
  autoscaling:
    minScale: 1
    maxScale: 10
    targetUtilizationPercentage: 60
  env:
    - name: DJANGO_SETTINGS_MODULE
      value: "django_project.settings_qa"
    - name: GUNICORN_WORKERS
      value: 2
  resources:
    requests:
      cpu: "2000m"
      memory: "4Gi"

celery:
  scheduler:
    name: celery-scheduler
    namespace: default
    image:
      repository: ""
      tag: ""
    env:
      - name: DJANGO_SETTINGS_MODULE
        value: "django_project.settings_qa"
    resources:
      requests:
        cpu: "2000m"
        memory: "4Gi"
  flower:
    name: flower
    namespace: default
    hostName: ""
    port: 5555
    image:
      repository: ""
      tag: ""
    autoscaling:
      minScale: 1
      maxScale: 10
      targetUtilizationPercentage: 60
    env:
      - name: DJANGO_SETTINGS_MODULE
        value: "django_project.settings_qa"
    resources:
      requests:
        cpu: "2000m"
        memory: "4Gi"
  worker:
    name: celery-worker
    namespace: default
    image:
      repository: ""
      tag: ""
    queues:
      - queueName: celery
        autoscaling:
          minScale: 1
          maxScale: 5
          queueLengthTarget: 10
          pollingInterval: 30
          cooldownPeriod: 200
        env:
          - name: DJANGO_SETTINGS_MODULE
            value: "${DJANGO_SETTINGS_MODULE}"
          - name: WORKER_POOL
            value: "gevent"
          - name: CONCURRENCY
            value: "100"
          - name: ENABLE_GEVENT_PATCH
            value: "true"
        resources:
          requests:
            cpu: "1000m"
            memory: "2Gi"
      - queueName: io_bound
        autoscaling:
          minScale: 1
          maxScale: 5
          queueLengthTarget: 10
          pollingInterval: 30
          cooldownPeriod: 200
        env:
          - name: DJANGO_SETTINGS_MODULE
            value: "${DJANGO_SETTINGS_MODULE}"
          - name: WORKER_POOL
            value: "gevent"
          - name: CONCURRENCY
            value: "100"
          - name: ENABLE_GEVENT_PATCH
            value: "true"
        resources:
          requests:
            cpu: "1000m"
            memory: "2Gi"
      - queueName: cpu_bound
        autoscaling:
          minScale: 1
          maxScale: 5
          queueLengthTarget: 10
          pollingInterval: 30
          cooldownPeriod: 200
        env:
          - name: DJANGO_SETTINGS_MODULE
            value: "${DJANGO_SETTINGS_MODULE}"
          - name: WORKER_POOL
            value: "prefork"
          - name: CONCURRENCY
            value: "2"
          - name: ENABLE_GEVENT_PATCH
            value: "false"
        resources:
          requests:
            cpu: "2000m"
            memory: "4Gi"

```


