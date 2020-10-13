---
title: Instale e execute os recipientes Docker para a API do Detetor de Anomalias
titleSuffix: Azure Cognitive Services
description: Use os algoritmos da API do Detetor de Anomalias para encontrar anomalias nos seus dados, no local usando um recipiente Docker.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: no local, Docker, contentor, streaming, algoritmos
ms.openlocfilehash: ff4d15b33cb261e71ea883c0245afe5781005e38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460005"
---
# <a name="install-and-run-docker-containers-for-the-anomaly-detector-api"></a>Instale e execute os recipientes Docker para a API do Detetor de Anomalias 

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Os recipientes permitem-lhe utilizar o Detetor de Anomalias API o seu próprio ambiente. Os contentores são ótimos para requisitos específicos de governação de dados e segurança. Neste artigo você vai aprender a descarregar, instalar e executar um recipiente de detetor de anomalias.

O Detetor de Anomalias oferece um único recipiente Docker para a utilização da API no local. Utilize o recipiente para:
* Use os algoritmos do Detetor de Anomalias nos seus dados
* Monitorize os dados de streaming e detete anomalias à medida que ocorrem em tempo real.
* Detete anomalias em todo o seu conjunto de dados como um lote. 
* Detete pontos de mudança de tendência no seu conjunto de dados como um lote.
* Ajuste a sensibilidade do algoritmo de deteção de anomalias para melhor se adaptar aos seus dados.

Para obter informações detalhadas sobre a API, consulte:
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve encontrar os seguintes pré-requisitos antes de utilizar recipientes do Detetor de Anomalias:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.|
|Recurso de detetor de anomalias |Para utilizar estes recipientes, deve ter:<br><br>Um recurso _de detetor de anomalia_ azure para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas do detetor de **anomalias** do portal Azure e das páginas Keys e são obrigados a iniciar o contentor.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página **'Vista Geral',**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

A tabela a seguir descreve os núcleos e memórias mínimos e recomendados da CPU para alocar para o detetor de anomalias.

| QPS (Consultas por segundo) | Mínimo | Recomendado |
|-----------|---------|-------------|
| 10 QPS | 4 núcleo, memória de 1-GB | 8 núcleo de memória 2-GB |
| 20 QPS | 8 núcleo, memória de 2 GB | 16 núcleo de memória 4-GB |

Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

Utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar uma imagem de contentor.

| Contentor | Repositório |
|-----------|------------|
| cognitive-services-anomalia-detector | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker puxa para o recipiente do Detetor de Anomalias

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Como usar o recipiente

Uma vez que o recipiente esteja no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.
1. [Consultar o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o contentor. Consulte a [recolha dos parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` valores e `{API_KEY}` valores.

[Exemplos](anomaly-detector-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um detetor de anomalias a partir da imagem do recipiente
* Atribui um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui uma pseudo-TTY para o contentor
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)

### <a name="running-multiple-containers-on-the-same-host"></a>Executando vários contentores no mesmo anfitrião

Se pretender executar vários recipientes com portas expostas, certifique-se de que funciona cada recipiente com uma porta diferente. Por exemplo, executar o primeiro contentor no porto 5000 e o segundo contentor no porto 5001.

Substitua o `<container-registry>` e `<container-name>` pelos valores dos recipientes que utiliza. Estes não têm de ser o mesmo recipiente. Pode ter o recipiente do Detetor de Anomalias e o recipiente LUIS a funcionar em conjunto no HOST ou pode ter vários recipientes de detetor de anomalias em funcionamento.

Executar o primeiro contentor no porto 5000.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Executar o segundo contentor na porta 5001.


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cada recipiente subsequente deve estar numa porta diferente.

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contentor fornece APIs de ponto final de predição de consulta com base em REST.

Utilize o anfitrião, http://localhost:5000, para APIs de contentor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](anomaly-detector-container-configuration.md#mount-settings) de saída e um registo ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os contentores do Detetor de Anomalias enviam informações de faturação para a Azure, utilizando um recurso _de detetor de anomalias_ na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de Detetor de Anomalias. Em resumo:

* O Detetor de Anomalias fornece um recipiente Linux para Docker, encapsulando a deteção de anomalias com streaming de lote vs, inferência de alcance esperada e sintonização de sensibilidade.
* As imagens do contentor são descarregadas a partir de um registo privado de contentores Azure dedicado a contentores.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou a SDK para ligar para operações em contentores do Detetor de Anomalias, especificando o hospedeiro URI do recipiente.
* Deve especificar as informações de faturação ao instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os contentores dos Serviços Cognitivos não enviam dados dos clientes (por exemplo, os dados da série de tempo que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Rever recipientes de configuração](anomaly-detector-container-configuration.md) para configurações de configuração
* [Implantar um detetor de anomalias em instâncias de contentores de Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
