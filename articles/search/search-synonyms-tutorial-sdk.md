---
title: Sinónimos C# exemplo - Azure Search
description: Neste C# exemplo, saiba como adicionar a funcionalidade de sinónimos para um índice da Azure Search. Um mapa de sinónimos é uma lista de termos equivalentes. Campos com suporte para sinónimos expanda queries para incluir o termo fornecidos pelo usuário e todos os relacionados sinónimos.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: cb0d04e46834530c8fea29ef79a2156caabeeeff
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202372"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Exemplo: Adicionar sinónimos para o Azure Search noC#

Os sinónimos expandem uma consulta, ao efetuar a correspondência em termos considerados semanticamente equivalentes ao termo introduzido. Por exemplo, poderá pretender que a palavra "carro" corresponda nos documentos que contêm os termos "automóvel" ou "veículo". 

No Azure Search, os sinónimos são definidos num *mapa de sinónimos*, através de *regras de mapeamento* que associam termos equivalentes. Este exemplo aborda as etapas essenciais para adicionar e utilizar sinónimos com um índice existente. Saiba como:

> [!div class="checklist"]
> * Ativar os sinónimos ao criar e publicar regras de mapeamento 
> * Referenciar um mapa de sinónimos numa cadeia de consulta

Pode criar vários mapas de sinónimos, publicá-los como um recurso amplo de serviços disponível para qualquer índice e, em seguida, referenciar o que irá utilizar ao nível do campo. No momento da consulta, para além de procurar um índice, o Azure Search faz uma pesquisa num mapa de sinónimos, se um for especificado nos campos utilizados na consulta.

> [!NOTE]
> Os sinónimos são suportados nas versões de API e de SDK mais recentes (api-version=2017-11-11, versão 5.0.0 do SDK). Não existe suporte do portal do Azure neste momento. Se o suporte para sinónimos do portal do Azure for útil para si, forneça o seu feedback no [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos de tutorial incluem o seguinte:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Serviço do Azure Search](search-create-service-portal.md)
* [Biblioteca Microsoft.Azure.Search.NET](https://aka.ms/search-sdk)
* [Como utilizar o Azure Search a partir de uma Aplicação .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Descrição geral

As consultas de antes e após demonstram o valor dos sinónimos. Neste exemplo, use um aplicativo de exemplo que executa consultas e devolve resultados num índice de exemplo. A aplicação de exemplo cria um índice pequeno com o nome "hotéis" preenchido com dois documentos. A aplicação executa consultas de pesquisa com termos e expressões que não são apresentados no índice, ativa a funcionalidade de sinónimos e, em seguida, inicia as mesmas pesquisas novamente. O código abaixo demonstra o fluxo geral.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Os passos para criar e preencher o índice de exemplo serão explicados em [How to use Azure Search from a .NET Application (Como utilizar o Azure Search a partir de uma Aplicação .NET)](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Consultas "antes"

No `RunQueriesWithNonExistentTermsInIndex`, pode emitir consultas de pesquisa com "cinco estrelas", "internet" e "economia E hotel".
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Nenhum dos dois documentos indexados contém os termos, pelo que vamos obter o seguinte resultado do primeiro `RunQueriesWithNonExistentTermsInIndex`.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Ativar sinónimos

A ativação dos sinónimos é um processo de dois passos. Em primeiro lugar, definimos e carregamos as regras de sinónimos e, em seguida, configuramos os campos para os utilizar. O processo está destacado em `UploadSynonyms` e em `EnableSynonymsInHotelsIndex`.

1. Adicione um mapa de sinónimos ao seu serviço de pesquisa. No `UploadSynonyms`, definimos quatro regras no nosso mapa de sinónimos "desc-synonymmap" e carregamos para o serviço.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Um mapa de sinónimos tem de estar em conformidade para abrir o formato `solr` standard de origem. O formato é explicado em [Synonyms in Azure Search (Sinónimos no Azure Search)](search-synonyms.md) na secção `Apache Solr synonym format`.

2. Configure os campos pesquisáveis para utilizar o mapa de sinónimos na definição do índice. No `EnableSynonymsInHotelsIndex`, ativamos sinónimos em dois campos `category` e `tags`, ao definir a propriedade `synonymMaps` para o nome do mapa de sinónimos carregado recentemente.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Ao adicionar um mapa de sinónimos, as reconstruções do índice não são necessárias. Pode adicionar um mapa de sinónimos ao seu serviço e, em seguida, corrigir definições de campo existente em qualquer índice para utilizar o novo mapa de sinónimos. A adição de novos atributos não tem qualquer impacto na disponibilidade do índice. O mesmo aplica-se na desativação de sinónimos num campo. Pode definir simplesmente a propriedade `synonymMaps` numa lista vazia.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Consultas de "após"

Depois de o mapa de sinónimos ser carregado e o índice ser atualizado para utilizar o mapa de sinónimos, a segunda chamada `RunQueriesWithNonExistentTermsInIndex` produz o seguinte:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
A primeira consulta encontra o documento a partir da regra `five star=>luxury`. A segunda consulta expande a pesquisa com `internet,wifi` e a terceira com `hotel, motel` e `economy,inexpensive=>budget` ao encontrar os documentos que corresponderam.

Adicionar sinónimos altera completamente a experiência de pesquisa. Neste exemplo, as consultas originais não conseguiram devolver resultados com significado, apesar dos documentos no nosso índice serem relevantes. Ao ativar os sinónimos, podemos expandir um índice para incluir termos na utilização comum, sem alterações aos dados subjacentes no índice.

## <a name="sample-application-source-code"></a>Código de origem da aplicação de exemplo
Pode encontrar o código de origem completo da aplicação de exemplo utilizado nesta apresentação no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida a limpeza depois de um exemplo é eliminando o grupo de recursos que contém o serviço Azure Search. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página Descrição Geral do serviço Azure Search.

## <a name="next-steps"></a>Passos Seguintes

Este exemplo demonstrou a [API do REST de sinónimos](https://aka.ms/rgm6rq) no C# código para criar e publicar regras de mapeamento e, em seguida, chamar o mapa de sinónimos numa consulta. Encontram-se mais informações na documentação de referência do [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) e [API REST](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Como utilizar sinónimos no Azure Search](search-synonyms.md)
