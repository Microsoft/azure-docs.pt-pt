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
ms.date: 8/22/2019
ms.author: dapine
ms.openlocfilehash: 1627aea958707eaaef6ee79908a17afc2e8f7b45
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068972"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Usar Pesquisa Visual Computacional contêiner com kubernetes e Helm

Uma opção para gerenciar seus contêineres de Pesquisa Visual Computacional local é usar kubernetes e Helm. Usando kubernetes e Helm para definir a imagem de contêiner Reconhecimento de Texto, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local. Por fim, exploraremos como testar os serviços implantados. Para obter mais informações sobre como executar contêineres do Docker sem orquestração kubernetes, consulte [instalar e executar reconhecimento de texto contêineres](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar Pesquisa Visual Computacional contêineres locais:

|Requerido|Objetivo|
|--|--|
| Conta do Azure | Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Acesso ao registro de contêiner | Para que o kubernetes pegue as imagens do Docker no cluster, ele precisará de acesso ao registro de contêiner. Você precisa [solicitar o acesso ao registro de contêiner][vision-preview-access] primeiro. |
| CLI do kubernetes | A [CLI do kubernetes][kubernetes-cli] é necessária para gerenciar as credenciais compartilhadas do registro de contêiner. O kubernetes também é necessário antes de Helm, que é o Gerenciador de pacotes do kubernetes. |
| CLI do Helm | Como parte da instalação da [CLI do Helm][helm-install] , você também precisará inicializar o Helm, que será [][tiller-install]instalado no entanto. |
| Pesquisa Visual Computacional recurso |Para usar o contêiner, você deve ter:<br><br>Um recurso de **Pesquisa Visual computacional** do Azure e a chave de API associada do URI do ponto de extremidade. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : Uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao cluster kubernetes

Espera-se que o computador host tenha um cluster kubernetes disponível. Consulte este tutorial sobre como [implantar um cluster kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para obter um entendimento conceitual de como implantar um cluster kubernetes em um computador host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando as credenciais do Docker com o cluster kubernetes

Para permitir que o cluster kubernetes `docker pull` para as imagens configuradas `containerpreview.azurecr.io` do registro de contêiner, você precisa transferir as credenciais do Docker para o cluster. Execute o [`kubectl create`][kubectl-create] comando a seguir para criar um *segredo do registro do Docker* com base nas credenciais fornecidas do pré-requisito de acesso ao registro de contêiner.

Na sua interface de linha de comando de escolha, execute o comando a seguir. Certifique-se de substituir `<username>`o `<password>`, e `<email-address>` pelas credenciais de registro de contêiner.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver acesso ao `containerpreview.azurecr.io` registro de contêiner, poderá criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado em seu JSON de configuração do Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir será impressa no console quando o segredo tiver sido criado com êxito.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com o `secrets` sinalizador.

```console
kuberctl get secrets
```

Executar o `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores do gráfico Helm para implantação

Comece criando uma pasta chamada *Text-* Recognizer, copie e cole o seguinte conteúdo YAML em um novo arquivo chamado `Chart.yml`.

```yaml
apiVersion: v1
name: text-recognizer
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-recognize-text to a Kubernetes cluster
```

Para configurar os valores padrão do gráfico de Helm, copie e cole o seguinte YAML em um `values.yaml`arquivo chamado. Substitua os `# {ENDPOINT_URI}` comentários `# {API_KEY}` e pelos seus próprios valores.

```yaml
# These settings are deployment specific and users can provide customizations

recognizeText:
  enabled: true
  image:
    name: cognitive-services-recognize-text
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-recognize-text
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se os `billing` valores `apikey` e não forem fornecidos, os serviços expirarão após 15 minutos. Da mesma forma, a verificação falhará, pois os serviços não estarão disponíveis.

Crie uma pasta de *modelos* no diretório de *reconhecimento de texto* . Copie e cole o YAML a seguir em um arquivo `deployment.yaml`chamado. O `deployment.yaml` arquivo funcionará como um modelo Helm.

> Os modelos geram arquivos de manifesto, que são descrições de recursos formatados por YAML que o kubernetes pode entender. [-Guia de modelo de gráfico de Helm][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: text-recognizer
spec:
  template:
    metadata:
      labels:
        app: text-recognizer-app
    spec:
      containers:
      - name: {{.Values.recognizeText.image.name}}
        image: {{.Values.recognizeText.image.registry}}{{.Values.recognizeText.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.recognizeText.image.args.eula}}
        - name: billing
          value: {{.Values.recognizeText.image.args.billing}}
        - name: apikey
          value: {{.Values.recognizeText.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.recognizeText.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: text-recognizer
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: text-recognizer-app
```

O modelo especifica um serviço de balanceador de carga e a implantação do seu contêiner/imagem para reconhecimento de texto.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote kubernetes (gráfico Helm)

O *gráfico Helm* contém a configuração da imagem (s) do Docker a ser extraída `containerpreview.azurecr.io` do registro de contêiner.

> Um [gráfico do Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa do aplicativo Web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos Helm* fornecidos puxam as imagens do Docker do serviço pesquisa Visual computacional e os serviços de texto Recognize `containerpreview.azurecr.io` do registro de contêiner.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico Helm no cluster kubernetes

Para instalar o *gráfico do Helm*, precisaremos executar o [`helm install`][helm-install-cmd] comando. Certifique-se de executar o comando instalar do diretório acima `text-recognizer` da pasta.

```console
helm install text-recognizer --name text-recognizer
```

Aqui está um exemplo de saída que você pode esperar ver em uma execução de instalação bem-sucedida:

```console
NAME:   text-recognizer
LAST DEPLOYED: Thu Aug 22 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                              READY  STATUS             RESTARTS  AGE
text-recognizer-57cb76bcf7-45sdh  0/1    ContainerCreating  0         0s

==> v1/Service
NAME             TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
text-recognizer  LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME             READY  UP-TO-DATE  AVAILABLE  AGE
text-recognizer  0/1    1           0          0s
```

A implantação do kubernetes pode levar mais de alguns minutos para ser concluída. Para confirmar se os pods e os serviços estão adequadamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

```console
λ kubectl get all
NAME                                   READY   STATUS    RESTARTS   AGE
pod/text-recognizer-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes        ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/text-recognizer   LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                              READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/text-recognizer   1/1     1            1           17s

NAME                                         DESIRED   CURRENT   READY   AGE
replicaset.apps/text-recognizer-57cb76bcf7   1         1         1       17s
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
[vision-preview-access]: computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
