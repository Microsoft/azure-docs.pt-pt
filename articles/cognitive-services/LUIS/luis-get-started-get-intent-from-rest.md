---
title: 'Quickstart: Tenha intenção com APIs REST - LUIS'
description: Neste rest API quickstart, utilize uma aplicação PÚBLICA LUIS disponível para determinar a intenção de um utilizador a partir de texto de conversação.
ms.topic: quickstart
ms.date: 05/18/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 90d98e56e53e28991fb5aada9eab5a7e9c2e69c3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654303"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Quickstart: Obtenha intenção com APIs rest

Neste arranque rápido, utilizará uma aplicação LUIS para determinar a intenção de um utilizador a partir de texto de conversação. Envie a intenção do utilizador como texto para o ponto final de previsão HTTP da aplicação Pizza. No final, a LUIS aplica o modelo da app Pizza para analisar o texto de linguagem natural para significado, determinando a intenção geral e extraindo dados relevantes para o domínio da app.

Este guia de início rápido utiliza a API REST de ponto final. Para obter mais informações, veja a [documentação de APIs de ponto final](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78).

Para este artigo, precisa de uma conta gratuita do [LUIS](https://www.luis.ai).

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
