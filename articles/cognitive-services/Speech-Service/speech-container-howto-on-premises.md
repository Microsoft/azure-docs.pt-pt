---
title: Utilizar o Kubernetes no local
titleSuffix: Azure Cognitive Services
description: Com o Kubernetes (K8s) e o Helm para definir as imagens de contentor de voz em texto e texto para voz, vamos criar um pacote do Kubernetes. Este pacote será implantado para Kubernetes cluster no local.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: dapine
ms.openlocfilehash: 1e3afc80abad5f5c1f9b4d57c52ca75449eeb755
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711485"
---
# <a name="use-kubernetes-on-premises"></a>Utilizar o Kubernetes no local

Com o Kubernetes (K8s) e o Helm para definir as imagens de contentor de voz em texto e texto para voz, vamos criar um pacote do Kubernetes. Este pacote será implantado para Kubernetes cluster no local. Por fim, vamos explorar como testar os serviços implementados e várias opções de configuração.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que tem de estar instaladas e executadas localmente.

* Utilize uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita][free-azure-account] antes de começar.
* Instalar o [CLI do Azure][azure-cli] (az).
* Instalar o [CLI do Kubernetes][kubernetes-cli] (kubectl).
* Instalar o [Helm][helm-install] cliente, o Gestor de pacotes do Kubernetes.
    * Instalar o servidor de Helm [Tiller][tiller-install].
* Um recurso do Azure com o escalão de preço correto. Nem todos os escalões de preço trabalham com estas imagens de contentor:
    * **Voz** recurso com preços Standard ou de F0 camadas apenas.
    * **Os serviços cognitivos** recurso com o S0 escalão de preço.

## <a name="the-recommended-host-computer-configuration"></a>A configuração do computador anfitrião recomendado

Consulte a [computador de anfitrião de contentor do serviço de voz][speech-container-host-computer] detalhes como referência. Isso *gráfico helm* calcula automaticamente os requisitos de CPU e memória com base no número descodifica (pedidos simultâneos) que o utilizador Especifica. Além disso, será ajustado com base no se otimizações para entrada de áudio/texto estão configuradas como `enabled`. As predefinições do gráfico helm para dois pedidos em simultâneo e a otimização de Desativação.

| Serviço | CPU / contentor | Memória / contentor |
|--|--|--|
| **Conversão de voz em texto** | um Decodificador requer um mínimo de 1,150 milinúcleos. Se o `optimizedForAudioFile` é ativado, então 1,950 milinúcleos são necessários. (predefinição: dois decodificadores) | Necessário: 2 GB<br>Limitado:  4GB |
| **Voz** | um pedido simultâneo requer um mínimo de 500 milinúcleos. Se o `optimizeForTurboMode` é ativado, então 1.000 milinúcleos são necessários. (predefinição: duas solicitações simultâneas) | Necessário: 1 GB<br> Limitado: 2 GB |

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Submeter os [formulário de pedido de contentores de voz dos serviços cognitivos][speech-preview-access] para pedir acesso ao contentor. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Ligue ao cluster do Kubernetes

