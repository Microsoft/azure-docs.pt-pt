---
title: Instalar e executar contentores - detetor de anomalias
titleSuffix: Azure Cognitive Services
description: Utilize algoritmos avançados da API de detetor de anomalias para identificar anomalias nos seus dados de séries de tempo.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 5dcec0d5f313b1c746c0674d0f9bf4d30ed19e5c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026315"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Instalar e executar contentores detetor de anomalias

O detetor de anomalias tem o seguinte contêiner: 

|Função|Funcionalidades|
|-|-|
|Detetor de anomalias| <li> Deteta anomalias, à medida que ocorrem em tempo real. <li> Deteta anomalias ao longo do seu conjunto de dados como um lote. <li> Infere o intervalo normal esperado dos seus dados. <li> Ajuste sensibilidade de deteção de anomalias do suporta para melhor ajustar os seus dados. |

Para obter informações detalhadas sobre as APIs, consulte:
* [Saiba mais sobre o serviço de API de detetor de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores detetor de anomalias:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Recursos de detetor de anomalias |Para utilizar estes contentores, tem de ter:<br><br>Uma _detetor de anomalias_ recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis nas páginas de descrição geral de detetor de anomalias e chaves do portal do Azure e são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Primeiro tem de concluir e submeter o [formulário de pedido de contentor detetor de anomalias](https://aka.ms/adcontainer) para pedir acesso ao contentor.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador anfitrião

O **anfitrião** é o computador que executa o contentor do docker. Pode ser um computador no local ou um docker a alojar o serviço no Azure incluindo:

* [Serviço Kubernetes do Azure](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) cluster implementado [do Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Para obter mais informações, consulte [implementar o Kubernetes no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).
<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória a Alocar para o contentor de detetor de anomalias.

| QPS (consultas por segundo) | Mínimo | Recomendado |
|-----------|---------|-------------|
| 10 QPS | 4 núcleos, 1GB de memória | 2GB de memória de 8 núcleos |
| 20 QPS | 8 núcleos, 2GB de memória | 4GB de memória de 16 núcleos |

Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Utilize o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor.

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Solicitação de docker para o contentor de detetor de anomalias

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), o necessário com as definições de faturação. Obter mais [exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de anomalias detetor chaves do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral de detetor de anomalias do portal do Azure.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de detetor de anomalias da imagem de contentor
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>A execução de vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Substitua a `<container-registry>` e `<container-name>` com os valores dos contentores que utilizar. Estes não têm de ser o mesmo contentor. Pode ter o contentor de detetor de anomalias e o contentor de LUIS em execução no anfitrião em conjunto, ou pode ter vários contentores de detetor de anomalias em execução. 

Execute o primeiro contentor na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Execute o contentor de segundo na porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cada contentor subseqüente deve ser uma porta diferente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. 

Usar o host, https://localhost:5000, para o contentor APIs.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](anomaly-detector-container-configuration.md#mount-settings) e registo ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem ao iniciar ou executar o contentor. 

## <a name="billing"></a>Faturação

O envio de contentores detetor de anomalias cobrança informações para o Azure, utilizando uma _detetor de anomalias_ recursos na sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e detetor de anomalias contentores em execução. Em resumo:

* Detetor de anomalias fornece um contentor do Linux para o Docker, encapsulando a deteção de anomalias com o vs de lote, transmissão em fluxo, inferência de tipos do intervalo esperado e a otimização de sensibilidade.
* Imagens de contentor são transferidas a partir de um registo de contentor privado do Azure dedicado para a pré-visualização de contentores.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações em contentores de detetor de anomalias ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, os dados de séries temporais que está a ser analisados) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](anomaly-detector-container-configuration.md) para definições de configuração
* [Saiba mais sobre o serviço de API de detetor de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
