---
title: Instalar e executar contentores - Análise de Texto
titleSuffix: Azure Cognitive Services
description: Como descarregar, instalar e executar recipientes para Text Analytics neste tutorial de walkthrough.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2d44df1bb828140e662b06ffbe5fb14f207f68e0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877084"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contentores da Análise de Texto

Os contentores permitem-lhe executar as APIs analíticas do texto no seu próprio ambiente e são ótimos para os seus requisitos específicos de segurança e governança de dados. Os recipientes Text Analytics fornecem processamento avançado de linguagem natural sobre texto cru, e incluem três funções principais: análise de sentimentos, extração de frases-chave e deteção de linguagem. A ligação de entidades não é atualmente suportada num contentor.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> A conta gratuita está limitada a 5.000 transações por mês e apenas os níveis de preços **Free** e **Standard** são <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">válidos <span class="docon docon-navigate-external x-hidden-focus"></span> </a> para contentores. Para obter mais informações sobre as taxas de pedido de transação, consulte [Limites de Dados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos recipientes text Analytics, deve ter o computador de acolhimento e os ambientes de contentores.

## <a name="preparation"></a>Preparação

Deve cumprir os seguintes pré-requisitos antes de utilizar os recipientes Text Analytics:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os recipientes se conectem e enviem dados de faturação para o Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar os recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos docker, como registos, repositórios, `docker` contentores e imagens de contentores, bem como conhecimento de comandos básicos.| 
|Recurso de Análise de Texto |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure [Text Analytics](../../cognitive-services-apis-create-account.md) para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas de Visão Geral e Chaves do portal Azure e são necessários para iniciar o recipiente.<br><br>**{API_KEY}**: Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}**: O ponto final fornecido na página **'Visão Geral'**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações de contentores

A tabela seguinte descreve os núcleos cpu mínimos e recomendados, pelo menos 2,6 gigahertz (GHz) ou mais rápido, e memória, em gigabytes (GB), para alocar para cada recipiente text Analytics.

# <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS - transações por segundo

O núcleo e `--cpus` a `--memory` memória correspondem às definições e definições, que são usadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com`docker pull`

As imagens do contentor para O Análise de Texto estão disponíveis no Registo de Contentores da Microsoft.

# <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker puxar para os recipientes De Análise de Texto

# <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Uma vez que o recipiente esteja no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run` do comando estão disponíveis.
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com`docker run`

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três recipientes. Consulte o [Gathering os parâmetros necessários](#gathering-required-parameters) `{ENDPOINT_URI}` para `{API_KEY}` obter os valores e valores necessários.

[Exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

# <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](#billing)

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

O recipiente fornece APIs finais de previsão de consulta baseadas em REST.

Utilize o `http://localhost:5000`hospedeiro, para apis de contentor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](../text-analytics-resource-container-config.md#mount-settings) de saída e uma exploração de madeira ativada, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou funcionamento do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

Os recipientes Text Analytics enviam informações de faturação para o Azure, utilizando um recurso _Text Analytics_ na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](../text-analytics-resource-container-config.md)

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar contentores de Text Analytics. Em resumo:

* O Text Analytics fornece três recipientes Linux para o Docker, encapsulando várias capacidades:
   * *Extração de Expressões-Chave*
   * *Deteção de Idioma*
   * *Análise de Sentimentos*
* As imagens de contentores são descarregadas a partir do Registo de Contentores da Microsoft (MCR) em Azure.
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST ou SDK para ligar para operações em recipientes de Análise de Texto, especificando o uri hospedeiro do recipiente.
* Deve especificar a informação de faturação ao instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem serem ligados ao Azure para medição. Os clientes precisam de permitir que os contentores comuniquem sempre informações de faturação com o serviço de medição. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Rever [Configure recipientes](../text-analytics-resource-container-config.md) para configurações de configuração
* Consulte [as perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados com a funcionalidade.
