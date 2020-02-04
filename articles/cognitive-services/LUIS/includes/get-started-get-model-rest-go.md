---
title: Obtenha modelo com chamada REST em Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a153416a247ec3a38ec29e95b83fa919e765942b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966845"
---
## <a name="prerequisites"></a>Pré-requisitos

* Compreensão da língua azure - chave de caracteres de recursos 32 e url de ponto final de autor. Criar com o [portal Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) ou [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Importe o aplicativo [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) do repositório do GitHub cognitiva-Services-Language-Understanding.
* A ID do aplicativo LUIS para o aplicativo TravelAgent importado. O ID da aplicação é apresentado no dashboard de aplicações.
* A ID da versão no aplicativo que recebe o declarações. O ID predefinido é "0.1".
* Linguagem de programação [Go](https://golang.org/)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Ficheiro JSON de expressões de exemplo

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Alterar modelo programaticamente

Use o Go para adicionar uma [API](https://aka.ms/luis-apim-v3-authoring) de entidade aprendida por máquina ao aplicativo.

1. Crie um novo ficheiro com o nome `predict.go`. Adicione o seguinte código:

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. Substitua os valores a partir de `YOUR-` pelos seus próprios valores.

    |Proteção das|Finalidade|
    |--|--|
    |`YOUR-KEY`|A tua chave de autor de 32 personagens.|
    |`YOUR-ENDPOINT`| O seu ponto final de URL de autoria. Por exemplo, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Definiu o seu nome de recurso quando criou o recurso.|
    |`YOUR-APP-ID`| O seu ID de aplicativo LUIS. |

    As chaves e recursos atribuídos são visíveis no portal LUIS na secção Gerir, na página de **recursos do Azure.** O ID da aplicação está disponível na mesma secção Gerir, na página definições de **aplicações.**

1. Com um pedido de comando no mesmo diretório onde criou o ficheiro, insira o seguinte comando para compilar o ficheiro Go:

    ```console
    go build model.go
    ```

1. Execute a aplicação do Go a partir da linha de comandos ao introduzir o seguinte texto na linha de comandos:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este guia de início rápido, exclua o arquivo do sistema de arquivos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Práticas recomendadas para um aplicativo](../luis-concept-best-practices.md)