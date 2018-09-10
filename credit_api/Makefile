GOOGLE_CLOUD_PROJECT:=$(shell gcloud config list project --format="value(core.project)")

.PHONY: build
build:
	docker build -t eu.gcr.io/$(GOOGLE_CLOUD_PROJECT)/api:latest .

.PHONY: push
push: build
	docker push eu.gcr.io/$(GOOGLE_CLOUD_PROJECT)/api:latest

.PHONY: delete
delete:
	kubectl delete service api
	kubectl delete deployment api
