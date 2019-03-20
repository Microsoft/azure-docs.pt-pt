---
title: 'Início rápido: Pesquisa de entidades com o SDK de pesquisa de entidades do Bing paraC#'
titleSuffix: Azure Cognitive Services
description: Utilize este guia de introdução para procurar entidades com o SDK de pesquisa de entidades do Bing para C#.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: v-gedod
ms.openlocfilehash: 99b7f05304b48b7d885a80705d05fbe24854150f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080646"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Enviar um pedido de pesquisa com o SDK de pesquisa de entidades do Bing paraC#

Utilize este guia de introdução para começar a pesquisa de entidades com o SDK de pesquisa de entidades do Bing para C#. Embora a pesquisa de entidades do Bing tenha uma API de REST compatível com a maioria das linguagens de programação, o SDK fornece uma forma fácil de integrar o serviço aos seus aplicativos. O código-fonte para este exemplo pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Pré-requisitos

* Qualquer edição do [Visual Studio 2017](https://www.visualstudio.com/downloads/).
* O framework [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote NuGet.
* Se estiver a utilizar o Linux/MacOS, esta aplicação pode ser executada com o [Mono](https://www.mono-project.com/).
* O [pacote NuGet do SDK de pesquisa do Bing notícias](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Também instalar este pacote instala o seguinte:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Para adicionar o SDK de pesquisa de entidades do Bing ao seu projeto do Visual Studio, utilize o `Manage NuGet Packages` opção no Explorador de soluções e adicione o `Microsoft.Azure.CognitiveServices.Search.EntitySearch` pacote.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Criar e inicializar uma aplicação

1. criar um novo C# consola de solução no Visual Studio. Em seguida, adicione o seguinte para o ficheiro de código principal.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Criar um cliente e enviar um pedido de pesquisa

1. Crie um novo cliente de pesquisa. Adicionar a chave de subscrição ao criar um novo `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Utilizar o cliente `Entities.Search()` função para procurar a sua consulta:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Obter e imprimir uma descrição da entidade

1. Se a API devolver resultados da pesquisa, obter a entidade principal do `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Imprimir a descrição da entidade principal 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma aplicação Web de página única](../tutorial-bing-entities-search-single-page-app.md)

* [O que é a API de pesquisa de entidades do Bing?](../overview.md )