---
title: Instalar e executar contentores
titleSuffix: Text Analytics -  Azure Cognitive Services
description: Como transferir, instalar e executar contentores para análise de texto neste tutorial passo a passo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: 39c1a212bee552b436ef21d208f4a20086578325
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437854"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contentores de análise de texto

Os contentores de análise de texto fornecem avançada de processamento de linguagem natural relativamente a texto não processado e inclui três funções principais: análise de sentimentos, extração de expressões-chave e deteção de idioma. Ligação de entidades não é atualmente suportado num contentor. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos contentores de análise de texto, tem de ter os ambientes de contentor e de computador do anfitrião.

## <a name="preparation"></a>Preparação

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de análise de texto:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Recurso de análise de texto |Para utilizar o contentor, tem de ter:<br><br>R [ _análise de texto_ ](text-analytics-how-to-access-key.md) recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis nas páginas de descrição geral da análise de texto e as chaves do portal do Azure e são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os núcleos de CPU mínimos e recomendados, pelo menos 2,6 GHz (gigahertz) ou mais rápida e memória, em gigabytes (GB), ao alocar para cada contentor de análise de texto.

| Contentor | Mínimo | Recomendado | TPS<br>(No mínimo, máximo)|
|-----------|---------|-------------|--|
|Extração de Expressões-Chave | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Deteção de Idioma | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|
|Análise de Sentimentos | 1 núcleo, 2 GB de memória | 1 núcleo, 4 GB de memória |15, 30|

* Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Imagens de contentor para análise de texto estão disponíveis a partir do registo de contentor do Microsoft. 

| Contentor | Repositório |
|-----------|------------|
|Extração de Expressões-Chave | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Deteção de Idioma | `mcr.microsoft.com/azure-cognitive-services/language` |
|Análise de Sentimentos | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Utilize o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contentor do registo de contentor do Microsoft.

Para obter uma descrição completa de etiquetas disponíveis para os contentores de análise de texto, consulte os seguintes contentores sobre o Docker Hub:

* [Extração de expressões-chave](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Deteção de idioma](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Análise de sentimentos](https://go.microsoft.com/fwlink/?linkid=2018654)

Utilize o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Solicitação de docker para o contentor de extração de frase chave

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>Solicitação de docker para o contentor de deteção de idioma

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>Solicitação de docker para o contentor de sentimentos

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), o necessário com as definições de faturação. Obter mais [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de chaves de análise de texto do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral da análise de texto do portal do Azure.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de expressões-chave a partir da imagem de contentor
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

Obter mais [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. 

Usar o host, `https://localhost:5000`, para o contentor APIs.

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](../text-analytics-resource-container-config.md#mount-settings) e registo ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem ao iniciar ou executar o contentor. 

## <a name="containers-api-documentation"></a>Documentação da API do contentor

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Faturação

O envio de contentores de análise de texto cobrança informações para o Azure, utilizando um _análise de texto_ recursos na sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](../text-analytics-resource-container-config.md).

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
* Consulte a [perguntas mais frequentes (FAQ) do sobre](../text-analytics-resource-faq.md) para resolver problemas relacionados à funcionalidade.

