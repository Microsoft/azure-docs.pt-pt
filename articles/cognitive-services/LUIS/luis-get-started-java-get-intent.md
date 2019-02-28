---
title: Obter a intenção, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido de Java, utilize uma aplicação do LUIS pública disponível para determinar a intenção de um utilizador de texto de conversação.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 02e03868f5a48088b78d5d9b0221387212f248cf
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958716"
---
# <a name="quickstart-get-intent-using-java"></a>Início rápido: Objetivo de GET com Java

Neste início rápido, transmita expressões a um ponto final de LUIS e obtenha a intenção e as entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação 

Pode utilizar o Java para aceder aos mesmos resultados que viu na janela do browser no passo anterior. 

1. Copie o seguinte código para criar uma classe num ficheiro denominado `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Substitua o valor da variável `YOUR-KEY` pela sua chave do LUIS.

3. Compile o programa Java com `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Execute a aplicação com `java -cp ":lib/*" LuisGetRequest.java`. Apresenta o mesmo JSON que viu anteriormente na janela do browser.

    ![A janela da consola apresenta o resultado JSON do LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Elimine o ficheiro Java. 

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Adicionar expressões](luis-get-started-java-add-utterance.md)
