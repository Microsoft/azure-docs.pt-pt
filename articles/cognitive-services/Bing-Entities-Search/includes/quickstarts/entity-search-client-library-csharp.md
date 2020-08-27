---
title: Bing Entity Search C# biblioteca de clientes quickstart
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931897"
---
Use este quickstart para começar a procurar entidades com a biblioteca de clientes Bing Entity Search para C#. Embora a Bing Entity Search tenha uma API REST compatível com a maioria das linguagens de programação, a biblioteca do cliente proporciona uma forma fácil de integrar o serviço nas suas aplicações. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017 ou posterior](https://www.visualstudio.com/downloads/).
* O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O [pacote Bing News Search SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). A instalação deste pacote também instala o seguinte:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Para adicionar a biblioteca de clientes Bing Entity Search ao seu projeto Visual Studio, utilize a opção **'Gerir pacotes'** do **Solution Explorer**e adicione o `Microsoft.Azure.CognitiveServices.Search.EntitySearch` pacote.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Criar e inicializar uma aplicação

1. criar uma nova solução de consola C# no Visual Studio. Em seguida, adicione o seguinte no ficheiro de código principal.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Criar um cliente e enviar um pedido de pesquisa

1. Criar um novo cliente de pesquisa. Adicione a sua chave de subscrição criando um novo `ApiKeyServiceClientCredentials` .

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Utilize a função do cliente `Entities.Search()` para procurar a sua consulta:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Obter e imprimir uma descrição da entidade

1. Se a API devolver os resultados da pesquisa, obtenha a entidade principal de `entityData` .

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Imprima a descrição da entidade principal 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação web de página única](../../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de Pesquisa de Entidade Bing?](../../overview.md )
