---
title: Instalar e executar contêineres-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Como transferir, instalar e executar contentores para análise de texto neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: f658e8d0f820ccec513b5665fc1ce94c083c3b3e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703532"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contêineres de Análise de Texto

Os contêineres permitem executar as APIs de análise de texto em seu próprio ambiente e são ótimos para seus requisitos específicos de segurança e governança de dados. Os contêineres de Análise de Texto fornecem processamento de idioma natural avançado sobre texto bruto e incluem três funções principais: análise de sentimentos, extração de frases-chave e detecção de idioma. No momento, não há suporte para vinculação de entidade em um contêiner.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contêineres de Análise de Texto, você deve ter o computador host e os ambientes de contêiner.

## <a name="preparation"></a>Preparação

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de análise de texto:

|Requerido|Objetivo|
|--|--|
|Mecanismo do Docker| Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de comandos `docker` básicos.| 
|Análise de Texto recurso |Para usar o contêiner, você deve ter:<br><br>Um [recurso de análise de texto](../../cognitive-services-apis-create-account.md) do Azure para obter a chave de API e o URI de ponto de extremidade associados. Ambos os valores estão disponíveis na Análise de Texto visão geral e nas páginas de chaves do portal do Azure e são necessários para iniciar o contêiner.<br><br>**{API_KEY}** : Uma das duas chaves de recurso disponíveis na página **chaves**<br><br>**{ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página **visão geral**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os núcleos de CPU mínimos e recomendados, pelo menos 2,6 GHz (gigahertz) ou mais rápida e memória, em gigabytes (GB), ao alocar para cada contentor de análise de texto.

| Contentor | Mínimo | Recomendado | TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|Extração de Expressões-Chave | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Deteção de Idioma | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Análise de Sentimento 2. x | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Análise de Sentimento 3. x | 1 núcleo, 2 GB de memória | 4 núcleos, 4 GB de memória |15, 30|

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS-transações por segundo

O núcleo e a `--cpus` memória correspondem às configurações e `--memory` , que são `docker run` usadas como parte do comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com`docker pull`

Imagens de contentor para análise de texto estão disponíveis a partir do registo de contentor do Microsoft.

| Contentor | Repositório |
|-----------|------------|
|Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
|Análise de Sentimento 2. x| `mcr.microsoft.com/azure-cognitive-services/sentiment` |
|Análise de Sentimento 3. x| `containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0` |

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner do registro de contêiner da Microsoft.

Para obter uma descrição completa de etiquetas disponíveis para os contentores de análise de texto, consulte os seguintes contentores sobre o Docker Hub:

* [Extração de expressões-chave](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Deteção de idioma](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Análise de sentimentos](https://go.microsoft.com/fwlink/?linkid=2018654)

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Pull do Docker para o contêiner de extração de frases-chave

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Pull do Docker para o contêiner de detecção de idioma

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-2x-container"></a>Pull do Docker para o contêiner de sentimentos 2. x

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

### <a name="docker-pull-for-the-sentiment-3x-container"></a>Pull do Docker para o contêiner de sentimentos 3. x

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run)com as configurações de cobrança necessárias. Mais [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.
1. Consulte o ponto de extremidade de previsão do contêiner para [v2](#query-the-containers-v2-prediction-endpoint) ou [v3](#query-the-containers-v3-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Execute o contêiner com`docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. Consulte [coletando parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{Endpoint_URI}` valores `{API_Key}` e.

[Exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

### <a name="run-v2-container-example-of-docker-run-command"></a>Executar exemplo de contêiner v2 do comando de execução do Docker

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Executa um contêiner de frase-chave da imagem de contêiner
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

### <a name="run-v3-container-example-of-docker-run-command"></a>Executar exemplo de contêiner V3 do comando de execução do Docker

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0 \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Executa um contêiner de frase-chave da imagem de contêiner
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host.

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-v2-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão v2 do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST.

Use o host, `https://localhost:5000`, para APIs de contêiner.

## <a name="query-the-containers-v3-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão V3 do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST.

Use o host, `https://localhost:5000`, para APIs de contêiner.

### <a name="v3-api-request-post-body"></a>Corpo de POSTAgem de solicitação de API v3

O JSON a seguir é um exemplo de um corpo de POSTAgem da solicitação de API V3:

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="v3-api-response-body"></a>Corpo da resposta da API v3

O JSON a seguir é um exemplo de um corpo de POSTAgem da solicitação de API V3:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se você executar o contêiner com uma [montagem](../text-analytics-resource-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem ao iniciar ou executar o contêiner.

## <a name="billing"></a>Faturação

Os contêineres de Análise de Texto enviam informações de cobrança para o Azure, usando um recurso de _análise de texto_ em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e análise de texto contentores em execução. Em resumo:

* Análise de texto fornece três contentores do Linux para o Docker, encapsulando a extração de expressões-chave, deteção de idioma e análise de sentimentos.
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR) no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações em contentores de análise de texto ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](../text-analytics-resource-container-config.md) para definições de configuração
* Consulte as [perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.
