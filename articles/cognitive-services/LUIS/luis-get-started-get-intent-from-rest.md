---
title: Como obter uma intenção usando a API REST
description: Neste artigo, utilize uma aplicação pública disponível LUIS para determinar a intenção de um utilizador a partir de texto de conversação.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b49d90ffe2a262895727fdd4ef5f8a7950104102
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437019"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>Como obter uma intenção usando as APIs REST

Neste artigo, utilizará uma aplicação LUIS para determinar a intenção de um utilizador a partir de texto sonoro. Envie a intenção do utilizador como texto para o ponto final de previsão HTTP da aplicação Pizza. No ponto final, a LUIS aplica o modelo da app Pizza para analisar o texto de linguagem natural para significado, determinando a intenção geral e extraindo dados relevantes para o domínio da app.

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
