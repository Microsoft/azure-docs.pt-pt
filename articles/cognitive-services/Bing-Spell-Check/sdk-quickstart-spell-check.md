---
title: 'Quickstart: Verifique a ortografia com o Bing Spell Check SDK para C #'
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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78273536"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Quickstart: Verifique a ortografia com o Bing Spell Check SDK para C #

Utilize este quickstart para começar a verificar o feitiço com o Bing Spell Check SDK para C#. Embora o Bing Spell Check tenha uma API REST compatível com a maioria dos idiomas de programação, o SDK fornece uma forma fácil de integrar o serviço nas suas aplicações. O código fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dependências da aplicação

* Qualquer edição do [Visual Studio 2017 ou mais tarde.](https://visualstudio.microsoft.com/downloads/)
* O pacote de [nuget](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) de verificação de feitiço bing

Para adicionar o Bing Spell Check SDK ao seu projeto, selecione **Manage NuGet Packages** from **Solution Explorer** in Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. O pacote também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma nova solução de consola C# no Estúdio Visual. Em seguida, `using` adicione a seguinte declaração.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Criar uma nova classe. Em seguida, crie uma função assíncrona chamada `SpellCheckCorrection()` que pega numa chave de subscrição e envia o pedido de verificação de feitiços.

3. Instantie o cliente `ApiKeyServiceClientCredentials` criando um novo objeto. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Envie o pedido e leia a resposta

1. Na função acima criada, execute os seguintes passos. Envie o pedido de verificação de feitiços com o cliente. Adicione o texto a ser `text` verificado no parâmetro e `proof`ajuste o modo para .  
    
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
> [Criar uma aplicação Web de página única](tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](overview.md)
- [Guia de referência bing spell check C# SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
