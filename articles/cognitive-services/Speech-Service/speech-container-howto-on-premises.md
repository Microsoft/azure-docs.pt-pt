---
title: Utilize recipientes de serviço de fala com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando Kubernetes e Helm para definir as imagens de contentores de fala a texto e texto-a-fala, vamos criar um pacote Kubernetes. Este pacote será implantado num aglomerado kubernetes no local.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: cd4ff97902b1ce3d1d5a0ea066608fd33e6bf697
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037106"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Utilize recipientes de serviço de fala com Kubernetes e Helm

Uma opção para gerir os seus contentores de Fala no local é usar Kubernetes e Helm. Usando Kubernetes e Helm para definir as imagens de contentores de fala a texto e texto-a-fala, vamos criar um pacote Kubernetes. Este pacote será implantado num aglomerado kubernetes no local. Finalmente, vamos explorar como testar os serviços implantados e várias opções de configuração. Para obter mais informações sobre a execução de contentores Docker sem orquestração Kubernetes, consulte [instalar e executar recipientes](speech-container-howto.md)de serviço da Fala .

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de utilizar em local os contentores da Fala:

|Necessário|Objetivo|
|--|--|
| Conta do Azure | Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Acesso ao Registo de Contentores | Para que os Kubernetes puxem as imagens do estivador para o aglomerado, precisará de acesso ao registo do contentor. |
| Kubernetes CLI | O [Kubernetes CLI][kubernetes-cli] é necessário para gerir as credenciais partilhadas do registo de contentores. Kubernetes também é necessário antes de Helm, que é o gestor de pacotes Kubernetes. |
| Helm CLI | Como parte da instalação [helm CLI,][helm-install] também terá de inicializar o Helm, que irá instalar [o Tiller][tiller-install]. |
|Recurso da fala |Para utilizar estes recipientes, deve ter:<br><br>Um recurso _Speech_ Azure para obter a chave de faturação associada e o ponto final de faturação URI. Ambos os valores estão disponíveis nas páginas de Visão Geral do **Discurso** e Chaves do portal Azure e são necessários para iniciar o recipiente.<br><br>**{API_KEY}** : chave de recursos<br><br>**{ENDPOINT_URI}** : o exemplo do ponto final URI é: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>A configuração recomendada do computador anfitrião

Consulte os detalhes do computador do contentor do [serviço speech][speech-container-host-computer] como referência. Este *gráfico de leme* calcula automaticamente os requisitos de CPU e memória com base em quantos descodifica (pedidos simultâneos) que o utilizador especifica. Além disso, irá ajustar-se com base na configuração das otimizações para entrada de áudio/texto `enabled`. O gráfico do leme falha a, dois pedidos simultâneos e otimização incapacitante.

| Serviço | CPU / Contentor | Memória / Recipiente |
|--|--|--|
| **Discurso ao Texto** | um descodificador requer um mínimo de 1.150 milisílcores. Se o `optimizedForAudioFile` estiver ativado, são necessários 1.950 milisílcores. (predefinição: dois descodificadores) | Obrigatório: 2 GB<br>Limitado: 4 GB |
| **Texto-a-discurso** | um pedido simultâneo requer um mínimo de 500 milisílcores. Se o `optimizeForTurboMode` estiver ativado, são necessários 1.000 miliscores. (predefinição: dois pedidos simultâneos) | Obrigatório: 1 GB<br> Limitado: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Ligue-se ao cluster Kubernetes

