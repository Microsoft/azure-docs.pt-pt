---
title: 'Início rápido: Verifique a ortografia com o SDK de verificação de ortografia para do Bing paraC#'
titlesuffix: Azure Cognitive Services
description: Começar a utilizar a API de REST de verificação do Bing ortográfica para verificar a ortografia e gramática.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: v-gedod
ms.openlocfilehash: ae558c40a3eb30cb239b19a59542d9d83d5a9566
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886150"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Início rápido: Verifique a ortografia com o SDK de verificação de ortografia para do Bing paraC#

Utilizar este início rápido para começar a ortográfica com o SDK de verificação de ortografia para do Bing para C#. Enquanto a verificação de ortografia do Bing tem uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dependências da aplicação

* Qualquer edição do [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
* A verificação de ortografia do Bing [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Para adicionar o SDK de verificação de ortográfica do Bing ao seu projeto, clique em `Manage NuGet Packages` no Explorador de soluções no Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. O pacote também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. criar um novo C# consola de solução no Visual Studio. Em seguida, adicione o seguinte `using` instrução.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Crie uma nova classe. Em seguida, crie uma função assíncrona chamada `SpellCheckCorrection()` que usa uma chave de subscrição e envia o pedido de verificação ortográfica.

3. Criar uma instância do cliente ao criar um novo `ApiKeyServiceClientCredentials` objeto. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Enviar a solicitação e leio a resposta

1. Na função criada acima, execute os seguintes passos. Envie o pedido de verificação ortográfica com o cliente. Adicione o texto a ser verificado para o `text` parâmetro e definir o modo como `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Obtenha o resultado de verificação ortográfica primeiro, se existir um. A primeira palavra escrita incorretamente (token) devolvidas o tipo de token e o número de sugestões de impressão.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Obter a correção sugerida primeiro, se existir um. Imprima a pontuação de sugestão e a palavra sugerida. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
}

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)