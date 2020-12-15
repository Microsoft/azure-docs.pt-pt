---
title: 'Quickstart: Mineração de texto utilizando a biblioteca do cliente Text Analytics'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para realizar análise de sentimento e muito mais, utilizando a API text Analytics da Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: aahi
keywords: mineração de texto, análise de sentimento, análise de texto
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: a1eba2f599a0365f43707ff7c9035d91e8637481
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505827"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Quickstart: Use a biblioteca de clientes Text Analytics e REST API

Utilize este artigo para começar com a biblioteca de clientes Text Analytics e REST API. Siga estes passos para experimentar o código exemplos para o texto mineiro:

* Análise de sentimentos
* Mineração de opinião
* Deteção de idioma
* Reconhecimento de entidades
* Reconhecimento de Informação de Identificação Pessoal
* Extração de expressões-chave


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
>    * Certifique-se de que só segue as instruções da versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.
> * Se quiser utilizar o Text Analytics para operações de saúde ou assíncronas, consulte os exemplos no Github para [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) ou [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.
Se quiser utilizar o Text Analytics para operações de saúde ou assíncronas, consulte os exemplos no Github para [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) ou [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
>    * Certifique-se de que só segue as instruções da versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.
> * Também pode executar esta versão da biblioteca de clientes Text Analytics [no seu browser.](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
>    * Certifique-se de que só segue as instruções da versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo. Se quiser utilizar o Text Analytics para operações de saúde ou assíncronas, consulte os exemplos no Github para [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) ou [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
>    * Certifique-se de que só segue as instruções da versão que está a utilizar.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender limpar e remover uma subscrição dos Serviços Cognitivos, pode eliminar o grupo de recursos ou recursos. A eliminação do grupo de recursos também elimina quaisquer outros recursos que lhe sejam associados.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore uma solução](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Descrição geral da Análise de Texto](../overview.md)
* [Análise de sentimento](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidades](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detetar linguagem](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento linguístico](../how-tos/text-analytics-how-to-language-detection.md)
