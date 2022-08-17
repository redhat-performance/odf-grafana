# odf-grafana
Playbook and dashboards to help with ODF performance analysis.  

This project uses Ansible, and the community grafana operator to create a dedicated Grafana instance connected to Openshift's prometheus instance. The playbook also loads any available dashboards from this project into the Grafana instance, so you don't have to start from scratch!

## Installation
before you start you'll need to satisfy the dependencies.

you will need;  
* ansible
* ansible-core
* oc (*binaries are [here](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/)*)
* pwgen

Once you have these packages/binaries in place, download this repo to your machine. The repo provides the following playbooks

| Filename | Purpose |
|----------|---------|
| deploy-grafana.yml | creates a new namespace and deploys Grafana |
| add-dashboard.yml | Load a dashboard to an existing Grafana deployment |
| purge-grafana.yml | deletes your grafana namespace (sledgehammer!)


## Usage
The `group_vars/all.yml` file defines a number of parameters that can be used to tweak a deploement, but normally you should need to change anything.


### Deploying Grafana
Use the deploy-grafana.yml playbook
```
# ansible-playbook deploy-grafana.yml
```
In less than a minute you'll have a functional Grafana instance :smile:  
At the end of the run you'll see a summary of the settings defined for the Grafana instance.

```
Deployment Summary
------------------

Grafana Details
  Namespace : mygrafana
  User      : grafana
  Password  : aih6ohGoo0aeveez
  Login URL : https://grafana-route-mygrafana.apps.my-odf.aws.myteam.org

Prometheus Datasource
  thanos URL      : https://thanos-querier-openshift-monitoring.apps.my-odf.aws.myteam.org
  Service Account : prometheus-k8s
```
Use the grafana information to login to your instance and get started! The screen capture below shows you the Prometheus data source definition and the default ODF dashboard.

![grafana UI](assets/grafana-dashboard.gif)

### Removing the Grafana deployment

Once your done with your grafana instance either delete the namespace manually to tidy things up or run the purge playbook.

```
# ansible-playbook purge-grafana.yml -e grafana-namespace=mygrafana
```

NB. When you remove your grafana configuration, remember to use `oc project default` to switch you local CLI from the old project.

### Adding a dashboard

If you need to add dashboards to your instance after a deployment, you can use the add-dashboard playbook and specify the path to the dashboard file either from the all.yml file or as an extra-vars on the playbook command line.

```
# ansible-playbook add-dashboard.yml -e dashboard_path=<insert your path here>
```
NB. For the dashboard to work, it must define it's datasources as $datasource. This is a simple way to make your dashboards portable.


## Configurations Tested

The playbooks have been tested against the following config

| Host OS | ansible | ansible-core | pwgen | oc | Cloud | Grafana operator| Grafana |
|---------|---------|--------------|-------|----|-------|---------|---|
| Fedora 36 | 5.9 | 2.12 | 2.08 | 4.11 | AWS | 4.5.1 | 9.0.7 |

## Design Notes

1. The playbook uses the `oc` command instead of the ansible `k8s` module, primarily to reduce dependencies. 
