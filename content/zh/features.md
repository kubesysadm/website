+++
title = '功能特性'
date = 2024-07-14T18:15:06+08:00
draft = false
+++

当前Kubesysadm所具有功能特性如下所述：
- 自动优雅地重启负载

  我们知道工作负载(Deployment, statfulset, DaemaonSet)在引用了configMap/secret时，如果configMap/Secret内容变化后，它们所对应的Pod
  不会自动优雅地重新启动。这将导致对configMap/secrets的更新不能及时应用于工作负载

  kubesysadm可以根据用户配置的规则监视工作负载引用的configMaps/secrets。当Kubesysadm发现
  工作负载所引用的configMap/secret内容发生改变时，它会自动的优雅地重新启动对应的工作负载，
  
- 自动清除非运行状态的Pod
  我们知道K8S集群中没有非运行状态的pod(如Completed, Error状态)不会被自动删除。Kubesysadm可以
  根据用户设置的规则清除不运行的pod。
 
- 清除Pod规则配置
  用户可以配置清除非运行状态Pod的规则

- 监控规则配置
  用户可以配置监控规则。