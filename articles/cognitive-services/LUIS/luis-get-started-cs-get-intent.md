---
title: Obter a intenção,C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste C# início rápido, utilize uma aplicação do LUIS pública disponível para determinar a intenção de um utilizador de texto de conversação.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 6d72299e4350c472ef552fa6afd96e48fd1869b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710493"
---
# <a name="quickstart-get-intent-using-c"></a>Início rápido: Obter a intenção de utilizarC#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio Community – edição de 2017](https://visualstudio.microsoft.com/vs/community/)
* Linguagem de programação C# (incluída com o VS Community 2017)
* ID da aplicação pública: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção através de programação

Utilize a linguagem C# para consultar o ponto final de predição [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET e obter os mesmos resultados que viu na janela do browser na secção anterior. 

1. Crie uma nova aplicação de consola no Visual Studio. 

    ![Criar uma nova aplicação de consola no Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. No projeto do Visual Studio, no Explorador de Soluções, selecione **Adicionar referência** e, em seguida, selecione **System.Web** no separador Assemblagens.

    ![selecione Adicionar referência, em seguida, selecione System. Web a partir do separador Assemblies](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Substitua Program.cs pelo seguinte código:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Substitua o valor de `YOUR_KEY` pela sua chave do LUIS.

5. Compile e execute a aplicação da consola. Apresenta o mesmo JSON que viu anteriormente na janela do browser.

    ![A janela da consola apresenta o resultado JSON do LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Chaves LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este guia de introdução, feche o projeto do Visual Studio e remova o diretório do projeto do sistema de ficheiros. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Adicionar expressões e formação com C#](luis-get-started-cs-add-utterance.md)
