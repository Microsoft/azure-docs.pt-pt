---
title: Instalar e executar contentores - Análise de Texto
titleSuffix: Azure Cognitive Services
description: Como transferir, instalar e executar contentores para análise de texto neste tutorial passo a passo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 65033f9b6599d690b1097b4b78aa01148a40fc39
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037509"
---
# <a name="install-and-run-text-analytics-containers"></a>Instalar e executar contentores da Análise de Texto

Os contentores permitem-lhe executar as APIs analíticas do texto no seu próprio ambiente e são ótimos para os seus requisitos específicos de segurança e governança de dados. Os recipientes Text Analytics fornecem processamento avançado de linguagem natural sobre texto cru, e incluem três funções principais: análise de sentimentos, extração de frases-chave e deteção de linguagem. A ligação de entidades não é atualmente suportada num contentor.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> A conta gratuita está limitada a 5.000 transações por mês e apenas os níveis de preços **Free** e **Standard** são <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">válidos <span class="docon docon-navigate-external x-hidden-focus"></span> </a> para contentores. Para obter mais informações sobre as taxas de pedido de transação, consulte [Limites de Dados](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview#data-limits).

## <a name="prerequisites"></a>Pré-requisitos

Para executar qualquer um dos recipientes text Analytics, deve ter o computador de acolhimento e os ambientes de contentores.

## <a name="preparation"></a>Preparação

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores de análise de texto:

|Necessário|Objetivo|
|--|--|
|Motor Docker| Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Windows](https://docs.docker.com/docker-for-windows/)e [Linux.](https://docs.docker.com/engine/installation/#supported-platforms) Para um primer sobre o Docker e o básico do contentor, consulte a visão geral do [Docker.](https://docs.docker.com/engine/docker-overview/)<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar os recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de comandos básicos de `docker`.| 
|Recurso de Análise de Texto |Para utilizar o recipiente, deve ter:<br><br>Um recurso Azure [Text Analytics](../../cognitive-services-apis-create-account.md) para obter a chave API associada e o ponto final URI. Ambos os valores estão disponíveis nas páginas de Visão Geral e Chaves do portal Azure e são necessários para iniciar o recipiente.<br><br>**{API_KEY}** : Uma das duas teclas de recursos disponíveis na página **Keys**<br><br>**{ENDPOINT_URI}** : O ponto final fornecido na página **'Visão Geral'**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os núcleos de CPU mínimos e recomendados, pelo menos 2,6 GHz (gigahertz) ou mais rápida e memória, em gigabytes (GB), ao alocar para cada contentor de análise de texto.

# <a name="key-phrase-extraction"></a>[Extração de frase chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detection"></a>[Deteção de Idiomas](#tab/language)

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS - transações por segundo

O núcleo e a memória correspondem às definições `--cpus` e `--memory`, que são utilizadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

As imagens do contentor para O Análise de Texto estão disponíveis no Registo de Contentores da Microsoft.

# <a name="key-phrase-extraction"></a>[Extração de frase chave](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detection"></a>[Deteção de Idiomas](#tab/language)

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker puxar para os recipientes De Análise de Texto

# <a name="key-phrase-extraction"></a>[Extração de frase chave](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Deteção de Idiomas](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Uma vez que o recipiente esteja no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de faturação necessárias. Mais [exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do comando `docker run` estão disponíveis.
1. [Consulta do ponto final da previsão do recipiente](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três recipientes. Consulte o [Gathering os parâmetros necessários](#gathering-required-parameters) para obter os valores `{ENDPOINT_URI}` e `{API_KEY}`.

[Exemplos](../text-analytics-resource-container-config.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

# <a name="key-phrase-extraction"></a>[Extração de frase chave](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Deteção de Idiomas](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> As opções de `Eula`, `Billing`e `ApiKey` devem ser especificadas para funcionar o contentor; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](#billing)

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

O recipiente fornece APIs finais de previsão de consulta baseadas em REST.

Utilize o hospedeiro, `http://localhost:5000`, para apis de contentor.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Pare o recipiente

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

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e análise de texto contentores em execução. Em resumo:

* O Text Analytics fornece três recipientes Linux para o Docker, encapsulando várias capacidades:
   * *Extração de frase chave*
   * *Deteção de Idiomas*
   * *Análise de Sentimentos*
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR) no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações em contentores de análise de texto ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Rever [Configure recipientes](../text-analytics-resource-container-config.md) para configurações de configuração
* Consulte [as perguntas frequentes (FAQ)](../text-analytics-resource-faq.md) para resolver problemas relacionados com a funcionalidade.
