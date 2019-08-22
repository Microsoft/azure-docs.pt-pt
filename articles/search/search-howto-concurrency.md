---
title: Como gerenciar gravações simultâneas para recursos-Azure Search
description: Use a simultaneidade otimista para evitar colisões de ar médio em atualizações ou exclusões para Azure Search índices, indexadores, fontes de dados.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 67f2dad016d3958dc10ba87e785d31694a1c94f5
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656726"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Como gerenciar a simultaneidade no Azure Search

Ao gerenciar Azure Search recursos como índices e fontes de dados, é importante atualizar os recursos com segurança, especialmente se os recursos forem acessados simultaneamente por diferentes componentes do seu aplicativo. Quando dois clientes atualizam simultaneamente um recurso sem coordenação, as condições de corrida são possíveis. Para evitar isso, Azure Search oferece um *modelo de simultaneidade otimista*. Não há bloqueios em um recurso. Em vez disso, há uma ETag para cada recurso que identifica a versão do recurso para que você possa criar solicitações que evitem substituições acidentais.

> [!Tip]
> O código conceitual em uma [solução de exemplo C# ](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica como o controle de simultaneidade funciona em Azure Search. O código cria condições que invocam o controle de simultaneidade. Ler o [fragmento de código abaixo](#samplecode) provavelmente é suficiente para a maioria dos desenvolvedores, mas se você quiser executá-lo, edite appSettings. JSON para adicionar o nome do serviço e uma chave de API de administração. Dada uma URL de serviço `http://myservice.search.windows.net`de, o nome do `myservice`serviço é.

## <a name="how-it-works"></a>Como funciona

A simultaneidade otimista é implementada por meio de verificações de condição de acesso em chamadas à API gravando em índices, indexadores, fontes de fonte e recursos de synonymMap.

Todos os recursos têm uma [*ETag (marca de entidade)* ](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações de versão do objeto. Ao verificar a ETag primeiro, você pode evitar atualizações simultâneas em um fluxo de trabalho típico (Get, modificar localmente, atualizar) garantindo que a ETag do recurso corresponda à sua cópia local.

+ A API REST usa uma [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho da solicitação.
+ O SDK do .NET define a ETag por meio de um objeto accessCondition, definindo o [If-Match | Cabeçalho If-Match-None](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Qualquer objeto herdado de [IResourceWithETag (SDK do .net)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) tem um objeto accessCondition.

Toda vez que você atualiza um recurso, sua ETag é alterada automaticamente. Quando você implementa o gerenciamento de simultaneidade, tudo o que está fazendo é colocar uma pré-condição na solicitação de atualização que exige que o recurso remoto tenha a mesma ETag que a cópia do recurso que você modificou no cliente. Se um processo simultâneo já tiver alterado o recurso remoto, a ETag não corresponderá à pré-condição e a solicitação falhará com o HTTP 412. Se você estiver usando o SDK do .net, isso se manifesta como `CloudException` um onde `IsAccessConditionFailed()` o método de extensão retorna true.

> [!Note]
> Há apenas um mecanismo de simultaneidade. Ele é sempre usado, independentemente de qual API é usada para atualizações de recursos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Casos de uso e código de exemplo

O código a seguir demonstra as verificações de accessCondition para as principais operações de atualização:

+ Falha em uma atualização se o recurso não existir mais
+ Falha em uma atualização se a versão do recurso for alterada

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Código de exemplo do [programa DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

Um padrão de design para implementar a simultaneidade otimista deve incluir um loop que repete a verificação da condição de acesso, um teste para a condição de acesso e, opcionalmente, recupere um recurso atualizado antes de tentar aplicar novamente as alterações.

Este trecho de código ilustra a adição de um synonymMap a um índice que já existe. Esse código é do [exemplo de C# sinônimo para Azure Search](search-synonyms-tutorial-sdk.md).

O trecho de código obtém o índice "Hotéis", verifica a versão do objeto em uma operação de atualização, gera uma exceção se a condição falhar e, em seguida, tenta novamente a operação (até três vezes), começando com a recuperação de índice do servidor para obter a versão mais recente.

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


## <a name="next-steps"></a>Passos Seguintes

Examine o [ C# exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) de sinônimos para obter mais contexto sobre como atualizar um índice existente com segurança.

Tente modificar qualquer um dos exemplos a seguir para incluir objetos ETags ou AccessCondition.

+ [Exemplo de API REST no GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Exemplo do SDK do .net no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Essa solução inclui o projeto "DotNetEtagsExplainer" que contém o código apresentado neste artigo.

## <a name="see-also"></a>Consulte também

[](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)Cabeçalhos
de solicitação HTTP comuns e de resposta[códigos](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
de status http[operações de índice (API REST)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
