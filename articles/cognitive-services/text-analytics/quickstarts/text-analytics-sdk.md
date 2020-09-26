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
ms.date: 09/09/2020
ms.author: aahi
keywords: mineração de texto, análise de sentimento, análise de texto
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: ed87c536c53ab1c07af05a7b8223901591c16e66
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332345"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Quickstart: Use a biblioteca de clientes Text Analytics

Utilize este artigo para começar com a biblioteca de clientes Text Analytics. Siga estes passos para instalar a embalagem e experimente o código de exemplo para o texto de mineração.

Utilize a biblioteca de clientes Text Analytics para realizar:

* Análise de sentimentos
* Deteção de idioma
* Reconhecimento de entidades
* Extração de expressões-chave

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
>    * Certifique-se de que só segue as instruções da versão que está a utilizar.
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * A versão mais recente estável da API text Analytics é `3.0` .
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.

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
> * O código deste artigo utiliza métodos sincronizados e armazenamento de credenciais não garantidos por razões de simplicidade. Para cenários de produção, recomendamos a utilização dos métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Suporte linguístico adicional

Se clicou neste separador, provavelmente não viu um arranque rápido na sua linguagem de programação favorita. Não se preocupe, temos mais rapidez disponíveis. Utilize a tabela para encontrar a amostra certa para a sua linguagem de programação.

| Linguagem | Versão disponível | 
|----------|------------------------|
| Ruby     | [Versão 2.1](ruby-sdk.md) | 
| Go       | [Versão 2.1](go-sdk.md) | 

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
* [Detetar idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento linguístico](../how-tos/text-analytics-how-to-language-detection.md)
