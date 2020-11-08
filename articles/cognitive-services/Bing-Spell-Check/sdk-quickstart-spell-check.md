---
title: 'Quickstart: Verifique a ortografia com o SDK de verificação ortográfica de Bing para C #'
titleSuffix: Azure Cognitive Services
description: Começa a utilizar a API Bing Spell Check REST para verificar a ortografia e a gramática.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: b2df7bab9bceaefae5793e4802eba6a6aa218e96
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364807"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Quickstart: Verifique a ortografia com o SDK de verificação ortográfica de Bing para C #

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Utilize este arranque rápido para começar a verificar o feitiço com o Bing Spell Check SDK para C#. Embora o Bing Spell Check tenha uma API REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dependências da aplicação

* Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/).
* O pacote Bing Spell Check [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Para adicionar o Bing Spell Check SDK ao seu projeto, **selecione Gerir pacotes NuGet** do **Solution Explorer** em Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. O pacote também instala as seguintes dependências:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Criar e inicializar a aplicação

1. Crie uma nova solução de consola C# no Visual Studio. Em seguida, adicione a seguinte `using` declaração.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Criar uma nova classe. Em seguida, crie uma função assíncronea chamada `SpellCheckCorrection()` que pega numa chave de subscrição e envia o pedido de verificação ortográfica.

3. Instantânear o cliente criando um novo `ApiKeyServiceClientCredentials` objeto. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Envie o pedido e leia a resposta

1. Na função acima criada, execute os seguintes passos. Envie o pedido de verificação ortográfica com o cliente. Adicione o texto a ser verificado ao `text` parâmetro e desa ajuste o modo para `proof` .  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Obtenha o primeiro resultado de verificação ortográfica, se houver um. Imprima a primeira palavra mal escrita (token) devolvida, o tipo de símbolo, e o número de sugestões.

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

3. Obtenha a primeira correção sugerida, se houver uma. Imprima a pontuação da sugestão, e a palavra sugerida. 

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

Construa e execute o seu projeto. Se estiver a utilizar o Visual Studio, prima **F5** para depurar o ficheiro.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](tutorials/spellcheck.md)

- [O que é a API de Verificação Ortográfica do Bing?](overview.md)
- [Bing Spell Check C# Guia de referência SDK](/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)