Espera-se que o computador anfitrião tem um cluster de Kubernetes disponível. Veja este tutorial sobre [implementar um cluster de Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para uma compreensão conceptual sobre como implementar um cluster do Kubernetes num computador anfitrião.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Partilhar as credenciais do Docker com o cluster de Kubernetes

Para permitir que o cluster de Kubernetes `docker pull` as imagens de configurado do `containerpreview.azurecr.io` registo de contentor, terá de transferir as credenciais de docker para o cluster. Executar o [ `kubectl create` ][kubectl-create] comando abaixo para criar um *segredo de docker-registry* com base nas credenciais fornecidas do contêiner [acesso ao Registro](#request-access-to-the-container-registry) secção.

A partir da sua interface de linha de comandos de preferência, execute o seguinte comando. Certifique-se de que substitua a `<username>`, `<password>`, e `<email-address>` com as credenciais de registo de contentor.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Se já tiver acesso para o `containerpreview.azurecr.io` registo de contentor, pode criar um segredo do Kubernetes com o sinalizador genérico em vez disso. Considere o seguinte comando executado em relação a sua configuração de Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

O resultado seguinte é impresso no console quando o segredo foi criado com êxito.

```console
secret "containerpreview" created
```

Para verificar que o segredo foi criado, execute o [ `kubectl get` ][kubectl-get] com o `secrets` sinalizador.

```console
kuberctl get secrets
```

Executar o `kubectl get secrets` imprime todos os segredos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configurar valores de gráfico Helm para implementação

Visite o [Microsoft Helm Hub][ms-helm-hub] para todos os gráficos do helm disponíveis publicamente oferecidos pela Microsoft. Do Hub de Helm Microsoft, encontrará a **gráfico de locais de voz dos serviços cognitivos**. O **cognitivos serviços de voz no local** é o gráfico, vamos instalar, mas, primeiro tem de criar um `config-values.yaml` ficheiro por configurações explícitas. Vamos começar por adicionar o repositório da Microsoft para nossa instância do Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Em seguida, vamos configurar nossos valores de gráfico do Helm. Copie e cole o seguinte YAML para um ficheiro denominado `config-values.yaml`. Para obter mais informações sobre como personalizar o **cognitivos serviços de voz no local gráfico Helm**, consulte [personalizar gráficos helm](#customize-helm-charts). Substitua a `billing` e `apikey` valores pelos seus próprios.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Se o `billing` e `apikey` valores não são fornecidos, os serviços irão expirar após 15 minutos. Da mesma forma, a verificação irá falhar, os serviços não estará disponíveis.

### <a name="the-kubernetes-package-helm-chart"></a>O pacote do Kubernetes (gráfico Helm)

O *gráfico Helm* contém a configuração dos quais imagens do docker de onde o `containerpreview.azurecr.io` registo de contentor.

> R [gráfico Helm][helm-charts] é uma coleção de arquivos que descrevem um conjunto relacionado de recursos do Kubernetes. Um único gráfico pode ser utilizado para implementar algo simples, como um pod memcached, ou algo complexo, como uma pilha de aplicação web completa com HTTP servidores, bases de dados, caches e assim por diante.

Fornecido *executar Helm gráficos* extrair as imagens do docker do serviço de voz, voz e os serviços de voz em texto do `containerpreview.azurecr.io` registo de contentor.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalar o gráfico do Helm no cluster de Kubernetes

Para instalar o *gráfico helm* vamos precisar de executar o [ `helm install` ][helm-install-cmd] comando, substituindo o `<config-values.yaml>` com o argumento de nome de ficheiro e caminho apropriado. O `microsoft/cognitive-services-speech-onpremise` gráfico do Helm referenciado abaixo está disponível na [aqui para o Microsoft Helm Hub][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Aqui está uma saída de exemplo, pode esperar para ver a partir de uma execução concluída a instalação:

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

A implementação de Kubernetes pode assumir a vários minutos a concluir. Para confirmar que o pods e serviços estão corretamente implementada e disponível, execute o seguinte comando:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Verificar a implementação de Helm com testes Helm

Definem os gráficos do Helm instalados *executar Helm testes*, que servem como uma conveniência para verificação. Esses testes validam a preparação para o serviço. Para verificar ambos **voz em texto** e **voz** serviços, executaremos o [teste Helm][helm-test] comando.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Esses testes irão falhar se não for o estado POD `Running` ou se a implementação não está listada no `AVAILABLE` coluna. Seja paciente como isto pode demorar mais de dez minutos para concluir.

Esses testes produzirá vários resultados de estado:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa à execução do *executar helm testes*, que pode recolher o *IP externo* endereços e portas correspondentes do `kubectl get all` comando. Utilizar o IP e a porta, abra um browser e navegue para `http://<external-ip>:<port>:/swagger/index.html` para ver a API do swagger página (s).

## <a name="customize-helm-charts"></a>Personalizar gráficos Helm

Gráficos Helm são hierárquicos. Estar hierárquica permite a herança de gráfico, ele também atende ao conceito de especificidade, onde as definições que são mais específicas substituem regras herdadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Passos Seguintes

Para obter mais detalhes sobre como instalar aplicações com Helm no Azure Kubernetes Service (AKS), [visite aqui][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contentores de serviços cognitivos][cog-svcs-containers]

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
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
