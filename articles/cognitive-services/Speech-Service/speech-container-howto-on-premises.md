---
title: Usar contêineres de serviço de fala com kubernetes e Helm
titleSuffix: Azure Cognitive Services
description: Usando kubernetes e Helm para definir as imagens de contêiner de conversão de texto em texto e de Text para fala, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7874a6b274939c233dd1c4e6d146df2a9a409e65
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833996"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Usar contêineres de serviço de fala com kubernetes e Helm

Uma opção para gerenciar seus contêineres de fala no local é usar kubernetes e Helm. Usando kubernetes e Helm para definir as imagens de contêiner de conversão de texto em texto e de Text para fala, criaremos um pacote kubernetes. Este pacote será implantado em um cluster kubernetes local. Por fim, exploraremos como testar os serviços implantados e várias opções de configuração. Para obter mais informações sobre como executar contêineres do Docker sem orquestração kubernetes, consulte [instalar e executar contêineres de serviço de fala](speech-container-howto.md).

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos antes de usar os contêineres de fala locais:

|Obrigatório|Finalidade|
|--|--|
| Conta do Azure | Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar. |
| Acesso ao registro de contêiner | Para que o kubernetes pegue as imagens do Docker no cluster, ele precisará de acesso ao registro de contêiner. |
| CLI do kubernetes | A [CLI do kubernetes][kubernetes-cli] é necessária para gerenciar as credenciais compartilhadas do registro de contêiner. O kubernetes também é necessário antes de Helm, que é o Gerenciador de pacotes do kubernetes. |
| CLI do Helm | Como parte da instalação da [CLI do Helm][helm-install] , você também precisará inicializar o Helm, que será instalado no entanto. [][tiller-install] |
|Recurso de fala |Para usar esses contêineres, você deve ter:<br><br>Um recurso do Azure de _fala_ para obter a chave de cobrança associada e o URI do ponto de extremidade de cobrança. Ambos os valores estão disponíveis nas páginas de visão geral de **fala** e chaves do portal do Azure e são necessários para iniciar o contêiner.<br><br>**{Api_key}** : chave de recurso<br><br>**{ENDPOINT_URI}** : o exemplo de URI do ponto de extremidade é: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>A configuração do computador host recomendado

Consulte os detalhes do [computador host do contêiner do serviço de fala][speech-container-host-computer] como uma referência. Este *gráfico de Helm* calcula automaticamente os requisitos de CPU e memória com base em quantos decodificações (solicitações simultâneas) que o usuário especifica. Além disso, ele se ajustará com base em se as otimizações de entrada de áudio/texto são configuradas como `enabled`. O gráfico Helm usa como padrão duas solicitações simultâneas e desabilitando a otimização.

| Serviço | CPU/contêiner | Memória/contêiner |
|--|--|--|
| **Conversão de fala em texto** | um decodificador requer um mínimo de 1.150 milicores. Se a `optimizedForAudioFile` estiver habilitada, 1.950 milicores serão necessárias. (padrão: dois decodificadores) | Necessário: 2 GB<br>Limitado: 4 GB |
| **Conversão de texto em fala** | uma solicitação simultânea requer um mínimo de 500 milicores. Se a `optimizeForTurboMode` estiver habilitada, 1.000 milicores serão necessárias. (padrão: duas solicitações simultâneas) | Necessário: 1 GB<br> Limitado: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar-se ao cluster kubernetes

