---
title: Instale aplicações existentes com helm em AKS
description: Aprenda a usar a ferramenta de embalagem Helm para implantar contentores num cluster azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: e46bed5fc9fd83a907f8c9e716317a54548c58cc
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870256"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instale aplicações existentes com helm no Serviço Azure Kubernetes (AKS)

[O Helm][helm] é uma ferramenta de embalagem de código aberto que o ajuda a instalar e gerir o ciclo de vida das aplicações kubernetes. Semelhante aos gestores de pacotes Linux, como *apt* e *Yum,* Helm é usado para gerir gráficos Kubernetes, que são pacotes de recursos kubernetes pré-reconfigurados.

Este artigo mostra-lhe como configurar e usar helm num cluster Kubernetes em AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que você tem um aglomerado AKS existente. Se precisar de um cluster AKS, consulte o quickstart AKS [utilizando o Azure CLI][aks-quickstart-cli] ou [utilizando o portal Azure][aks-quickstart-portal].

Também precisa do Helm CLI instalado, que é o cliente que funciona no seu sistema de desenvolvimento. Permite-lhe começar, parar e gerir aplicações com o Helm. Se utilizar a Casca de Nuvem Azure, o HELM CLI já está instalado. Para obter instruções de instalação na sua plataforma local, consulte [o Comando de Instalação][helm-install].

> [!IMPORTANT]
> O leme destina-se a correr com os nódosos Linux. Se tiver nós do Windows Server no seu cluster, tem de garantir que as cápsulas Helm estão programadas apenas para serem executadas nos nós linux. Também precisa de garantir que quaisquer gráficos helm que instale também estão programados para serem executados nos nódosos corretos. Os comandos deste artigo utilizam [os seletores de nó][k8s-node-selector] para se certificarem de que as cápsulas estão programadas para os nós corretos, mas nem todas as tabelas helm podem expor um seletor de nó. Também pode considerar a utilização de outras opções no seu cluster, tais como [manchas.][taints]

## <a name="verify-your-version-of-helm"></a>Verifique a sua versão de Helm

Utilize `helm version` o comando para verificar a versão do Helm que instalou:

```console
helm version
```

O exemplo seguinte mostra a versão Helm 3.0.0 instalada:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

