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
ms.date: 01/27/2020
ms.author: aahi
ms.openlocfilehash: 124145059c825dee1dd52298688a47a807058551
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182099"
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

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores de gráfico de leme para implantação

Comece por criar uma pasta chamada *leitura*. Em seguida, cole o seguinte conteúdo YAML num novo ficheiro chamado `chart.yaml` :

```yaml
apiVersion: v2
name: read
version: 1.0.0
description: A Helm chart to deploy the Read OCR container to a Kubernetes cluster
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
    registry:  mcr.microsoft.com/
    repository: azure-cognitive-services/vision/read
    tag: 3.2-preview.1
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
> - Se colocar vários recipientes De leitura atrás de um equilibrador de carga, por exemplo, sob Docker Compose ou Kubernetes, deve ter uma cache externa. Como o recipiente de processamento e o recipiente de pedido GET podem não ser os mesmos, uma cache externa armazena os resultados e partilha-os através de contentores. Para obter mais informações sobre as definições de cache, consulte [os recipientes Configure Computer Vision Docker](./computer-vision-resource-container-config.md).
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

> [!NOTE]
> Este artigo contém referências ao termo escravo, um termo que a Microsoft já não usa. Quando o termo for removido do software, vamos removê-lo deste artigo.

```yaml
{{- define "rabbitmq.hostname" -}}
{{- printf "%s-rabbitmq" .Release.Name -}}
{{- end -}}

{{- define "redis.connStr" -}}
{{- $hostMain := printf "%s-redis-master:6379" .Release.Name }}
{{- $hostReplica := printf "%s-redis-slave:6379" .Release.Name -}}
{{- $passWord := printf "password=%s" .Values.read.image.args.redis.password -}}
{{- $connTail := "ssl=False,abortConnect=False" -}}
{{- printf "%s,%s,%s,%s" $hostMain $hostReplica $passWord $connTail -}}
{{- end -}}
```
O modelo especifica um serviço de balançador de carga e a implantação do seu recipiente/imagem para Ler.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de que estivar a imagem(s) para puxar do registo do `mcr.microsoft.com` contentor.

> Um [gráfico Helm][helm-charts] é uma coleção de ficheiros que descrevem um conjunto relacionado de recursos Kubernetes. Um único gráfico pode ser usado para implementar algo simples, como um casulo memcached, ou algo complexo, como uma pilha completa de aplicativos web com servidores HTTP, bases de dados, caches, e assim por diante.

Os *gráficos helm* fornecidos retiram as imagens do serviço de visão computacional e o serviço correspondente do registo do `mcr.microsoft.com` contentor.

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

## <a name="deploy-multiple-v3-containers-on-the-kubernetes-cluster"></a>Implementar vários recipientes V3 no cluster Kubernetes

A partir da v3 do recipiente, pode utilizar os recipientes em paralelo tanto a nível de tarefa como a nível de página.

Por design, cada recipiente V3 tem um despachante e um trabalhador de reconhecimento. O despachante é responsável por dividir uma tarefa de várias páginas em várias sub-tarefas de página única. O trabalhador de reconhecimento está otimizado para reconhecer um documento de uma única página. Para alcançar o paralelismo de nível de página, coloque vários recipientes V3 atrás de um equilibrador de carga e deixe que os recipientes partilhem um armazenamento e fila universais. 

> [!NOTE] 
> Atualmente apenas o Azure Storage e a Azure Queue são suportados. 

O recipiente que recebe o pedido pode dividir a tarefa em sub-tarefas de página única e adicioná-las à fila universal. Qualquer trabalhador de reconhecimento de um recipiente menos ocupado pode consumir sub-tarefas de página única a partir da fila, realizar reconhecimento e carregar o resultado para o armazenamento. A produção pode ser melhorada até ao `n` momento, dependendo do número de contentores que são implantados.

O recipiente v3 expõe a sonda de vida API sob o `/ContainerLiveness` caminho. Use o exemplo de implementação a seguir para configurar uma sonda de vivação para Kubernetes. 

Copiar e colar o seguinte YAML num ficheiro denominado `deployment.yaml` . Substitua os `# {ENDPOINT_URI}` comentários e os comentários pelos seus `# {API_KEY}` próprios valores. Substitua o `# {AZURE_STORAGE_CONNECTION_STRING}` comentário pela sua cadeia de ligação de armazenamento Azure. Configure `replicas` o número que deseja, que é definido no exemplo `3` seguinte.

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
  replicas: # {NUMBER_OF_READ_CONTAINERS}
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: cognitive-services-read
        image: mcr.microsoft.com/azure-cognitive-services/vision/read
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: accept
        - name: billing
          value: # {ENDPOINT_URI}
        - name: apikey
          value: # {API_KEY}
        - name: Storage__ObjectStore__AzureBlob__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        - name: Queue__Azure__ConnectionString
          value: # {AZURE_STORAGE_CONNECTION_STRING}
        livenessProbe:
          httpGet:
            path: /ContainerLiveness
            port: 5000
          initialDelaySeconds: 60
          periodSeconds: 60
          timeoutSeconds: 20
--- 
apiVersion: v1
kind: Service
metadata:
  name: azure-cognitive-service-read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort: 5000
  selector:
    app: read-app
```

Execute o seguinte comando. 

```console
kubectl apply -f deployment.yaml
```

Abaixo está uma saída de exemplo que pode ver a partir de uma execução de implementação bem sucedida:

```console
deployment.apps/read created
service/azure-cognitive-service-read created
```

A implantação de Kubernetes pode demorar vários minutos a ser concluída. Para confirmar que ambas as cápsulas e serviços estão devidamente implantados e disponíveis, em seguida, execute o seguinte comando:

```console
kubectl get all
```

Deve ver a saída da consola semelhante à seguinte:

```console
kubectl get all
NAME                       READY   STATUS    RESTARTS   AGE
pod/read-6cbbb6678-58s9t   1/1     Running   0          3s
pod/read-6cbbb6678-kz7v4   1/1     Running   0          3s
pod/read-6cbbb6678-s2pct   1/1     Running   0          3s

NAME                                   TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)          AGE
service/azure-cognitive-service-read   LoadBalancer   10.0.134.0   <none>         5000:30846/TCP   17h
service/kubernetes                     ClusterIP      10.0.0.1     <none>         443/TCP          78d

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   3/3     3            3           3s

NAME                             DESIRED   CURRENT   READY   AGE
replicaset.apps/read-6cbbb6678   3         3         3       3s
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
[azure-cli]: /cli/azure/install-azure-cli
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
