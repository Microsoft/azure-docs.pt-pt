---
title: Obter modelo com chamada REST emC#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a21834cec456ded82c15dc916cc4991a196ea22c
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76967008"
---
## <a name="prerequisites"></a>Pré-requisitos

* Compreensão da língua azure - chave de caracteres de recursos 32 e url de ponto final de autor. Criar com o [portal Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importe o aplicativo [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório do GitHub cognitiva-Services-Language-Understanding.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. O ID da aplicação é apresentado no dashboard de aplicações.
* A ID da versão no aplicativo que recebe o declarações. O ID predefinido é "0.1".
* [Python 3.6](https://www.python.org/downloads/) ou posterior.
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]


## <a name="change-model-programmatically"></a>Alterar modelo programaticamente

Use o Go para adicionar uma [API](https://aka.ms/luis-apim-v3-authoring) de entidade aprendida por máquina ao aplicativo.

1. Crie um novo ficheiro com o nome `model.py`. Adicione o seguinte código:

    ```python
    ########### Python 3.6 #############
    import requests

    # 32 character Authoring key
    LUIS_authoringKey  = "YOUR-KEY"

    LUIS_APP_ID = "YOUR-APP-ID"

    # Authoring endpoint, example: your-resource-name.api.cognitive.microsoft.com
    LUIS_ENDPOINT = "YOUR-ENDPOINT"

    # The version number of your LUIS app
    LUIS_APP_VERSION = "0.1"

    URI_AddUtterances = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/examples'
    URI_Train = f'https://{LUIS_ENDPOINT}/luis/authoring/v3.0-preview/apps/{LUIS_APP_ID}/versions/{LUIS_APP_VERSION}/train'

    HEADERS = {'Ocp-Apim-Subscription-Key': LUIS_authoringKey}

    def addUtterances():
        r = requests.post(URI_AddUtterances,headers=HEADERS)
        print(r.json())

    def train():
        r = requests.post(URI_Train,headers=HEADERS)
        print(r.json())

    def trainStatus():
        r = requests.get(URI_Train,headers=HEADERS)
        print(r.json())

    addUtterances()
    train()
    trainStatus()
    ```

1. Substitua os valores a partir de `YOUR-` pelos seus próprios valores.

    |Proteção das|Finalidade|
    |--|--|
    |`YOUR-KEY`|A tua chave de autor de 32 personagens.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de autoria. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Definiu o seu nome de recurso quando criou o recurso.|
    |`YOUR-APP-ID`| O seu ID de aplicativo LUIS. |

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos do Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página definições de **aplicações.**

1. Com um prompt de comando no mesmo diretório em que você criou o arquivo, digite o seguinte comando para executar o arquivo:

    ```console
    python model.py
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)
