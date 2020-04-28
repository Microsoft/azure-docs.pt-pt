---
title: Como gerir escritos simultâneos aos recursos
titleSuffix: Azure Cognitive Search
description: Utilize uma conmoeda otimista para evitar colisões no ar em atualizações ou eliminações para índices de pesquisa cognitiva azure, indexadores, fontes de dados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72792222"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Como gerir a conmoeda na Pesquisa Cognitiva Azure

Ao gerir recursos de Pesquisa Cognitiva Azure, como índices e fontes de dados, é importante atualizar os recursos de forma segura, especialmente se os recursos forem acedidos simultaneamente por diferentes componentes da sua aplicação. Quando dois clientes atualizam simultaneamente um recurso sem coordenação, as condições de corrida são possíveis. Para evitar isto, a Azure Cognitive Search oferece um *modelo de conmoeda otimista.* Não há fechaduras num recurso. Em vez disso, existe um ETag para cada recurso que identifica a versão de recurso para que possa elaborar pedidos que evitem sobreposições acidentais.

> [!Tip]
> O código conceptual numa [solução C# de amostra](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica como funciona o controlo de moedas na Pesquisa Cognitiva Azure. O código cria condições que invocam o controlo da moeda. Ler o fragmento de [código abaixo](#samplecode) é provavelmente suficiente para a maioria dos desenvolvedores, mas se quiser executá-lo, edite as definições de appsettings.json para adicionar o nome do serviço e uma chave de api-key de administração. Dado um `http://myservice.search.windows.net`URL de serviço `myservice`de , o nome de serviço é .

## <a name="how-it-works"></a>Como funciona

A conmoeda otimista é implementada através de verificações de condições de acesso em chamadas API escritas para índices, indexadores, fontes de dados e recursos sinonymMap.

Todos os recursos têm uma [*etiqueta de entidade (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações sobre a versão do objeto. Ao verificar primeiro o ETag, pode evitar atualizações simultâneas num fluxo de trabalho típico (obter, modificar localmente, atualizar) garantindo que o ETag do recurso corresponde à sua cópia local.

+ A API REST usa um [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho de pedido.
+ O .NET SDK define o ETag através de um objeto accessCondition, definindo o [If-Match [ Cabeçalho if-Match-None](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Qualquer objeto herdado do [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) tem um objeto accessCondition.

Sempre que atualiza um recurso, o seu ETag muda automaticamente. Quando implementa a gestão da moeda, tudo o que está a fazer é colocar uma condição prévia no pedido de atualização que requer que o recurso remoto tenha o mesmo ETag que a cópia do recurso que modificou no cliente. Se um processo simultâneo já tiver alterado o recurso remoto, o ETag não corresponderá à condição prévia e o pedido falhará com http 412. Se estiver a utilizar o .NET SDK, `CloudException` isto `IsAccessConditionFailed()` manifesta-se como um local onde o método de extensão retorna verdadeiro.

> [!Note]
> Só há um mecanismo de conmoedação. É sempre usado independentemente do qual a API é usada para atualizações de recursos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Utilizar casos e código de amostra

O seguinte código demonstra controlos de acessoCondition para operações de atualização chave:

+ Falhar uma atualização se o recurso já não existir
+ Falhar uma atualização se a versão de recurso mudar

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>Código de amostra do [programa DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
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

Um padrão de conceção para implementar uma moeda otimista deve incluir um loop que retente a verificação da condição de acesso, um teste para a condição de acesso, e obter opcionalmente um recurso atualizado antes de tentar reaplicar as alterações.

Este fragmento de código ilustra a adição de um sinonymMap a um índice que já existe. Este código é do [exemplo Synonym C# para a Pesquisa Cognitiva Azure](search-synonyms-tutorial-sdk.md).

O snippet obtém o índice de "hotéis", verifica a versão do objeto numa operação de atualização, lança uma exceção se a condição falhar, e depois tenta novamente a operação (até três vezes), começando com a recuperação de índices do servidor para obter a versão mais recente.

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


## <a name="next-steps"></a>Passos seguintes

Reveja a [amostra de sinónimos C#](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) para obter mais contexto sobre como atualizar com segurança um índice existente.

Tente modificar qualquer uma das seguintes amostras para incluir eTags ou objetos AccessCondition.

+ [Amostra de API REST no GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [amostra de SDK .NET no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Esta solução inclui o projeto "DotNetEtagsExplainer" contendo o código apresentado neste artigo.

## <a name="see-also"></a>Consulte também

[Cabeçalhos comuns](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
de pedido e resposta[HTTP códigos](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
de estado[(REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
