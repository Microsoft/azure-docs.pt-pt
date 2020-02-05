---
title: 'Início rápido: biblioteca de cliente do Análise de Texto v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Utilize este quickstart para ligar as suas aplicações à API textanalytics dos Serviços Cognitivos Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/23/2020
ms.author: aahi
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 87cf5f226a8b2d1efcc0ee967d84f6e0002cb03d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987924"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Início rápido: usar a biblioteca de cliente Análise de Texto

Introdução à biblioteca de cliente do Análise de Texto. Siga estas etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

Use a biblioteca de cliente Análise de Texto para executar:

* Análise de sentimentos
* Deteção de idioma
* Reconhecimento de entidades
* Extração de expressões chave

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * A versão de pré-visualização mais recente da API text analytics é `3.0-preview`, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e reconhecimento de [entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A versão mais recente é `2.1`.
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código neste artigo usa métodos síncronos e o armazenamento de credenciais não protegidas por motivos de simplicidade. Para cenários de produção, é recomendável usar os métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * A versão de pré-visualização mais recente da API text analytics é `3.0-preview`, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e reconhecimento de [entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A versão mais recente é `2.1`.
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código neste artigo usa métodos síncronos e o armazenamento de credenciais não protegidas por motivos de simplicidade. Para cenários de produção, é recomendável usar os métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * Este quickstart é apenas para versão `3.0-preview` da biblioteca de clientes Text Analytics, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e reconhecimento de [entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código neste artigo usa métodos síncronos e o armazenamento de credenciais não protegidas por motivos de simplicidade. Para cenários de produção, é recomendável usar os métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * A versão de pré-visualização mais recente da API text analytics é `3.0-preview`, que inclui uma pré-visualização pública para melhor análise de [sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) e reconhecimento de [entidades nomeadas (NER)](../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features). A versão mais recente é `2.1`.
>    * Certifique-se de que segue apenas as instruções para a versão que está a utilizar.
> * O código neste artigo usa métodos síncronos e o armazenamento de credenciais não protegidas por motivos de simplicidade. Para cenários de produção, é recomendável usar os métodos assíncronos em lote para desempenho e escalabilidade. Consulte a documentação de referência abaixo.

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Apoio linguístico adicional

Se você tiver clicado nessa guia, provavelmente não verá um guia de início rápido em sua linguagem de programação favorita. Não se preocupe, temos mais quickstarts disponíveis. Utilize a tabela para encontrar a amostra certa para a sua linguagem de programação.

| Linguagem | Versão disponível | 
|----------|------------------------|
| Ruby     | [Versão 2.1](ruby-sdk.md)          | 
| Go       | [Versão 2.1](go-sdk.md)          | 

::: zone-end

## <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar e remover uma assinatura de serviços cognitivas, poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os outros recursos associados a ele.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explorar uma solução](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Descrição Geral da Análise de Texto](../overview.md)
* [Análise de sentimentos](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconhecimento de entidade](../how-tos/text-analytics-how-to-entity-linking.md)
* [Detectar idioma](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Reconhecimento de idioma](../how-tos/text-analytics-how-to-language-detection.md)
