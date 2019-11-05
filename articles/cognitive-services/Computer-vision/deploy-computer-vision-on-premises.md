---
title: Usar Pesquisa Visual Computacional contêiner com kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Implante o contêiner Pesquisa Visual Computacional em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8d285bf60e356f15caf55271b0791e9adc97ac14
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481770"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Usar Pesquisa Visual Computacional contêiner com kubernetes e Helm

Uma opção para gerenciar seus contêineres de Pesquisa Visual Computacional local é usar kubernetes e Helm. Usando kubernetes e Helm para definir uma Pesquisa Visual Computacional imagem de contêiner, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local. Por fim, exploraremos como testar os serviços implantados. Para obter mais informações sobre como executar contêineres do Docker sem orquestração kubernetes, consulte [instalar e executar pesquisa Visual computacional contêineres](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar Pesquisa Visual Computacional contêineres locais:

|Necessário|Objetivo|
|--|--|
| Conta do Azure | Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| CLI do kubernetes | A [CLI do kubernetes][kubernetes-cli] é necessária para gerenciar as credenciais compartilhadas do registro de contêiner. O kubernetes também é necessário antes de Helm, que é o Gerenciador de pacotes do kubernetes. |
| CLI do Helm | Como parte da instalação da [CLI do Helm][helm-install] , você também precisará inicializar o Helm, que será instalado no entanto. [][tiller-install] |
| Pesquisa Visual Computacional recurso |Para usar o contêiner, você deve ter:<br><br>Um recurso de **Pesquisa Visual computacional** do Azure e a chave de API associada do URI do ponto de extremidade. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{Api_key}** : uma das duas chaves de recurso disponíveis na página de **chaves**<br><br>**{ENDPOINT_URI}** : o ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos de contêiner

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao cluster kubernetes

Espera-se que o computador host tenha um cluster kubernetes disponível. Consulte este tutorial sobre como [implantar um cluster kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para obter um entendimento conceitual de como implantar um cluster kubernetes em um computador host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando as credenciais do Docker com o cluster kubernetes

Para permitir que o cluster kubernetes `docker pull` as imagens configuradas do registro de contêiner `containerpreview.azurecr.io`, você precisa transferir as credenciais do Docker para o cluster. Execute o comando [`kubectl create`][kubectl-create] abaixo para criar um *segredo do registro do Docker* com base nas credenciais fornecidas do pré-requisito de acesso ao registro de contêiner.

Na sua interface de linha de comando de escolha, execute o comando a seguir. Certifique-se de substituir o `<username>`, `<password>`e `<email-address>` pelas credenciais do registro de contêiner.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver acesso ao registro de contêiner de `containerpreview.azurecr.io`, poderá criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado em seu JSON de configuração do Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir será impressa no console quando o segredo tiver sido criado com êxito.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com o sinalizador `secrets`.

```console
kuberctl get secrets
```

A execução da `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores do gráfico Helm para implantação

Comece criando uma pasta chamada *Read*e cole o seguinte conteúdo YAML em um novo arquivo chamado *Chart. yml*.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

Para configurar os valores padrão do gráfico de Helm, copie e cole o YAML a seguir em um arquivo chamado `values.yaml`. Substitua os comentários `# {ENDPOINT_URI}` e `# {API_KEY}` pelos seus próprios valores.

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
> Se os valores de `billing` e `apikey` não forem fornecidos, os serviços expirarão após 15 minutos. Da mesma forma, a verificação falhará, pois os serviços não estarão disponíveis.

Crie uma pasta de *modelos* no diretório de *leitura* . Copie e cole o YAML a seguir em um arquivo chamado `deployment.yaml`. O arquivo de `deployment.yaml` funcionará como um modelo Helm.

> Os modelos geram arquivos de manifesto, que são descrições de recursos formatados por YAML que o kubernetes pode entender. [-Guia de modelo de gráfico de Helm][chart-template-guide]

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

O modelo especifica um serviço de balanceador de carga e a implantação do seu contêiner/imagem para leitura.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote kubernetes (gráfico Helm)

O *gráfico Helm* contém a configuração da imagem (s) do Docker a ser extraída do registro de contêiner `containerpreview.azurecr.io`.

> Um [gráfico do Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa do aplicativo Web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos Helm* fornecidos recebem as imagens do Docker do serviço pesquisa Visual computacional e o serviço correspondente do registro de contêiner `containerpreview.azurecr.io`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico Helm no cluster kubernetes

Para instalar o *gráfico do Helm*, precisaremos executar o comando [`helm install`][helm-install-cmd] . Certifique-se de executar o comando instalar do diretório acima da pasta `read`.

```console
helm install read --name read
```

Aqui está um exemplo de saída que você pode esperar ver em uma execução de instalação bem-sucedida:

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

A implantação do kubernetes pode levar mais de alguns minutos para ser concluída. Para confirmar se os pods e os serviços estão adequadamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

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

Para obter mais detalhes sobre como instalar aplicativos com o Helm no AKS (serviço kubernetes do Azure), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contêineres de serviços cognitivas][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
