---
title: Implantar contêineres com Helm no kubernetes no Azure
description: Saiba como usar a ferramenta de empacotamento Helm para implantar contêineres em um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: 27d557ab12093223450fd7bc1b88c68e1f156947
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135507"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalar aplicativos com o Helm no AKS (serviço kubernetes do Azure)

O [Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda você a instalar e gerenciar o ciclo de vida de aplicativos kubernetes. Assim como os gerenciadores de pacotes do Linux, como *apt* e *yum*, o Helm é usado para gerenciar os gráficos do kubernetes, que são pacotes de recursos kubernetes pré-configurados.

Este artigo mostra como configurar e usar o Helm em um cluster kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente. Se você precisar de um cluster AKS, consulte o guia de início rápido do AKS [usando o CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Você também precisa da CLI do Helm instalada, que é o cliente que é executado no seu sistema de desenvolvimento. Ele permite que você inicie, pare e gerencie aplicativos com o Helm. Se você usar o Azure Cloud Shell, a CLI do Helm já estará instalada. Para obter instruções de instalação em sua plataforma local, consulte Instalando o [Helm][helm-install].

> [!IMPORTANT]
> O Helm destina-se a ser executado em nós do Linux. Se você tiver nós do Windows Server em seu cluster, deverá garantir que Helm pods sejam agendadas apenas para execução em nós do Linux. Você também precisa garantir que todos os gráficos do Helm instalados também estejam agendados para execução nos nós corretos. Os comandos neste artigo usam seletores de [nó][k8s-node-selector] para garantir que os pods estejam agendados para os nós corretos, mas nem todos os gráficos Helm podem expor um seletor de nó. Você também pode considerar o uso de outras opções em seu cluster, [][taints]como os seus contras.

## <a name="create-a-service-account"></a>Criar uma conta de serviço

Antes de implantar o Helm em um cluster AKS habilitado para RBAC, você precisa de uma conta de serviço e uma associação de função para o serviço da gaveta. Para obter mais informações sobre como proteger o Helm/gaveta em um cluster habilitado para RBAC, consulte o [gaveta, namespaces e RBAC][tiller-rbac]. Se o cluster AKS não estiver habilitado para RBAC, ignore esta etapa.

Crie um arquivo chamado `helm-rbac.yaml` e copie o seguinte YAML:

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

Crie a conta de serviço e a associação de `kubectl apply` função com o comando:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Proteger o gaveta e o Helm

O cliente Helm e o serviço de gaveta se autenticam e se comunicam entre si usando TLS/SSL. Esse método de autenticação ajuda a proteger o cluster kubernetes e quais serviços podem ser implantados. Para melhorar a segurança, você pode gerar seus próprios certificados assinados. Cada usuário Helm receberia seu próprio certificado de cliente, e o gaveta seria inicializado no cluster kubernetes com certificados aplicados. Para obter mais informações, consulte [usando TLS/SSL entre Helm e o gaveta][helm-ssl].

Com um cluster kubernetes habilitado para RBAC, você pode controlar o nível de acesso que o tem ao cluster. Você pode definir o namespace kubernetes no qual o gaveta está implantado e restringir em quais namespaces o gaveta pode implantar recursos. Essa abordagem permite criar instâncias do gaveta em namespaces diferentes e limitar os limites de implantação e o escopo dos usuários do Helm Client para determinados namespaces. Para obter mais informações, consulte [controles de acesso baseado em função do Helm][helm-rbac].

## <a name="configure-helm"></a>Configurar o Helm

Para implantar um gaveta básico em um cluster AKS, use o comando [init Helm][helm-init] . Se o cluster não estiver habilitado para RBAC, remova `--service-account` o argumento e o valor. Se você configurou o TLS/SSL para o gaveta e o Helm, pule essa etapa básica de inicialização e `--tiller-tls-` forneça o necessário, conforme mostrado no exemplo a seguir.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Se você configurou o TLS/SSL entre o Helm e o `--tiller-tls-*` gaveta, forneça os parâmetros e os nomes dos seus próprios certificados, conforme mostrado no exemplo a seguir:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="find-helm-charts"></a>Localizar gráficos do Helm

Os gráficos Helm são usados para implantar aplicativos em um cluster kubernetes. Para pesquisar gráficos Helm criados previamente, use o comando [Helm Search][helm-search] :

```console
helm search
```

A seguinte saída de exemplo condensada mostra alguns dos gráficos Helm disponíveis para uso:

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

Para atualizar a lista de gráficos, use o comando de [atualização do repositório Helm][helm-repo-update] . O exemplo a seguir mostra uma atualização de repositório bem-sucedida:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Executar gráficos do Helm

Para instalar gráficos com o Helm, use o comando de [instalação do Helm][helm-install] e especifique o nome do gráfico a ser instalado. Para ver a instalação de um gráfico do Helm em ação, vamos instalar uma implantação básica do Nginx usando um gráfico do Helm. Se você configurou o TLS/SSL, `--tls` adicione o parâmetro para usar seu certificado de cliente do Helm.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A seguinte saída de exemplo condensada mostra o status de implantação dos recursos de kubernetes criados pelo gráfico Helm:

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

Leva um minuto ou dois para o endereço *IP externo* do serviço Nginx-ingress-Controller a ser populado e permitir que você o acesse com um navegador da Web.

## <a name="list-helm-releases"></a>Listar versões do Helm

Para ver uma lista de versões instaladas no cluster, use o comando [Helm List][helm-list] . O exemplo a seguir mostra a versão Nginx-ingress implantada na etapa anterior. Se você configurou o TLS/SSL, `--tls` adicione o parâmetro para usar seu certificado de cliente do Helm.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando você implanta um gráfico do Helm, vários recursos do kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, use o `helm delete` comando e especifique o nome da versão, conforme encontrado no comando anterior. `helm list` O exemplo a seguir exclui a versão chamada *flailing-alpaca*:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como gerenciar implantações de aplicativos kubernetes com o Helm, consulte a documentação do Helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
