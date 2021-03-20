---
title: Como gerir escritos simultâneos para recursos
titleSuffix: Azure Cognitive Search
description: Use concuncy otimista para evitar colisões aéreas em atualizações ou eliminações para índices de Pesquisa Cognitiva Azure, indexadores, fontes de dados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 52260facc1a458a3ef18b067e6d52e189f5d329c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881759"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Como gerir a concordância na Pesquisa Cognitiva Azure

Ao gerir recursos de Pesquisa Cognitiva Azure, como índices e fontes de dados, é importante atualizar os recursos de forma segura, especialmente se os recursos forem acedidos simultaneamente por diferentes componentes da sua aplicação. Quando dois clientes atualizam simultaneamente um recurso sem coordenação, as condições de corrida são possíveis. Para evitar isto, a Azure Cognitive Search oferece um *modelo de concordância otimista.* Não há fechaduras num recurso. Em vez disso, existe um ETag para cada recurso que identifica a versão de recursos para que possa formular pedidos que evitem substituições acidentais.

> [!Tip]
> O código conceptual numa [solução C# explica](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) como funciona o controlo da conuncy na Pesquisa Cognitiva Azure. O código cria condições que invocam o controlo da concordância. Ler o fragmento de [código abaixo](#samplecode) pode ser suficiente para a maioria dos desenvolvedores, mas se quiser executá-lo, edite appsettings.jspara adicionar o nome de serviço e uma chave api de administração. Dado um URL de serviço `http://myservice.search.windows.net` de, o nome de serviço é `myservice` .

## <a name="how-it-works"></a>Como funciona

A concordância otimista é implementada através de controlos de condições de acesso em chamadas API escritas para índices, indexadores, fontes de dados, skillsets e recursos synonymMap.

Todos os recursos têm uma [*etiqueta de entidade (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações sobre a versão do objeto. Ao verificar primeiro o ETag, pode evitar atualizações simultâneas num fluxo de trabalho típico (obter, modificar localmente, atualizar) garantindo que o ETag do recurso corresponde à sua cópia local.

+ A API REST utiliza um [ETag](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho de pedido.

+ O .NET SDK define o ETag através de um objeto de acessoCondição, definindo o [if-match | Se-Match-Nenhum cabeçalho](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Os objetos que utilizam ETags, tais como [SynonymMap.ETag](/dotnet/api/azure.search.documents.indexes.models.synonymmap.etag) e [SearchIndex.ETag,](/dotnet/api/azure.search.documents.indexes.models.searchindex.etag)têm um objeto de acessoCondição.

Sempre que atualiza um recurso, o seu ETag muda automaticamente. Quando implementa uma gestão de conuncy, tudo o que está a fazer é colocar uma condição prévia no pedido de atualização que requer que o recurso remoto tenha o mesmo ETag que a cópia do recurso que modificou no cliente. Se um processo simultâneo já tiver alterado o recurso remoto, o ETag não corresponderá à condição prévia e o pedido falhará com HTTP 412. Se estiver a utilizar o .NET SDK, isto manifesta-se como um `CloudException` local onde o método de `IsAccessConditionFailed()` extensão retorna verdadeiramente.

> [!Note]
> Só há um mecanismo de concordância. É sempre usado independentemente do que a API é usada para atualizações de recursos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Utilizar casos e código de amostra

O seguinte código demonstra o acesso Verificações de condições para operações de atualização chave:

+ Falhe uma atualização se o recurso já não existir
+ Falhe uma atualização se a versão do recurso mudar

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Código de amostra do [programa DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```csharp
class Program
{
    // This sample shows how ETags work by performing conditional updates and deletes
    // on an Azure Cognitive Search index.
    static void Main(string[] args)
    {
        IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
        IConfigurationRoot configuration = builder.Build();

        SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

        Console.WriteLine("Deleting index...\n");
        DeleteTestIndexIfExists(serviceClient);

        // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
        // of the resource you're working on. When you first create a resource such as an index, its ETag is
        // empty.
        Index index = DefineTestIndex();
        Console.WriteLine(
            $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

        // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
        // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
        // blank ETag.
        Console.WriteLine("Creating index...\n");
        index = serviceClient.Indexes.Create(index);
        
        Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

        // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
        // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
        // succeed if the index already exists.
        index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
        index =
            serviceClient.Indexes.CreateOrUpdate(
                index,
                accessCondition: AccessCondition.GenerateIfExistsCondition());

        Console.WriteLine(
            $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

        // More importantly, ETags protect you from concurrent updates to the same resource. If another
        // client tries to update the resource, it will fail as long as all clients are using the right
        // access conditions.
        Index indexForClient1 = index;
        Index indexForClient2 = serviceClient.Indexes.Get("test");

        Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
        Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

        // Client 1 successfully updates the index.
        indexForClient1.Fields.Add(new Field("a", DataType.Int32));
        indexForClient1 =
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient1,
                accessCondition: AccessCondition.IfNotChanged(indexForClient1));

        Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

        // Client 2 tries to update the index, but fails, thanks to the ETag check.
        try
        {
            indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient2,
                accessCondition: AccessCondition.IfNotChanged(indexForClient2));

            Console.WriteLine("Whoops; This shouldn't happen");
            Environment.Exit(1);
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine("Client 2 failed to update the index, as expected.");
        }

        // You can also use access conditions with Delete operations. For example, you can implement an
        // atomic version of the DeleteTestIndexIfExists method from this sample like this:
        Console.WriteLine("Deleting index...\n");
        serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

        // This is slightly better than using the Exists method since it makes only one round trip to
        // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
        // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
        // Azure Cognitive Search is idempotent.

        // And we're done! Bye!
        Console.WriteLine("Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

    private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
    {
        string searchServiceName = configuration["SearchServiceName"];
        string adminApiKey = configuration["SearchServiceAdminApiKey"];

        SearchServiceClient serviceClient =
            new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
        return serviceClient;
    }

    private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("test"))
        {
            serviceClient.Indexes.Delete("test");
        }
    }

    private static Index DefineTestIndex() =>
        new Index()
        {
            Name = "test",
            Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
        };
    }
}
```

## <a name="design-pattern"></a>Padrão de design

Um padrão de design para implementar a concordância otimista deve incluir um loop que retrite a verificação da condição de acesso, um teste para a condição de acesso, e obter opcionalmente um recurso atualizado antes de tentar reapriscê-la.

Este código de corte ilustra a adição de um sinónimoMap a um índice que já existe. Este código é do [exemplo Synonym C# para Azure Cognitive Search](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/v10/DotNetHowToSynonyms).

O snippet obtém o índice de "hotéis", verifica a versão do objeto numa operação de atualização, lança uma exceção se a condição falhar e, em seguida, retrimba a operação (até três vezes), começando com a recuperação de índices do servidor para obter a versão mais recente.

```csharp
private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
{
    int MaxNumTries = 3;

    for (int i = 0; i < MaxNumTries; ++i)
    {
        try
        {
            Index index = serviceClient.Indexes.Get("hotels");
            index = AddSynonymMapsToFields(index);

            // The IfNotChanged condition ensures that the index is updated only if the ETags match.
            serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

            Console.WriteLine("Updated the index successfully.\n");
            break;
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
        }
    }
}

private static Index AddSynonymMapsToFields(Index index)
{
    index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
    index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
    return index;
}
```

## <a name="next-steps"></a>Passos seguintes

Tente modificar outras amostras para exercitar ETags ou objetos AccessCondition.

+ [pesquisa-dotnet-start-start-start-ins no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Este repositório inclui o projeto "DotNetEtagsExplainer".

+ [amostras azure-search-dotnet no GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples) contém amostras C# adicionais.

## <a name="see-also"></a>Ver também

+ [Comuns de pedidos http e cabeçalhos de resposta](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
+ [Códigos de estado HTTP](/rest/api/searchservice/http-status-codes)
+ [Operações de índice (REST API)](/rest/api/searchservice/index-operations)