---
title: Carregar dados para um índice da Azure Search no C# (.NET SDK) - Azure Search
description: Saiba como carregar dados para um índice de texto completo pesquisável no Azure Search utilizando C# código e o SDK de .NET de exemplo.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286912"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Início rápido: 2 - carregar dados para um índice de pesquisa do Azure com oC#

Este artigo mostra-lhe como importar dados para [um índice da Azure Search](search-what-is-an-index.md) com C# e o [SDK de .NET](https://aka.ms/search-sdk). Enviar documentos para o seu índice é conseguido através da execução dessas tarefas:

> [!div class="checklist"]
> * Criar uma [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objeto para ligar a um índice de pesquisa.
> * Criar uma [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objeto que contém os documentos a adicionar, modificar ou eliminar.
> * Chamar o `Documents.Index` método no `SearchIndexClient` carregar documentos para um índice.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um índice da Azure Search](search-create-index-dotnet.md) e uma `SearchServiceClient` objeto, conforme mostrado na ["Criar um cliente"](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Criar um cliente
Para importar dados, terá de uma instância do `SearchIndexClient` classe. Existem várias abordagens para criar essa classe, incluindo a utilização do `SearchServiceClient` instância que já foi criada. 

Como ilustra o exemplo seguinte, pode utilizar o `SearchServiceClient` instância e chamar seu `Indexes.GetClient` método. Este trecho de código obtém uma `SearchIndexClient` para o índice com o nome "Hotéis" de um `SearchServiceClient` com o nome `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` tem uma propriedade `Documents`. Esta propriedade fornece todos os métodos necessários para adicionar, modificar, eliminar ou consultar documentos de consulta no seu índice.

> [!NOTE]
> Num aplicativo típico de pesquisa, consulta e indexação são tratados em separado. Enquanto `Indexes.GetClient` é conveniente porque é possível reutilizar objetos, como `SearchCredentials`, uma abordagem mais robusta envolve a criação do `SearchIndexClient` diretamente para que pode passar uma chave de consulta em vez de uma chave de administração. Esta prática é consistente com o [princípio do menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) e ajuda a tornar a sua aplicação mais segura. Vai a construir um `SearchIndexClient` no exercício seguinte. Para obter mais informações sobre chaves, consulte [criar e gerir as chaves de api para um serviço Azure Search](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Construir IndexBatch

Para importar dados utilizando o SDK .NET, empacotar os dados para um `IndexBatch` objeto. Uma `IndexBatch` encapsula uma coleção de `IndexAction` objetos, cada um deles contendo um documento e uma propriedade que informa a Azure Search qual a ação a realizar nesse documento (carregar, intercalar, eliminar e mergeOrUpload). Para obter mais informações sobre as ações de indexação, consulte [ações de indexação: carregar, intercalar, mergeOrUpload, eliminar](search-what-is-data-import.md#indexing-actions).

Partindo do princípio de que sabe quais ações a efetuar nos seus documentos, está pronto para construir o `IndexBatch`. O exemplo abaixo mostra como criar um lote com algumas ações diferentes. O exemplo usa uma classe personalizada chamada `Hotel` que mapeia para um documento no índice "Hotéis".

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

Neste caso, estamos a utilizar `Upload`, `MergeOrUpload` e `Delete` como as nossas ações de pesquisa, tal como especificado pelos métodos chamados na classe `IndexAction`.

Partamos do princípio que este índice "hotéis" de exemplo já está preenchido com vários documentos. Observe como não tivemos de especificar todos os campos de documentos possíveis quando utilizamos `MergeOrUpload` e como especificamos apenas a chave do documento (`HotelId`) ao utilizar `Delete`.

Além disso, tenha em atenção que só pode incluir até 1000 documentos num único pedido de indexação.

> [!NOTE]
> Neste exemplo, estamos a aplicar diferentes ações para documentos diferentes. Se pretendia realizar as mesmas ações em todos os documentos no lote, em vez de chamar `IndexBatch.New`, pode utilizar os outros métodos estáticos de `IndexBatch`. Por exemplo, pode criar lotes chamando `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` ou `IndexBatch.Delete`. Estes métodos recolhem uma coleção de documentos (objetos do tipo `Hotel` neste exemplo), ao invés de objetos `IndexAction`.
> 
> 

## <a name="call-documentsindex"></a>Chamar Documents.Index
Agora que tem um objeto `IndexBatch` inicializado, pode enviá-lo para o índice ao chamar `Documents.Index` no seu objeto `SearchIndexClient` O exemplo seguinte mostra como chamar o `Index`, assim como alguns passos adicionais que terá de realizar:

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Tenha em atenção a `try`/`catch` adjacente à chamada para o método `Index`. O bloco catch processa um caso de erro importantes para indexação. Se o serviço Azure Search não consegue indexar alguns dos documentos no lote, uma `IndexBatchException` é emitida pelo `Documents.Index`. Isto pode acontecer se indexar documentos enquanto o seu serviço estiver sobrecarregado. **Recomendamos vivamente que processe explicitamente este caso no seu código.** Pode atrasar e, em seguida, repetir a indexação dos documentos que falharam, pode iniciar sessão e continuar como no exemplo ou pode fazer algo diferente dependendo dos requisitos de consistência de dados da aplicação.

Por fim, o código no exemplo acima tem um atraso de dois segundos. A indexação acontece de forma assíncrona no seu serviço da Azure Search, desta forma, a aplicação de exemplo tem de aguardar alguns momentos para se certificar de que os documentos estão disponíveis para pesquisa. Este género de atrasos são normalmente necessários apenas para demonstrações gratuitas, testes e aplicações de exemplo.

Para obter mais informações sobre o processamento de documentos, consulte ["Como o SDK .NET processa documentos"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Passos Seguintes
Depois de preencher o índice da Azure Search, a próxima etapa é emitir consultas para procurar documentos. 

> [!div class="nextstepaction"]
> [Consultar um índice da Azure Search noC#](search-query-dotnet.md)
