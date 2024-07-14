+++
title = '快速开始'
date = 2024-07-14T18:29:48+08:00
draft = false
+++

### 前置条件
- kubectl version v1.12+ with CRD support.
- Access to a Kubernetes v1.12+ cluster.

### 安装Kubernetes的CRD实例到K8S集群 
在已有的Kubernetes集群上安装Kubesysadm。这种方式对x86_64和arm64体系结构都可用。
```
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/install.yaml
```

上述命令将会在集群内创建如下资源。
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
使用下列命令检查kubesysadm 控制器是否已正常运行
```
kubectl get po -n kubesysadm-system
```

上述命令输出类似如下内容：
```
NAME                                             READY   STATUS    RESTARTS   AGE
kubesysadm-controller-manager-5f78865594-b6gsc   2/2     Running   0          2m30s
```

###  配置configMap监控规则
在下面的例子中，我们将做以下事情:
- 创建一个名字为test-kubesysadm的命名空间
- 在test-kubesysadm命名空间下创建一个名字叫cm-env的configMap。
- 在test-kubesysadm命名空间下创建一个名字叫cm-mount的configMap。 
- 分别创建名字为cm-env和cm-mount的configMap监控规则。 
- 在test-kubesysadm创建一个名字为test-kubesysadm的deployment,其使用了cm-env和cm-mount configMap

通过下列命令创建上述资源:
```azure
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/create_ns.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cm-env.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cm-mount.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cmmonitor_cm-env.yaml    
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/cmmonitor_cm-mount.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/configMap/deploy.yaml
```

通过下列命令查看Pod的运行状态：
```azure
  kubectl get po -n test-kubesysadm
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_started.png"/>

然后，我们尝试通过下列命令来修改cm-env/cm-mount configMap的内容，之后我们可以看到deployment test-kubesysadm的Pod将会被重启，如下图所示：
```azure
  kubectl edit cm -n test-kubesysadm cm-env/cm-mount 
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_restart.png"/>

### 配置secret监控进则
与configMap类似, 在下面的例子中，我们将做以下事情:
- 创建一个名字为test-kubesysadm的命名空间
- 在test-kubesysadm命名空间下创建一个名字叫secret-env的secret。
- 在test-kubesysadm命名空间下创建一个名字叫secret-mount的secret
- 分别创建名字为secret-env和secret-mount的secret监控规则
- 在test-kubesysadm创建一个名字为test-kubesysadm的deployment,其使用了secret-env和secret-mount secret

通过下列命令创建上述资源:
```azure
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/create_ns.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/secret_env.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/secret_mount.yaml 
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/cmmonitor_secret-env.yaml    
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/cmmonitor_secret-mount.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/secret/deploy.yaml
```

通过下列命令查看Pod的运行状态：
```azure
  kubectl get po -n test-kubesysadm
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_started.png"/>

然后，我们尝试通过下列命令来修改secret-env/secret-mount secret的内容，之后我们可以看到deployment test-kubesysadm的Pod将会被重启，如下图所示：
```azure
  kubectl edit secret -n test-kubesysadm secret-env/secret-mount
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/mornitoring_cm_restart.png"/>


### 配置Pod Cleaning 规则Configure Pod Cleaning rules
在下面的例子中，我们将做以下事情:
- 创建一个名字为test-kubesysadm的命名空间
- 在test-kubesysadm命名空间下创建一个名字为job1的Job
- 创建一个名称为cleanpods的Pod Cleaning规则。规则的age字段值为300， namespace为test-kubesysadm.这条规则的含义是
  PodCleanManager将清除创建于5分钟前，且处于非Running和非Pending状态的Pod.

通过下列命令查看Pod的运行状态：
```azure
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/podclean/create_ns.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/podclean/podcleanrule.yaml
  kubectl apply -f https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/installer/podclean/job.yaml
```

通过下列命令检查结果。
```azure
  kubectl get po -n test-kubesysadm
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/job_pods.png"/>

当我们重新运行时，我们发现test-kubesysadm命名空间中所有状态为"Completed"的pod在5分钟后被删除了
```azure
   kubectl logs -n kubesysadm-system kubesysadm-controller-manager-5f78865594-b6gsc
```

<img src="https://raw.githubusercontent.com/kubesysadm/kubesysadm/main/docs/images/podcleanmessage.png"/>