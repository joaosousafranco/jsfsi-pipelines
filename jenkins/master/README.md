# JENKINS MASTER

Jenkins provides hundreds of plugins to support building, deploying and automating any project. <https://jenkins.io/>

## Latest version

```sh
latest_version=$(curl -s http://mirrors.jenkins.io/war/ | grep latest -B 1 | head -n 1 | grep -oh -e "[0-9]*\.[0-9]*" | tail -n 1)
```

## Build

```sh
container_registry="gcr.io/${project_id}"
pipelines/scripts/docker/01-build.sh jenkins_master ${latest_version} ${container_registry} $(pwd)/jenkins/master/DockerFile $(pwd)/jenkins/master
```

### Publish to gcloud

```sh
gcloud auth login
pipelines/scripts/gcloud/docker/01-publish.sh jenkins_master ${latest_version} ${project_id}
```

### Deploy to kubernetes

```sh
gcloud auth login
gcloud container clusters get-credentials --project="${project_id}" --zone="${cluster_zone:-europe-west1-b}" "${cluster_name}"
GOOGLE_APP_SECRET="" VERSION=${latest_version} pipelines/scripts/kubernetes/01-deploy.sh ${application_environment_variables_file_path} ${deployment_environment_variables_file_path}
```
