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
ms.openlocfilehash: 5e5a186be19c0ecc9730bc79bddff41fb6bbb571
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401664"
---
# <a name="quickstart-get-intent-using-java"></a>Início rápido: Objetivo de GET com Java

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

Pode utilizar o Java para aceder aos mesmos resultados que viu na janela do browser no passo anterior. Certifique-se de que adicionar as bibliotecas do Apache a seu projeto.

1. Copie o seguinte código para criar uma classe num ficheiro denominado `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Substitua o valor da variável `YOUR-KEY` pela sua chave do LUIS.

3. Substitua o caminho do ficheiro e compile o programa de java numa linha de comandos: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Substitua pelo seu caminho de ficheiro e execute a aplicação a partir de uma linha de comando: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Apresenta o mesmo JSON que viu anteriormente na janela do browser.

    ![A janela da consola apresenta o resultado JSON do LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Elimine a pasta de ficheiro/projeto Java.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Adicionar expressões](luis-get-started-java-add-utterance.md)