Para o Helm v3, siga os passos na [secção Helm v3](#install-an-application-with-helm-v3). Para helm v2, siga os passos na [secção Helm v2](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Instale uma aplicação com helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Adicione o repositório oficial de gráficos estáveis Helm

Use o comando de [repo de leme][helm-repo-add] para adicionar o repositório oficial de gráficos estáveis Helm.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Encontre gráficos helm

Os gráficos de leme são usados para implantar aplicações num cluster kubernetes. Para procurar gráficos Helm pré-criados, utilize o comando de pesquisa do [leme:][helm-search]

```console
helm search repo stable
```

A saída de exemplo condensado seguinte mostra alguns dos gráficos Helm disponíveis para utilização:


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Para atualizar a lista de gráficos, utilize o comando de [atualização do helm repo.][helm-repo-update] O exemplo seguinte mostra uma atualização de repo bem sucedida:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Executar gráficos helm

Para instalar gráficos com o Helm, utilize o comando de [instalação][helm-install-command] do leme e especifique um nome de lançamento e o nome do gráfico para instalar. Para ver a instalação de um gráfico Helm em ação, vamos instalar uma implementação básica de nginx usando um gráfico Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A saída de exemplo condensado seguinte mostra o estado de implantação dos recursos Kubernetes criados pela tabela Helm:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Utilize `kubectl get services` o comando para obter o *EXTERNAL-IP* do seu serviço. Por exemplo, o comando abaixo mostra o *IP EXTERNO* para o serviço de *controlador my-nginx-ingress:*

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Lançamentos de listas

Para ver uma lista de lançamentos instalados `helm list` no seu cluster, utilize o comando.

```console
helm list
```

O exemplo seguinte mostra a libertação *my-nginx-ingress* implantada no passo anterior:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Limpar recursos

Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, utilize o comando desinstalação do [leme][helm-cleanup] e especifique o seu nome de lançamento, conforme encontrado no comando anterior. `helm list`

```console
helm uninstall my-nginx-ingress
```

O seguinte exemplo mostra que a versão denominada *my-nginx-ingress* foi desinstalada:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Instale uma aplicação com helm v2

### <a name="create-a-service-account"></a>Criar uma conta de serviço

Antes de poder implantar o Helm num cluster AKS ativado pelo RBAC, precisa de uma conta de serviço e de uma ligação de funções para o serviço Tiller. Para obter mais informações sobre a segurança do Leme / Tiller num cluster ativado por RBAC, consulte [Tiller, Namespaces e RBAC][tiller-rbac]. Se o seu cluster AKS não estiver ativado por RBAC, ignore este passo.

Criar um `helm-rbac.yaml` ficheiro nomeado e copiar no seguinte YAML:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Criar a conta de serviço `kubectl apply` e a vinculação de funções com o comando:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Secure Tiller e Helm

O cliente Helm e o serviço Tiller autenticam e comunicam entre si usando tLS/SSL. Este método de autenticação ajuda a proteger o cluster Kubernetes e que serviços podem ser implementados. Para melhorar a segurança, pode gerar os seus próprios certificados assinados. Cada utilizador helm receberia o seu próprio certificado de cliente, e tiller seria inicializado no cluster Kubernetes com certificados aplicados. Para mais informações, consulte [A Utilização de TLS/SSL entre o Leme e][helm2-ssl]o Leme .

Com um cluster Kubernetes ativado por RBAC, pode controlar o nível de acesso que tiller tem para o cluster. Você pode definir o espaço de nome Kubernetes em que Tiller é implantado, e restringir em que espaços de nome Tiller pode então implementar recursos. Esta abordagem permite criar instâncias Tiller em diferentes espaços de nome e limitar os limites de implementação, e examinar os utilizadores do cliente Helm para determinados espaços de nome. Para obter mais informações, consulte [os controlos de acesso baseados em papéis helm][helm2-rbac].

### <a name="configure-helm"></a>Configure Helm

Para implantar um Tiller básico num aglomerado AKS, use o comando de entrada do [leme.][helm2-init] Se o seu cluster não estiver `--service-account` ativado, remova o argumento e o valor. Os exemplos que se seguem também definiram o [histórico-max][helm2-history-max] para 200.

Se configurar TLS/SSL para Tiller e Helm, ignore este passo `--tiller-tls-` básico de inicialização e, em vez disso, forneça o necessário como mostrado no exemplo seguinte.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Se configurar TLS/SSL entre Helm e `--tiller-tls-*` Tiller fornecer os parâmetros e nomes dos seus próprios certificados, como mostra o seguinte exemplo:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Encontre gráficos helm

Os gráficos de leme são usados para implantar aplicações num cluster kubernetes. Para procurar gráficos Helm pré-criados, utilize o comando de pesquisa do [leme:][helm2-search]

```console
helm search
```

A saída de exemplo condensado seguinte mostra alguns dos gráficos Helm disponíveis para utilização:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Para atualizar a lista de gráficos, utilize o comando de [atualização do helm repo.][helm2-repo-update] O exemplo seguinte mostra uma atualização de repo bem sucedida:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Executar gráficos helm

Para instalar gráficos com o Helm, utilize o comando de [instalação][helm2-install-command] do leme e especifique o nome do gráfico para instalar. Para ver a instalação de um gráfico Helm em ação, vamos instalar uma implementação básica de nginx usando um gráfico Helm. Se configurar tLS/SSL, `--tls` adicione o parâmetro para utilizar o certificado de cliente Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A saída de exemplo condensado seguinte mostra o estado de implantação dos recursos Kubernetes criados pela tabela Helm:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

Demora um minuto ou dois para que o endereço *EXTERNO-IP* do serviço de controlador nginx-ingress seja povoado e permita-lhe aceder-lhe com um navegador web.

### <a name="list-helm-releases"></a>Lançamentos da Lista Helm

Para ver uma lista de lançamentos instalados no seu cluster, utilize o comando da [lista de leme.][helm2-list] O exemplo seguinte mostra a libertação nginx-ingress implantada no passo anterior. Se configurar tLS/SSL, `--tls` adicione o parâmetro para utilizar o certificado de cliente Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Limpar recursos

Quando se implanta um gráfico Helm, são criados vários recursos kubernetes. Estes recursos incluem cápsulas, implantações e serviços. Para limpar estes recursos, `helm delete` use o comando e especifique o seu nome de libertação, conforme encontrado no comando anterior. `helm list` O exemplo seguinte elimina a versão denominada *flailing-alpaca:*

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a gestão das implementações de aplicações da Kubernetes com o Helm, consulte a documentação Helm.

> [!div class="nextstepaction"]
> [Documentação do leme][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
