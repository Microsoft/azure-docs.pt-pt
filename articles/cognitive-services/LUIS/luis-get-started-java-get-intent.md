---
title: Get intuito, Java-LUIS
titleSuffix: Azure Cognitive Services
description: Neste início rápido de Java, utilize uma aplicação do LUIS pública disponível para determinar a intenção de um utilizador de texto de conversação.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7a80aefd7a88727cf3a2261115c076853bd45b3f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563815"
---
# <a name="quickstart-get-intent-using-java"></a>Início rápido: Obter a intenção usando o Java

Neste início rápido, transmita expressões a um ponto final de LUIS e obtenha a intenção e as entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de Desenvolvimento do Java, Edição Standard)
* [Visual Studio Code](https://code.visualstudio.com/) ou seu IDE favorito
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Pode utilizar o Java para aceder aos mesmos resultados que viu na janela do browser no passo anterior. Certifique-se de adicionar as bibliotecas do Apache ao seu projeto.

1. Copie o seguinte código para criar uma classe num ficheiro denominado `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Substitua o valor da variável `YOUR-KEY` pela sua chave do LUIS.

3. Substitua pelo caminho do arquivo e compile o programa Java a partir de uma linha `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`de comando:.

4. Substitua pelo caminho do arquivo e execute o aplicativo de uma linha de comando `java -cp .;<FILE_PATH>\* LuisGetRequest.java`:. Apresenta o mesmo JSON que viu anteriormente na janela do browser.

    ![A janela da consola apresenta o resultado JSON do LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Exclua a pasta de arquivo/projeto Java.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Adicionar expressões](luis-get-started-java-add-utterance.md)
