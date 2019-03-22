---
title: Como gerir escritas simultâneas para recursos - Azure Search
description: Utilize a simultaneidade otimista para evitar colisões médio ar atualizações ou exclusões para índices da Azure Search, indexadores e origens de dados.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2c317bbdef2511728d23b33d8eef1c4a41a87d97
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201522"
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Como gerir a simultaneidade no Azure Search

Gestão de recursos de pesquisa do Azure, como índices e fontes de dados, é importante atualizar recursos com segurança, especialmente se os recursos são acessados em simultâneo por diferentes componentes da sua aplicação. Quando dois clientes em simultâneo de atualização de um recurso sem coordenação, condições de corrida são possíveis. Para evitar esta situação, o Azure Search oferece um *modelo de concorrência otimista*. Não existem não existem bloqueios num recurso. Em vez disso, há uma ETag para substitui todos os recursos que identifica a versão de recursos para que pode criar pedidos que evitar acidental.

> [!Tip]
> Código conceitual num [exemplo C# solução](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica como funciona o controlo de simultaneidade no Azure Search. O código cria condições que invocar o controle de simultaneidade. Ler o [fragmento de código abaixo](#samplecode) é provavelmente suficiente para a maioria dos desenvolvedores, mas se desejar executá-lo, edite appSettings para adicionar o nome do serviço e uma chave de api de administrador. Tendo em conta um URL do serviço de `http://myservice.search.windows.net`, o nome do serviço é `myservice`.

## <a name="how-it-works"></a>Como funciona

É implementada a simultaneidade otimista através do acesso a condição verifica em chamadas de API, escrever a índices, indexadores, origens de dados e recursos de synonymMap.

Todos os recursos tenham uma [ *etiqueta de entidade (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) que fornece informações de versão do objeto. Ao selecionar a ETag em primeiro lugar, pode evitar atualizações em simultâneo num fluxo de trabalho normal (obter, modificar localmente, atualizar), garantindo sua cópia local de correspondências de ETag do recurso.

+ A API de REST utiliza um [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no cabeçalho do pedido.
+ O SDK de .NET define a ETag por meio de um objeto de accessCondition, definindo o [If-Match | O cabeçalho If-Match-nenhuma](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) no recurso. Qualquer objeto herdar a partir [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) tem um objeto de accessCondition.

Sempre que atualizar um recurso, a ETag é alterado automaticamente. Quando implementa o gerenciamento de simultaneidade, que está a fazer é colocar uma condição prévia no pedido de atualização que requer o recurso remoto para ter a mesma ETag como a cópia do recurso que modificou no cliente. Se um processo em simultâneo foi alterado o recurso remoto já, a ETag não corresponderá a pré-condição e o pedido irá falhar com HTTP 412. Se estiver a utilizar o SDK de .NET, isso se manifesta como um `CloudException` onde o `IsAccessConditionFailed()` método de extensão retorna true.

> [!Note]
> Existe apenas um mecanismo para a simultaneidade. É sempre usado, independentemente do que a API é atualizada para atualizações de recursos.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Código de exemplo e casos de utilização

O código a seguir demonstra accessCondition verifica a existência de operações de atualização da chave:

+ Efetuar a ativação de uma atualização se o recurso já não existe
+ Efetuar a ativação de uma atualização se a versão do recurso é alterada

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Exemplo de código a partir do [DotNetETagsExplainer programa](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

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

## <a name="design-pattern"></a>padrão de design

Um padrão de design para implementar a simultaneidade deve incluir um loop que repete a condição de acesso otimista verificar, um teste para a condição de acesso e, opcionalmente, obtém um recurso atualizado antes de tentar voltar a aplicar as alterações.

Este fragmento de código ilustra a adição de um synonymMap para um índice que já existe. Esse código é a partir da [sinónimos (pré-visualização) C# exemplo para o Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-example-sdk).

O trecho de código obtém o índice "Hotéis", verifica a versão de objeto numa operação de atualização, lança uma exceção se a condição falhar e, em seguida, repetem a operação (até três vezes), começando com a obtenção de índice do servidor para obter a versão mais recente.

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

Reveja os [sinónimos C# exemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) para obter mais contexto sobre como atualizar com segurança um índice existente.

Tente modificar qualquer um dos exemplos a seguir para incluir objetos ETags ou AccessCondition.

+ [Exemplo de REST API no GitHub](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [Exemplo de SDK do .NET no GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). Esta solução inclui o projeto de "DotNetEtagsExplainer" que contém o código apresentado neste artigo.

## <a name="see-also"></a>Consulte também

[Cabeçalhos comuns de solicitação e resposta HTTP](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[códigos de estado HTTP](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[(REST API) de operações de índice](https://docs.microsoft.com/rest/api/searchservice/index-operations)
