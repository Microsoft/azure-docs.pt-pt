---
title: Use recipientes de serviço de fala com Kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando Kubernetes e Helm para definir as imagens de porta-a-texto e de texto-a-discurso, criaremos um pacote Kubernetes. Este pacote será implantado num cluster kubernetes no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: aahi
ms.openlocfilehash: 277a3c1c53564d7c5dff6a87381680a7f41606de
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131603"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Use recipientes de serviço de fala com Kubernetes e Helm

Uma opção para gerir os seus recipientes de discurso no local é usar Kubernetes e Helm. Usando Kubernetes e Helm para definir as imagens de porta-a-texto e de texto-a-discurso, criaremos um pacote Kubernetes. Este pacote será implantado num cluster kubernetes no local. Finalmente, vamos explorar como testar os serviços implantados e várias opções de configuração. Para obter mais informações sobre a execução de recipientes Docker sem orquestração Kubernetes, consulte [instalar e executar recipientes de serviço de fala](speech-container-howto.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes da utilização dos recipientes de fala no local:

| Necessário | Objetivo |
|----------|---------|
| Conta do Azure | Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Acesso ao Registo de Contentores | Para que kubernetes puxe as imagens do estivador para o aglomerado, precisará de acesso ao registo do contentor. |
| Kubernetes CLI | O [CLI de Kubernetes][kubernetes-cli] é necessário para gerir as credenciais partilhadas do registo do contentor. Kubernetes também é necessário antes de Helm, que é o gestor de pacotes Kubernetes. |
| Helm CLI | Instale o [Helm CLI,][helm-install]que é utilizado para instalar um cartão de leme (definição de embalagem de recipiente). |
|Recurso de fala |Para utilizar estes recipientes, deve ter:<br><br>Um recurso _Speech_ Azure para obter a chave de faturação associada e o ponto final de faturação URI. Ambos os valores estão disponíveis nas páginas **"Speech** Overview" e "Chaves" do portal Azure e são obrigados a iniciar o contentor.<br><br>**{API_KEY}** : chave de recursos<br><br>**{ENDPOINT_URI}** : o exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>A configuração recomendada do computador anfitrião

Consulte os detalhes do [computador do recipiente de serviço de voz][speech-container-host-computer] como referência. Este *gráfico de leme* calcula automaticamente os requisitos de CPU e memória com base no número de decodificações (pedidos simultâneos) que o utilizador especifica. Além disso, ajustar-se-á com base na configuração das otimizações para a entrada áudio/texto como `enabled` . O gráfico de leme não tem padrão, dois pedidos simultâneos e otimização incapacitante.

| Serviço | CPU / Contentor | Memória / Recipiente |
|--|--|--|
| **Discurso-a-Texto** | um descodificador requer um mínimo de 1.150 millicores. Se o `optimizedForAudioFile` ativo for ativado, são necessários 1.950 millicores. (predefinição: dois descodificam) | Obrigatório: 2 GB<br>Limitado: 4 GB |
| **Texto-a-Discurso** | um pedido simultâneo requer um mínimo de 500 millicores. Se o `optimizeForTurboMode` ativo for ativado, são necessários 1.000 millicores. (predefinição: dois pedidos simultâneos) | Obrigatório: 1 GB<br> Limitado: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Ligue-se ao cluster Kubernetes

Espera-se que o computador anfitrião tenha um cluster Kubernetes disponível. Veja este tutorial sobre [a implementação de um cluster Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceptual de como implantar um cluster Kubernetes num computador anfitrião.

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores de gráfico de leme para implantação

Visite o [Microsoft Helm Hub][ms-helm-hub] para todos os gráficos de leme publicamente disponíveis oferecidos pela Microsoft. A partir do Microsoft Helm Hub, você encontrará o Gráfico de **Discurso de Serviços Cognitivos no Local** . O **Discurso dos Serviços Cognitivos nas Instalações** é o gráfico que vamos instalar, mas primeiro temos de criar um `config-values.yaml` ficheiro com configurações explícitas. Vamos começar por adicionar o repositório da Microsoft à nossa instância Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Em seguida, vamos configurar os nossos valores de gráfico Helm. Copiar e colar o seguinte YAML num ficheiro denominado `config-values.yaml` . Para obter mais informações sobre a personalização do Quadro de Timão do **Discurso dos Serviços Cognitivos nas Instalações,** consulte [gráficos de leme personalizados](#customize-helm-charts). Substitua os `# {ENDPOINT_URI}` comentários e os comentários pelos seus `# {API_KEY}` próprios valores.

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
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
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Se os `billing` valores e `apikey` valores não forem fornecidos, os serviços expirarão após 15 min. Da mesma forma, a verificação falhará, uma vez que os serviços não estarão disponíveis.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote Kubernetes (gráfico helm)

O *gráfico Helm* contém a configuração de que estivar a imagem(s) para puxar do registo do `mcr.microsoft.com` contentor.

> Um [gráfico Helm][helm-charts] é uma coleção de ficheiros que descrevem um conjunto relacionado de recursos Kubernetes. Um único gráfico pode ser usado para implementar algo simples, como um casulo memcached, ou algo complexo, como uma pilha completa de aplicativos web com servidores HTTP, bases de dados, caches, e assim por diante.

Os *gráficos* helm fornecidos puxam as imagens estivadoras do serviço Discurso, tanto de texto para discurso como dos serviços de fala-a-texto do registo do `mcr.microsoft.com` contentor.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instale o gráfico Helm no cluster Kubernetes

Para instalar o gráfico de *leme,* precisamos executar o [`helm install`][helm-install-cmd] comando, substituindo o `<config-values.yaml>` pelo caminho apropriado e argumento de nome de arquivo. O `microsoft/cognitive-services-speech-onpremise` gráfico Helm abaixo refere-se ao [Microsoft Helm Hub aqui][ms-helm-hub-speech-chart].

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

Deverá esperar ver algo semelhante à seguinte saída:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Verifique a implantação do Helm com os testes helm

Os gráficos helm instalados definem *os testes Helm,* que servem como uma conveniência para verificação. Estes testes validam a prontidão do serviço. Para verificar os serviços **de fala-a-texto** e **texto-a-voz,** executaremos o comando de [teste Helm.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Estes testes falharão se o estado do POD não estiver `Running` ou se a colocação não estiver listada na `AVAILABLE` coluna. Tenha paciência, pois isto pode levar mais de 10 minutos para ser concluído.

Estes testes irão resultados de vários resultados de estado:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa à execução dos *testes de leme,* pode recolher os endereços *IP Externos* e as portas correspondentes do `kubectl get all` comando. Utilizando o IP e a porta, abra um navegador web e navegue `http://<external-ip>:<port>:/swagger/index.html` para ver a página(s) da API swagger.

## <a name="customize-helm-charts"></a>Personalize gráficos helm

Os gráficos de leme são hierárquicos. Ser hierárquico permite a herança de gráficos, também atende ao conceito de especificidade, onde configurações mais específicas substituem as regras herdadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
