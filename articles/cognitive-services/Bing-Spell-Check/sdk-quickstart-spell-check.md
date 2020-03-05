---
title: 'Quickstart: Verifique a ortografia com o Bing Spell Check SDK paraC#'
titleSuffix: Azure Cognitive Services
description: Começar a usar a API bing spell check REST para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.openlocfilehash: 1cda7032d5bfe58e9f8bcbdb8b18dd597a691441
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273536"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Quickstart: Verifique a ortografia com o Bing Spell Check SDK paraC#

Utilize este arranque rápido para começar a verificar o C#feitiço com o Bing Spell Check SDK para . Embora o Bing Spell Check tenha uma API REST compatível com a maioria dos idiomas de programação, o SDK fornece uma forma fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dependências da aplicação

* Qualquer edição do [Visual Studio 2017 ou mais tarde.](https://visualstudio.microsoft.com/downloads/)
* O pacote de [nuget](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) de verificação de feitiço bing

Para adicionar o Bing Spell Check SDK ao seu projeto, selecione **Manage NuGet Packages** from **Solution Explorer** in Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. O pacote também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie C# uma nova solução de consola no Estúdio Visual. Em seguida, adicione a seguinte declaração `using`.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Criar uma nova classe. Em seguida, crie uma função assíncrona chamada `SpellCheckCorrection()` que pega numa chave de subscrição e envia o pedido de verificação ortográfica.

3. Instantie o cliente criando um novo objeto `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Envie o pedido e leia a resposta

1. Na função acima criada, execute os seguintes passos. Envie o pedido de verificação de feitiços com o cliente. Adicione o texto a ser verificado no parâmetro `text` e ajuste o modo para `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Obtenha o primeiro resultado da verificação do feitiço, se houver um. Imprima a primeira palavra mal escrita (símbolo) devolvida, o tipo de token e o número de sugestões.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Obtenha a primeira correção sugerida, se houver uma. Imprima a pontuação da sugestão e a palavra sugerida. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>Executar a aplicação

Construa e gereno seu projeto. Se estiver a usar o Visual Studio, prima **F5** para desinserm o ficheiro.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma única página web-app](tutorials/spellcheck.md)

- [O que é a API de Bing Spell Check?](overview.md)
- [Guia de C# referência sdk de bing spell check](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
