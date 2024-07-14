+++
title = 'Quick Start'
date = 2024-07-14T18:29:44+08:00
draft = false
+++

### Prerequisites
- kubectl version v1.12+ with CRD support.
- Access to a Kubernetes v1.12+ cluster.

### Install CRD and instance of Kubesysadm into  cluster
Install Kubesysadm on an existing Kubernetes cluster. This way is both available for x86_64 and arm64 architecture.
```
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/install.yaml
```

Enjoy! Kubesysadm will create the following resources in the cluster
```
namespace/kubesysadm-system created
customresourcedefinition.apiextensions.k8s.io/cmmonitors.monitoring.sysadm.cn created
customresourcedefinition.apiextensions.k8s.io/podcleanrules.monitoring.sysadm.cn created
serviceaccount/kubesysadm-controller-manager created
role.rbac.authorization.k8s.io/kubesysadm-leader-election-role created
clusterrole.rbac.authorization.k8s.io/kubesysadm-cmmonitor-editor-role created
clusterrole.rbac.authorization.k8s.io/kubesysadm-cmmonitor-viewer-role created
clusterrole.rbac.authorization.k8s.io/kubesysadm-manager-role created
clusterrole.rbac.authorization.k8s.io/kubesysadm-metrics-reader created
clusterrole.rbac.authorization.k8s.io/kubesysadm-proxy-role created
rolebinding.rbac.authorization.k8s.io/kubesysadm-leader-election-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/kubesysadm-manager-rolebinding created
clusterrolebinding.rbac.authorization.k8s.io/kubesysadm-proxy-rolebinding created
service/kubesysadm-controller-manager-metrics-service created
deployment.apps/kubesysadm-controller-manager created
```
Check whether kubesysadm controller is running by the following command
```
kubectl get po -n kubesysadm-system
```

The output of the above command like the following showing
```
NAME                                             READY   STATUS    RESTARTS   AGE
kubesysadm-controller-manager-5f78865594-b6gsc   2/2     Running   0          2m30s
```

### Configure configMap monitoring rules
In the following example we will to do the following things:
- Create a namespace named test-kubesysadm
- Create a configMap named cm-env in test-kubesysadm namespace
- Create a configMap named cm-mount in test-kubesysadm namespace
- Create configMap monitoring rules named cm-env and cm-mount
- Create a deployment named test-kubesysadm in test-kubesysadm namespace which using cm-env and cm-mount configMap

We create the above resource by the following commands:
```azure
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/create_ns.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cm-env.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cm-mount.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cmmonitor_cm-env.yaml    
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cmmonitor_cm-mount.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/deploy.yaml
```

Now we try to check the results
We get the pods' status by the following command
```azure
  kubectl get po -n test-kubesysadm
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_started.png"/>

Then we try to change cm-env/cm-mount configMap by the following command. After that, we will find that the pods of
deployment test-kubesysadm will be restared like the following image shown.
```azure
  kubectl edit cm -n test-kubesysadm cm-env/cm-mount 
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_restart.png"/>

### Configure secret monitoring rules
Like configMap, in the following example we will to do the following things:
- Create a namespace named test-kubesysadm
- Create a secret named secret-env in test-kubesysadm namespace
- Create a secret named secret-mount in test-kubesysadm namespace
- Create secret monitoring rules named secret-env and secret-mount
- Create a deployment named test-kubesysadm in test-kubesysadm namespace which using secret-env and secret-mount secret

We create the above resource by the following commands:
```azure
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/create_ns.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/secret_env.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/secret_mount.yaml 
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/cmmonitor_secret-env.yaml    
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/cmmonitor_secret-mount.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/deploy.yaml
```

Now we try to check the results
We get the pods' status by the following command
```azure
  kubectl get po -n test-kubesysadm
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_started.png"/>

Then we try to change secret-env/secret-mount secret by the following command. After that, we will find that the pods of
deployment test-kubesysadm will be restared like the following image shown.
```azure
  kubectl edit secret -n test-kubesysadm secret-env/secret-mount
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_restart.png"/>


### Configure Pod Cleaning rules
In the following example we will to do the following things:
- Create a namespace named test-kubesysadm
- Create a Job named job1
- Create a PodCleanRule named cleanpods. The age value of rule is 300 and namespace is test-kubesysadm. That meaning is
  PodCleanManager will clean the pods which created before 5 minutes and in no-running/no-pending status.

We create the above resource by the following commands:
```azure
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/podclean/create_ns.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/podclean/podcleanrule.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/podclean/job.yaml
```

Now we try to check the results
We get the pods' status by the following command
```azure
  kubectl get po -n test-kubesysadm
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/job_pods.png"/>

We found that all pods in "Completed" status in test-kubesysadm namespace have be deleted after 5 minutes when we re-run
the above command. And we get the log message like the following image shown when we run the following command.
```azure
   kubectl logs -n kubesysadm-system kubesysadm-controller-manager-5f78865594-b6gsc
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/podcleanmessage.png"/>
