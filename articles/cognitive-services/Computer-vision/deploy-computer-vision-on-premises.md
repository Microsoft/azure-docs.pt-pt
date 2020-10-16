---
title: Use o recipiente de visão de computador com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Saiba como implantar o recipiente de Visão de Computador utilizando Kubernetes e Helm.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 9a8e0dde8b24c39180a584c26af725ab82ea0176
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907102"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Use o recipiente de visão de computador com Kubernetes e Helm

Uma opção para gerir os seus contentores de Visão de Computador no local é utilizar Kubernetes e Helm. Usando Kubernetes e Helm para definir uma imagem de recipiente de Visão de Computador, criaremos um pacote Kubernetes. Este pacote será implantado num cluster kubernetes no local. Finalmente, vamos explorar como testar os serviços implantados. Para obter mais informações sobre a execução de recipientes Docker sem orquestração kubernetes, consulte [instalar e executar recipientes de Visão de Computador](computer-vision-how-to-install-containers.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes da utilização de recipientes de visão computacional no local:

| Necessário | Objetivo |
|----------|---------|
| Conta do Azure | Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Kubernetes CLI | O [CLI de Kubernetes][kubernetes-cli] é necessário para gerir as credenciais partilhadas do registo do contentor. Kubernetes também é necessário antes de Helm, que é o gestor de pacotes Kubernetes. |
| Helm CLI | Instale o [Helm CLI,][helm-install]que é utilizado para instalar um cartão de leme (definição de embalagem de recipiente). |
| Recurso de Visão Computacional |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure **Computer Vision** e a chave API associada o ponto final URI. Ambos os valores estão disponíveis nas páginas 'Visão Geral' e Chaves para o recurso e são obrigados a iniciar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página **'Vista Geral',**|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ligue-se ao cluster Kubernetes

Espera-se que o computador anfitrião tenha um cluster Kubernetes disponível. Veja este tutorial sobre [a implementação de um cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceptual de como implantar um cluster Kubernetes num computador anfitrião. Pode encontrar mais informações sobre implementações na [documentação de Kubernetes.](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Partilhar credenciais de Docker com o cluster Kubernetes

Para permitir que o cluster Kubernetes seja `docker pull` configurado a partir do registo do `containerpreview.azurecr.io` contentor, é necessário transferir as credenciais de estivador para o cluster. Execute o [`kubectl create`][kubectl-create] comando abaixo para criar um *segredo de registo de estivadores* com base nas credenciais fornecidas a partir do pré-requisito de acesso ao registo do contentor.

A partir da sua interface de linha de comando de eleição, executar o seguinte comando. Certifique-se de que substitui as `<username>` `<password>` credenciais de `<email-address>` registo de contentores e de contentores.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se já tiver acesso ao registo do `containerpreview.azurecr.io` contentor, pode criar um segredo de Kubernetes usando a bandeira genérica. Considere o seguinte comando que executa contra a configuração do Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A seguinte saída é impressa na consola quando o segredo foi criado com sucesso.

```console
secret "containerpreview" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com a `secrets` bandeira.

```console
kubectl get secrets
```

Executando as `kubectl get secrets` impressões digitais todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores de gráfico de leme para implantação

Comece por criar uma pasta chamada *leitura*. Em seguida, cole o seguinte conteúdo YAML num novo ficheiro chamado `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
dependencies:
- name: rabbitmq
  condition: read.image.args.rabbitmq.enabled
  version: ^6.12.0
  repository: https://kubernetes-charts.storage.googleapis.com/
- name: redis
  condition: read.image.args.redis.enabled
  version: ^6.0.0
  repository: https://kubernetes-charts.storage.googleapis.com/
```

Para configurar os valores predefinidos do gráfico Helm, copie e cole o YAML seguinte num ficheiro denominado `values.yaml` . Substitua os `# {ENDPOINT_URI}` comentários e os comentários pelos seus `# {API_KEY}` próprios valores. Configure resultadoExpirationPeriod, Redis e RabbitMQ, se necessário.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry:  containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
      
      # Result expiration period setting. Specify when the system should clean up recognition results.
      # For example, resultExpirationPeriod=1, the system will clear the recognition result 1hr after the process.
      # resultExpirationPeriod=0, the system will clear the recognition result after result retrieval.
      resultExpirationPeriod: 1
      
      # Redis storage, if configured, will be used by read container to store result records.
      # A cache is required if multiple read containers are placed behind load balancer.
      redis:
        enabled: false # {true/false}
        password: password

      # RabbitMQ is used for dispatching tasks. This can be useful when multiple read containers are
      # placed behind load balancer.
      rabbitmq:
        enabled: false # {true/false}
        rabbitmq:
          username: user
          password: password
```

> [!IMPORTANT]
> - Se os `billing` valores e `apikey` valores não forem fornecidos, os serviços expiram após 15 minutos. Da mesma forma, a verificação falha porque os serviços não estão disponíveis.
> 
> - Se colocar vários recipientes De leitura atrás de um equilibrador de carga, por exemplo, sob Docker Compose ou Kubernetes, deve ter uma cache externa. Como o recipiente de processamento e o recipiente de pedido GET podem não ser os mesmos, uma cache externa armazena os resultados e partilha-os através de contentores. Para obter mais informações sobre as definições de cache, consulte [os recipientes Configure Computer Vision Docker](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).
>

Crie uma pasta *de modelos* sob o *diretório de leitura.* Copiar e colar o seguinte YAML num ficheiro denominado `deployment.yaml` . O `deployment.yaml` ficheiro servirá como modelo de leme.

> Os modelos geram ficheiros manifestos, que são descrições de recursos formatadas por YAML que kubernetes podem entender. [- Guia de modelo de gráfico de leme][chart-template-guide]

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: read
  labels:
    app: read-deployment
spec:
  selector:
    matchLabels:
      app: read-app
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
        args:        
        - ReadEngineConfig:ResultExpirationPeriod={{ .Values.read.image.args.resultExpirationPeriod }}
        {{- if .Values.read.image.args.rabbitmq.enabled }}
        - Queue:RabbitMQ:HostName={{ include "rabbitmq.hostname" . }}
        - Queue:RabbitMQ:Username={{ .Values.read.image.args.rabbitmq.rabbitmq.username }}
        - Queue:RabbitMQ:Password={{ .Values.read.image.args.rabbitmq.rabbitmq.password }}
        {{- end }}      
        {{- if .Values.read.image.args.redis.enabled }}
        - Cache:Redis:Configuration={{ include "redis.connStr" . }}
        {{- end }}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}      
--- 
apiVersion: v1
kind: Service
metadata:
  name: read-service
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

Na pasta dos *mesmos modelos,* copie e cole as seguintes funções de ajudante em `helpers.tpl` . `helpers.tpl` define funções úteis para ajudar a gerar o modelo Helm.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMaster := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostSlave := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMaster $hostSlave $passWord $connTail -}}
{{- end -}}
```
O modelo especifica um serviço de balançador de carga e a implantação do seu recipiente/imagem para Ler.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de que estivar a imagem(s) para puxar do registo do `containerpreview.azurecr.io` contentor.

> Um [gráfico Helm][helm-charts] é uma coleção de ficheiros que descrevem um conjunto relacionado de recursos Kubernetes. Um único gráfico pode ser usado para implementar algo simples, como um casulo memcached, ou algo complexo, como uma pilha completa de aplicativos web com servidores HTTP, bases de dados, caches, e assim por diante.

Os *gráficos helm* fornecidos retiram as imagens do serviço de visão computacional e o serviço correspondente do registo do `containerpreview.azurecr.io` contentor.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instale o gráfico Helm no cluster Kubernetes

Para instalar o gráfico de *leme,* precisamos executar o [`helm install`][helm-install-cmd] comando. Certifique-se de que executa o comando de instalação a partir do diretório acima da `read` pasta.

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

Deverá esperar ver algo semelhante à seguinte saída:

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

Para mais detalhes sobre a instalação de aplicações com Helm in Azure Kubernetes Service (AKS), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contentores dos Serviços Cognitivos][cog-svcs-containers]

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
