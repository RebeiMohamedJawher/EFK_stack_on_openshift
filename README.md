# Openshift Kubernetes Logging with Fluent Bit



[Fluent Bit](http://fluentbit.io) is a lightweight and extensible __Log Processor__ that comes with full support for Kubernetes:

- Read Kubernetes/Docker log files from the file system or through systemd Journal
- Enrich logs with Kubernetes metadata
- Deliver logs to third party storage services like Elasticsearch.

This repository contains a set of Yaml files to deploy Fluent Bit which consider namespace, RBAC, Service Account, etc.

## Getting started

[Fluent Bit](http://fluentbit.io) must be deployed as a DaemonSet so that it will be available on Openshift Kubernetes cluster. To get started run the following commands to create the namespace, service account and role setup:

```
$ oc create namespace logging
$ oc create -f https://github.com/RebeiMohamedJawher/EFK_stack_on_openshift/blob/main/fluent-bit-service-account.yaml
$ oc create -f https://github.com/RebeiMohamedJawher/EFK_stack_on_openshift/blob/main/fluent-bit-role.yaml
$ oc create -f https://github.com/RebeiMohamedJawher/EFK_stack_on_openshift/blob/main/fluent-bit-role-binding.yaml
```

If you are deploying fluent-bit on openshift, you additionally need to run:

```
$ oc create -f https://github.com/RebeiMohamedJawher/EFK_stack_on_openshift/blob/main/fluent-bit-openshift-security-context-constraints.yaml
```

#### Fluent Bit to Elasticsearch

The next step is to create a ConfigMap that will be used by our Fluent Bit DaemonSet:

```
$ oc create -f https://github.com/RebeiMohamedJawher/EFK_stack_on_openshift/blob/main/output/elasticsearch/fluent-bit-configmap.yaml
```

Fluent Bit DaemonSet ready to be used with Elasticsearch on a normal Kubernetes Cluster:

```
$ oc create -f https://github.com/RebeiMohamedJawher/EFK_stack_on_openshift/blob/main/output/elasticsearch/fluent-bit-ds.yaml
```
## Details

The default configuration of Fluent Bit makes sure of the following:

- Consume all containers logs from the running Node.
- The Kubernetes filter will enrich the logs with Kubernetes metadata, specifically _labels_ and _annotations_. 
- The default backend in the configuration is Elasticsearch set by the Elasticsearch. 
- There is an option called __Retry_Limit__ set to False that means if Fluent Bit cannot flush the records to Elasticsearch it will re-try indefinitely until it succeeds.
