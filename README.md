# !! Disclaimer

This project is built on top of documents obtained from this public project: [waprin/kubernetes_django_postgres_redis](https://github.com/waprin/kubernetes_django_postgres_redis). I've merely adapted the code base to work with my own setup. I intend to change and remove this repo once I'm done playing around with it.

The following is documentation for my own edification and simplification of the process described so eloquantly by `waprin` in the above link.

## Revised process (after everything is setup to work right)

### Working with Minikube

To complete this section on the minikube commands for local development - haven't touched this yet.

### Working with K8s

Small random note - the reason it's k8s: there are 8 characters between the k and s in Kubernetes. Hence k8s. 

#### Jinja Templates

This project uses the [jinja 2 CLI](https://github.com/mattrobenolt/jinja2-cli)
to share templates between the GKE config and Minikube config.

`cd` into the project root and run:

```pip install -r requirements-dev.txt```

to install the CLI. At that point, you can see `minikube_jinja.json` and
`gke_jinja.json` as examples of variables you need to poplate to generate the
templates.

Run

```make template```

which will use the json variables to create the templates. These template-created yaml files are what we'll need to apply configs to the containers. 

#### Container Engine Pre-requisites

1. Install [Docker](https://www.docker.com/).

1. Create a project in the [Google Cloud Platform Console](https://console.cloud.google.com).

1. [Enable billing](https://console.cloud.google.com/project/_/settings) for your project.

1. [Enable APIs](https://console.cloud.google.com/flows/enableapi?apiid=compute_component,datastore,pubsub,storage_api,logging,plus)
for your project. The provided link will enable all necessary APIs, but if you wish to do so manually you will need
Compute, Datastore, Pub/Sub, Storage, and Logging. Note: enabling the APIs can take a few minutes.

1. [Initialise the Container Engine for the project](https://console.cloud.google.com/kubernetes/list)

1. If on OSX or Linux then install the [Google Cloud SDK](https://cloud.google.com/sdk):

```curl https://sdk.cloud.google.com | bash```

1. (Re-)Initialise the settings to set the compute zone:

```gcloud init```

1. Authenticate the CLI:

```gcloud auth application-default login```

1. `cd` into project root and run

```make create-cluster```

#### Run the project locally

1. `cd` into the application folder (api/credit_api)

1. Run ```pip install -r requirements.txt```

1. Run ```export NODB=1```

1. Run ```python manage.py runserver```

1. Close the application session once done. 

#### Deploying to GKE

Overarching steps are: apply redis.yaml, build and push postgres and apply postgres.yaml, build and push and apply project.yaml (api_gke.yml in this case).

First - Postgres
==================
in project root -- ```make disk```
cd kubernetes_configs/postgres/postgres_image
make build make push
cd ..
kubectl apply -f postgres_gke.yaml

Then - Redis
=============================

1. `cd` into project root and run ```make redis```

That should be it. Check if this is so: ```kubectl get pods``` You should see a `redis-master-####` and 2 `redis-slave-####` pods.

Last - Project specific
==================
cd credit_api
make build make push
cd kubernetes_configs/api
kubectl apply -f api_gke.yaml

Check error logs on console.google.cloud
========================================

## TODO

- [ ] Resolve frontend replica controllers that are missing. 
- [ ] Copy and adapt api_gke.yaml.jinja file --> frontend_gke.yaml.jijna + load_test_gke.yaml.jinja, using the .yaml.tmpl file contents for each respective file. 
- [ ] Check that everything is actually deploying as expected and that the application is accessible from a browser/curl command.
