---
title: 'Obtenha modelo com chamada REST em C #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/14/2020
ms.author: diberry
ms.openlocfilehash: 4d8da7d2bc51c4fc4ebc8d71f230f24f20b3aa24
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368448"
---
## <a name="prerequisites"></a>Pré-requisitos

* Compreensão da língua azure - chave de caracteres de recursos 32 e url de ponto final de autor. Criar com o [portal Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importar a aplicação [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório gitHub compreensivo cognitivo-serviços.
* O ID da aplicação LUIS para a aplicação TravelAgent importada. O ID da aplicação é apresentado no dashboard de aplicações.
* O ID da versão na aplicação que recebe as expressões. O ID predefinido é "0.1".
* [Python 3.6](https://www.python.org/downloads/) ou mais tarde.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Alterar modelo programáticamente

1. Crie um novo ficheiro com o nome `model.py`. Adicione o seguinte código:

    [!code-python[Add example utterances to Language Understanding in python](~/samples-luis/documentation-samples/quickstarts/change-model/python/3.x/add-utterances-3-6.py)]

1. Substitua os valores a começar pelos `YOUR-` seus próprios valores.

    |Informações|Objetivo|
    |--|--|
    |`YOUR-KEY`|A tua chave de autor de 32 personagens.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de autoria. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Definiu o seu nome de recurso quando criou o recurso.|
    |`YOUR-APP-ID`| O seu ID de aplicativo LUIS. |

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos do Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página definições de **aplicações.**

1. Com um pedido de comando no mesmo diretório onde criou o ficheiro, introduza o seguinte comando para executar o ficheiro:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este arranque rápido, elimine o ficheiro do sistema de ficheiros.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Boas práticas para uma aplicação](../luis-concept-best-practices.md)
