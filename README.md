# Chart for Deploying Django, Celery and NextJS

**Pulumi Automation API Script**
https://github.com/doherty-labs/pulumi-django-celery

## Add Repo

`helm repo add doherty-labs https://doherty-labs.github.io/helm-charts/`

## Install Chart
`helm upgrade --install django-celery-api doherty-labs/django-celery-api -f ./infrastructure/helm-values.yaml --debug --wait`

## Delete Old Jobs 

```
kubectl delete -n default job collectstatic --cascade=background --ignore-not-found=true
kubectl delete -n default job django-migrate --cascade=background --ignore-not-found=true
kubectl delete -n default job elastic-migrate --cascade=background --ignore-not-found=true
kubectl delete -n default job django-create-superuser --cascade=background --ignore-not-found=true
```
