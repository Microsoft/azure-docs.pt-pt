---
title: Como instalar e executar recipientes para utilizar a API do Detetor de Anomalias
titleSuffix: Azure Cognitive Services
description: Utilize os algoritmos avançados da API do Detetor de Anomalias para identificar anomalias nos dados da série de tempo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 45abd904ea95cf8e68583ba5630a485af59479ec
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220349"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Instalar e executar recipientes do Detetor de Anomalias (Pré-visualização)

O Detetor de Anomalias tem a seguinte funcionalidade de funcionalidade do recipiente:

| Função | Funcionalidades |
|--|--|
| Detetor de anomalias | <li> Deteta anomalias como ocorrem em tempo real. <li> Deteta anomalias em todo o seu conjunto de dados como um lote. <li> Infere a gama normal esperada dos seus dados. <li> Suporta o ajuste de sensibilidade à deteção de anomalias para melhor se adaptar aos seus dados. |

Para obter informações detalhadas sobre as APIs, consulte:
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve cumprir os seguintes pré-requisitos antes de utilizar os recipientes do Detetor de Anomalias:

|Necessário|Objetivo|
|--|--|
|Motor Docker| Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Windows](https://docs.docker.com/docker-for-windows/)e [Linux.](https://docs.docker.com/engine/installation/#supported-platforms) Para um primer sobre o Docker e o básico do contentor, consulte a visão geral do [Docker.](https://docs.docker.com/engine/docker-overview/)<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar os recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de comandos básicos de `docker`.| 
|Recurso do Detetor de Anomalias |Para utilizar estes recipientes, deve ter:<br><br>Um recurso do Detetor de _Anomalias_ Azure para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas de Visão Geral e Teclas do detetor de **anomalias** do portal Azure e são necessários para iniciar o recipiente.<br><br>**{API_KEY}** : Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}** : O ponto final fornecido na página **'Visão Geral'**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registo de contentores

Primeiro deve completar e submeter o formulário de pedido de pedido de detetor de [anomalias](https://aka.ms/adcontainer) para solicitar o acesso ao recipiente.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela que se segue descreve os núcleos e memória cpu mínimos e recomendados para alocar para o recipiente detetor de anomalias.

| QPS (consultas por segundo) | Mínimo | Recomendado |
|-----------|---------|-------------|
| 10 QPS | 4 núcleo, 1-GB de memória | 8 memória de 2-GB de núcleo |
| 20 QPS | 8 núcleo, 2-GB memória | 16 memória de 4-GB de núcleo |

Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às definições `--cpus` e `--memory`, que são utilizadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

Utilize o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor.

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker puxar para o recipiente do Detetor de Anomalias

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Uma vez que o recipiente esteja no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o recipiente. Consulte a recolha de [parâmetros necessários](#gathering-required-parameters) para obter os valores `{ENDPOINT_URI}` e `{API_KEY}`.

[Exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um recipiente de detetor de anomalias a partir da imagem do recipiente
* Atribui um núcleo cpu e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro. 

> [!IMPORTANT]
> As opções de `Eula`, `Billing`e `ApiKey` devem ser especificadas para funcionar o contentor; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](#billing)

### <a name="running-multiple-containers-on-the-same-host"></a>Executar vários recipientes no mesmo hospedeiro

Se pretender executar vários contentores com portas expostas, certifique-se de que executa cada recipiente com uma porta diferente. Por exemplo, executar o primeiro recipiente no porto 5000 e o segundo contentor no porto 5001.

Substitua a `<container-registry>` e a `<container-name>` os valores dos recipientes utilizados. Estes não têm de ser o mesmo recipiente. Pode ter o recipiente do Detetor de Anomalias e o recipiente LUIS a funcionar juntos no HOSPEDEIRO ou pode ter vários recipientes do Detetor de Anomalias em funcionamento. 

Gereno primeiro recipiente no porto 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Corra o segundo contentor no porto 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cada recipiente subsequente deve estar numa porta diferente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

O recipiente fornece APIs finais de previsão de consulta baseadas em REST. 

Utilize o hospedeiro, http://localhost:5000, para apis de contentor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Pare o recipiente

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](anomaly-detector-container-configuration.md#mount-settings) de saída e uma exploração de madeira ativada, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou funcionamento do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes do Detetor de Anomalias enviam informações de faturação para o Azure, utilizando um recurso do Detetor de _Anomalias_ na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](anomaly-detector-container-configuration.md)

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes do Detetor de Anomalias. Em resumo:

* O Detetor de Anomalias fornece um recipiente Linux para o Docker, encapsulando a deteção de anomalias com lote vs streaming, inferência de alcance esperada e afinação de sensibilidade.
* As imagens do contentor são descarregadas a partir de um registo privado de contentores Azure dedicado à pré-visualização de contentores.
* Executam imagens de contentor no Docker.
* Pode utilizar a API REST ou SDK para ligar para operações em recipientes do Detetor de Anomalias, especificando o uri hospedeiro do recipiente.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes (por exemplo, os dados da série de tempo que estão a ser analisados) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Rever [Configure recipientes](anomaly-detector-container-configuration.md) para configurações de configuração
* [Implante um recipiente de detetor de anomalias para instâncias de contentores de Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