Espera-se que o computador host tenha um cluster kubernetes disponível. Consulte este tutorial sobre como [implantar um cluster kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para obter um entendimento conceitual de como implantar um cluster kubernetes em um computador host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Compartilhando as credenciais do Docker com o cluster kubernetes

Para permitir que o cluster kubernetes `docker pull` as imagens configuradas do registro de contêiner `containerpreview.azurecr.io`, você precisa transferir as credenciais do Docker para o cluster. Execute o comando [`kubectl create`][kubectl-create] abaixo para criar um *segredo do registro do Docker* com base nas credenciais fornecidas do pré-requisito de acesso ao registro de contêiner.

Na sua interface de linha de comando de escolha, execute o comando a seguir. Certifique-se de substituir o `<username>`, `<password>`e `<email-address>` pelas credenciais do registro de contêiner.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se você já tiver acesso ao registro de contêiner de `containerpreview.azurecr.io`, poderá criar um segredo kubernetes usando o sinalizador genérico em vez disso. Considere o seguinte comando que é executado em seu JSON de configuração do Docker.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

A saída a seguir será impressa no console quando o segredo tiver sido criado com êxito.

```console
secret "mcr" created
```

Para verificar se o segredo foi criado, execute o [`kubectl get`][kubectl-get] com o sinalizador `secrets`.

```console
kubectl get secrets
```

A execução da `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores do gráfico Helm para implantação

Visite o [Hub do Microsoft Helm][ms-helm-hub] para todos os gráficos de Helm publicamente disponíveis oferecidos pela Microsoft. No Hub do Microsoft Helm, você encontrará o **gráfico de fala local dos serviços cognitivas**. A **fala local dos serviços cognitivas** é o gráfico que iremos instalar, mas primeiro devemos criar um arquivo de `config-values.yaml` com configurações explícitas. Vamos começar adicionando o repositório da Microsoft à nossa instância do Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Em seguida, configuraremos os valores do gráfico Helm. Copie e cole o YAML a seguir em um arquivo chamado `config-values.yaml`. Para obter mais informações sobre como personalizar o **gráfico Helm de fala no local dos serviços cognitivas**, consulte [personalizar gráficos do Helm](#customize-helm-charts). Substitua os comentários `# {ENDPOINT_URI}` e `# {API_KEY}` pelos seus próprios valores.

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
> Se os valores de `billing` e `apikey` não forem fornecidos, os serviços expirarão após 15 minutos. Da mesma forma, a verificação falhará, pois os serviços não estarão disponíveis.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote kubernetes (gráfico Helm)

O *gráfico Helm* contém a configuração da imagem (s) do Docker a ser extraída do registro de contêiner `containerpreview.azurecr.io`.

> Um [gráfico do Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do kubernetes. Um único gráfico pode ser usado para implantar algo simples, como um pod memcached, ou algo complexo, como uma pilha completa do aplicativo Web com servidores HTTP, bancos de dados, caches e assim por diante.

Os *gráficos Helm* fornecidos puxam as imagens do Docker do serviço de fala, conversão de texto em fala e os serviços de conversão de fala em texto do registro de contêiner `containerpreview.azurecr.io`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico Helm no cluster kubernetes

Para instalar o *gráfico Helm* , precisaremos executar o comando [`helm install`][helm-install-cmd] , substituindo o `<config-values.yaml>` pelo caminho apropriado e o argumento de nome de arquivo. O gráfico de `microsoft/cognitive-services-speech-onpremise` Helm referenciado abaixo está disponível no [Hub do Microsoft Helm aqui][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Aqui está um exemplo de saída que você pode esperar ver em uma execução de instalação bem-sucedida:

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

A implantação do kubernetes pode levar mais de alguns minutos para ser concluída. Para confirmar se os pods e os serviços estão adequadamente implantados e disponíveis, execute o seguinte comando:

```console
kubectl get all
```

Você deve esperar ver algo semelhante à seguinte saída:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Verificar a implantação do Helm com testes do Helm

Os gráficos do Helm instalados definem *testes Helm*, que servem como uma conveniência para a verificação. Esses testes validam a prontidão do serviço. Para verificar os serviços de **conversão de fala em texto** e de **texto em fala** , executaremos o comando de [teste Helm][helm-test] .

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Esses testes falharão se o status do POD não for `Running` ou se a implantação não estiver listada na coluna `AVAILABLE`. Seja paciente, pois isso pode levar mais de dez minutos para ser concluído.

Esses testes produzirão vários resultados de status:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa à execução dos *testes de Helm*, você pode coletar os endereços *IP externos* e as portas correspondentes do comando `kubectl get all`. Usando o IP e a porta, abra um navegador da Web e navegue até `http://<external-ip>:<port>:/swagger/index.html` para exibir a página (s) Swagger de API.

## <a name="customize-helm-charts"></a>Personalizar gráficos do Helm

Os gráficos Helm são hierárquicos. Ser hierárquico permite a herança de gráfico, ele também atende ao conceito de especificidade, em que as configurações mais específicas substituem as regras herdadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
