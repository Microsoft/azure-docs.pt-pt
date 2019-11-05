---
title: 'Início rápido: obter a intenção com as APIs REST-LUIS'
titleSuffix: Azure Cognitive Services
description: Neste início rápido da API REST, use um aplicativo LUIS público disponível para determinar a intenção de um usuário de texto de conversação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/17/2019
ms.author: diberry
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 3a8badb74bb8919876f3c0670d785f44fbcbb397
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499589"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Início rápido: obter a intenção com as APIs REST

Neste guia de introdução, irá aprender a utilizar uma aplicação LUIS pública disponível para determinar a intenção de um utilizador a partir do texto de uma conversação. Envie a intenção do utilizador como texto para o ponto final de predição HTTP da aplicação pública. No ponto final, o LUIS aplica o modelo da aplicação pública para analisar o significado do texto de linguagem natural e assim determinar a intenção geral e extrair os dados relevantes para o domínio de requerente da aplicação. 

Este guia de início rápido utiliza a API REST de ponto final. Para obter mais informações, veja a [documentação de APIs de ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Para este artigo, precisa de uma conta gratuita do [LUIS](https://www.luis.ai). 

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"

[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]

::: zone-end

::: zone pivot="programming-language-nodejs"

[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-go"

[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]

::: zone-end
