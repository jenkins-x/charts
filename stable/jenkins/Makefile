CHART_REPO := https://chartmuseum-jx.jenkins-x.live
NAME := jenkins
OS := $(shell uname)
RELEASE_VERSION := 0.10.38

setup:
	minikube addons enable ingress
	brew install kubernetes-helm
	helm init

build: clean
	rm -rf requirements.lock
	helm dependency build
	helm lint

install: clean build
	helm install . --name ${NAME}

upgrade: clean build
	helm upgrade ${NAME} .

delete:
	helm delete --purge ${NAME}

clean:
	rm -rf charts
	rm -rf ${NAME}*.tgz

release: clean
	helm dependency build
	helm lint
ifeq ($(OS),Darwin)
	sed -i "" -e "s/version:.*/version: $(RELEASE_VERSION)/" Chart.yaml
else ifeq ($(OS),Linux)
	echo "linux"
else
	exit -1
endif
	# git add Chart.yaml
	# git commit -a -m "release $(RELEASE_VERSION)"
	# git tag -fa v$(RELEASE_VERSION) -m "Release version $(RELEASE_VERSION)"
	# git push origin v$(RELEASE_VERSION)
	helm package .
	curl --fail -u $(CHARTMUSEUM_CREDS_USR):$(CHARTMUSEUM_CREDS_PSW) --data-binary "@$(NAME)-$(RELEASE_VERSION).tgz" $(CHART_REPO)/api/charts
	rm -rf ${NAME}*.tgz%