Espera-se que o computador anfitrião tenha um cluster Kubernetes disponível. Consulte este tutorial sobre [a implementação de um cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceptual de como implantar um cluster Kubernetes para um computador hospedeiro.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Partilhar credenciais do Docker com o cluster Kubernetes

Para permitir que o cluster Kubernetes `docker pull` as imagens configuradas do registo de contentores `containerpreview.azurecr.io`, é necessário transferir as credenciais de estivador para o cluster. Execute o comando [`kubectl create`][kubectl-create] abaixo para criar um *segredo de registo de estivadores* com base nas credenciais fornecidas a partir do pré-requisito de acesso ao registo de contentores.

A partir da interface de escolha da linha de comando, execute o seguinte comando. Certifique-se de substituir os `<username>`, `<password>`e `<email-address>` pelas credenciais de registo de contentores.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se já tiver acesso ao registo de contentores `containerpreview.azurecr.io`, poderá criar um segredo kubernetes usando a bandeira genérica. Considere o seguinte comando que executa contra a sua configuração Docker JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída seguinte é impressa na consola quando o segredo foi criado com sucesso.

```console
secret "mcr" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com a bandeira `secrets`.

```console
kubectl get secrets
```

Executar a `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configure os valores do gráfico helm para implementação

Visite o [Microsoft Helm Hub][ms-helm-hub] para obter todas as tabelas de leme disponíveis publicamente oferecidas pela Microsoft. A partir do Microsoft Helm Hub, você encontrará o gráfico de **discurso de serviços cognitivos no local**. O **Discurso dos Serviços Cognitivos no Local** é o gráfico que vamos instalar, mas primeiro temos de criar um ficheiro `config-values.yaml` com configurações explícitas. Vamos começar por adicionar o repositório da Microsoft à nossa instância Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Em seguida, vamos configurar os valores da nossa tabela Helm. Copie e cole o seguinte YAML num ficheiro chamado `config-values.yaml`. Para obter mais informações sobre a personalização do Gráfico de Leme do **Discurso dos Serviços Cognitivos,** consulte gráficos de [leme personalizados.](#customize-helm-charts) Substitua os comentários `# {ENDPOINT_URI}` e `# {API_KEY}` pelos seus próprios valores.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se os valores `billing` e `apikey` não forem fornecidos, os serviços expirarão após 15 min. Da mesma forma, a verificação falhará, uma vez que os serviços não estarão disponíveis.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de que imagens de estivador retirar do registo de contentores `containerpreview.azurecr.io`.

> Um [gráfico helm][helm-charts] é uma coleção de ficheiros que descrevem um conjunto relacionado de recursos kubernetes. Um único gráfico pode ser usado para implementar algo simples, como um casulo memcached, ou algo complexo, como uma pilha de aplicativos web completo com servidores HTTP, bases de dados, caches, e assim por diante.

As *tabelas helm* fornecidas retiram as imagens do estivador do serviço de Discurso, tanto de texto a fala como dos serviços de fala a texto do registo de contentores `containerpreview.azurecr.io`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instale o gráfico Helm no cluster Kubernetes

Para instalar o gráfico de *leme,* teremos de executar o comando [`helm install`,][helm-install-cmd] substituindo o `<config-values.yaml>` pelo caminho apropriado e pelo argumento do nome do ficheiro. O gráfico `microsoft/cognitive-services-speech-onpremise` Helm referenciado abaixo está disponível no [Microsoft Helm Hub aqui][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Aqui está um exemplo de saída que você pode esperar ver de uma execução de instalação bem sucedida:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

A implantação de Kubernetes pode demorar vários minutos a ser concluída. Para confirmar que tanto as cápsulas como os serviços estão devidamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Deve esperar ver algo semelhante à seguinte saída:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Verifique a implantação do Leme com os testes helm

Os gráficos helm instalados definem *os testes Helm,* que servem como uma conveniência para a verificação. Estes testes validam a prontidão do serviço. Para verificar tanto os serviços de **fala-a-texto** como **de texto-a-fala,** executaremos o comando de [teste Helm.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Estes testes falharão se o estado do POD não for `Running` ou se a implantação não estiver listada na coluna `AVAILABLE`. Tenha paciência, pois isto pode levar mais de 10 minutos para completar.

Estes testes irão resultados diversos:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa à execução dos testes de *leme,* pode recolher os endereços *IP externos* e as portas correspondentes do comando `kubectl get all`. Utilizando o IP e a porta, abra um navegador web e navegue para `http://<external-ip>:<port>:/swagger/index.html` para ver a página(s) da API.

## <a name="customize-helm-charts"></a>Personalizar gráficos helm

Os gráficos de leme são hierárquicos. Ser hierárquico permite a herança do gráfico, também atende ao conceito de especificidade, onde configurações mais específicas sobrepõem as regras hereditárias.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
