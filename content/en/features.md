+++
title = 'Features'
date = 2024-07-14T17:01:58+08:00
draft = false
+++

Now the features of kubesysadm described as the following:
- Automatically restart the workload gracefully

  we know that the workload(Deployment, statefulSet, DaemaonSet) does not automatically restart gracefully when the configMap/secret
  referenced by the workload changes. This results in updates to configMap not being applied to the workload in a timely manner.

  kubesysadm can monitor the configMaps/secrets referenced by workloads based on user-configured rules.  Kubesysadm will restart
  the workload gracefully when it finds the configMap/secret referenced by the workload has changed.

- Automatically cleaning no-running Pods
  We know that no-running(Such as Completed, Error) Pods in K8S cluster are not be deleted automatically. Kubesysadm can
  delete the no-running pods according to the rules which be configured by user.

- Pod cleaning rules configuration
  User can configure the rules for deleting no-running

- Monitoring rule configuration
  users can configure the monitoring rules.
