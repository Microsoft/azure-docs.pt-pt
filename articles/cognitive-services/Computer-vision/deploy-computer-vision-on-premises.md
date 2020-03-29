---
title: Utilize o recipiente de visão computacional com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente Computer Vision para uma instância de contentores Azure e teste-o num navegador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 126060875c09d70b8680447d78b7cf6ccdd782af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458023"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Utilize o recipiente de visão computacional com Kubernetes e Helm

Uma opção para gerir os seus recipientes Computer Vision no local é utilizar Kubernetes e Helm. Usando Kubernetes e Helm para definir uma imagem de recipiente computer vision, vamos criar um pacote Kubernetes. Este pacote será implantado num aglomerado kubernetes no local. Finalmente, vamos explorar como testar os serviços implantados. Para obter mais informações sobre a execução de contentores Docker sem orquestração Kubernetes, consulte [instalar e executar recipientes Computer Vision](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de utilizar em instalações recipientes computer vision:

| Necessário | Objetivo |
|----------|---------|
| Conta do Azure | Se não tiver uma subscrição Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Kubernetes CLI | O [Kubernetes CLI][kubernetes-cli] é necessário para gerir as credenciais partilhadas do registo de contentores. Kubernetes também é necessário antes de Helm, que é o gestor de pacotes Kubernetes. |
| Helm CLI | Instale o [HELM CLI,][helm-install]que é utilizado para instalar um gráfico de leme (definição de pacote de recipientes). |
| Recurso de Visão Computacional |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure **Computer Vision** e a chave API associada ao ponto final URI. Ambos os valores estão disponíveis nas páginas Overview e Keys para o recurso e são necessários para iniciar o recipiente.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **'Visão Geral'**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações de contentores

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ligue-se ao cluster Kubernetes

Espera-se que o computador anfitrião tenha um cluster Kubernetes disponível. Consulte este tutorial sobre [a implementação de um cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceptual de como implantar um cluster Kubernetes para um computador hospedeiro.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Partilhar credenciais do Docker com o cluster Kubernetes

Para permitir que o cluster `docker pull` Kubernetes se transforme nas imagens configuradas do `containerpreview.azurecr.io` registo do contentor, é necessário transferir as credenciais de estivador para o cluster. Execute [`kubectl create`][kubectl-create] o comando abaixo para criar um *segredo de registo de estivadores* com base nas credenciais fornecidas pelo pré-requisito de acesso ao registo do contentor.

A partir da interface de escolha da linha de comando, execute o seguinte comando. Certifique-se de `<username>` `<password>`substituir `<email-address>` as credenciais de registo de contentores.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se já tiver acesso `containerpreview.azurecr.io` ao registo de contentores, poderá criar um segredo kubernetes usando a bandeira genérica. Considere o seguinte comando que executa contra a sua configuração Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída seguinte é impressa na consola quando o segredo foi criado com sucesso.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi [`kubectl get`][kubectl-get] criado, execute o com a `secrets` bandeira.

```console
kubectl get secrets
```

Executando `kubectl get secrets` as impressões digitais todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configure os valores do gráfico helm para implementação

Comece por criar uma pasta chamada *read*, em seguida, cole o seguinte conteúdo YAML em um novo ficheiro chamado *Chart.yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Para configurar os valores predefinidos do gráfico Helm, copie e cole o seguinte YAML num ficheiro nomeado `values.yaml`. Substitua `# {ENDPOINT_URI}` `# {API_KEY}` os comentários e comentários pelos seus próprios valores.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se `billing` os `apikey` valores e valores não forem fornecidos, os serviços expirarão após 15 min. Da mesma forma, a verificação falhará, uma vez que os serviços não estarão disponíveis.

Crie uma pasta de *modelos* sob o diretório *de leitura.* Copie e cole o seguinte YAML num ficheiro chamado `deployment.yaml`. O `deployment.yaml` ficheiro servirá como modelo helm.

> Os modelos geram ficheiros manifestos, que são descrições de recursos formados por YAML que kubernetes podem entender. [- Guia de modelo de gráfico de leme][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

O modelo especifica um serviço de equilíbrio de carga e a implantação do seu recipiente/imagem para Read.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de que `containerpreview.azurecr.io` imagens de estivador retirar do registo do recipiente.

> Um [gráfico helm][helm-charts] é uma coleção de ficheiros que descrevem um conjunto relacionado de recursos kubernetes. Um único gráfico pode ser usado para implementar algo simples, como um casulo memcached, ou algo complexo, como uma pilha de aplicativos web completo com servidores HTTP, bases de dados, caches, e assim por diante.

As *tabelas helm* fornecidas retiram as imagens do estivador do Serviço de Visão Computacional e o serviço correspondente do registo de `containerpreview.azurecr.io` contentores.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instale o gráfico Helm no cluster Kubernetes

Para instalar o gráfico de *leme,* [`helm install`][helm-install-cmd] precisamos executar o comando. Certifique-se de executar o comando `read` de instalação a partir do diretório acima da pasta.

```console
helm install read ./read
```

Aqui está um exemplo de saída que você pode esperar ver de uma execução de instalação bem sucedida:

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

A implantação de Kubernetes pode demorar vários minutos a ser concluída. Para confirmar que tanto as cápsulas como os serviços estão devidamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Deve esperar ver algo semelhante à seguinte saída:

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passos seguintes

Para mais detalhes sobre a instalação de aplicações com helm in Azure Kubernetes Service (AKS), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Recipientes de Serviços Cognitivos][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
