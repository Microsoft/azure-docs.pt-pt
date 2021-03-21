---
title: Exemplo de sinónimos em C#
titleSuffix: Azure Cognitive Search
description: Neste exemplo C#, aprenda a adicionar a característica de sinónimos a um índice em Azure Cognitive Search. Um mapa de sinónimos é uma lista de termos equivalentes. Os campos com suporte sinónimo expandem as consultas para incluir o termo fornecido pelo utilizador e todos os sinónimos relacionados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97704661"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Exemplo: Adicionar sinónimos para pesquisa cognitiva Azure em C #

Os sinónimos expandem uma consulta, ao efetuar a correspondência em termos considerados semanticamente equivalentes ao termo introduzido. Por exemplo, poderá pretender que a palavra "carro" corresponda nos documentos que contêm os termos "automóvel" ou "veículo". 

Na Pesquisa Cognitiva Azure, os sinónimos são definidos num mapa de sinónimos, através de *regras de mapeamento* que *associam* termos equivalentes. Este exemplo abrange os passos essenciais para a adição e utilização de sinónimos com um índice existente.

Neste exemplo, aprenderá a:

> [!div class="checklist"]
> * Crie um mapa de sinónimos utilizando a [classe SynonymMap](/dotnet/api/azure.search.documents.indexes.models.synonymmap). 
> * Desconte a [propriedade SynonymMapsName](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) em campos que devem suportar a expansão de consultas através de sinónimos.

Pode consultar um campo com o objetivo de sinónimo, como normalmente faria. Não existe sintaxe de consulta adicional necessária para aceder a sinónimos.

Pode criar vários mapas de sinónimos, publicá-los como um recurso amplo de serviços disponível para qualquer índice e, em seguida, referenciar o que irá utilizar ao nível do campo. Na hora da consulta, além de pesquisar um índice, a Azure Cognitive Search faz uma pesquisa num mapa de sinónimo, se for especificado nos campos utilizados na consulta.

> [!NOTE]
> Os sinónimos podem ser criados programáticamente, mas não no portal.

## <a name="prerequisites"></a>Pré-requisitos

Os requisitos de tutorial incluem o seguinte:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Serviço de Pesquisa Cognitiva Azure](search-create-service-portal.md)
* [Azure.Search.Docpacote de uments](https://www.nuget.org/packages/Azure.Search.Documents/)

Se não estiver familiarizado com a biblioteca de clientes .NET, consulte [Como utilizar a Azure Cognitive Search em .NET](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Código de exemplo

Pode encontrar o código fonte completo da aplicação da amostra utilizada neste exemplo no [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="overview"></a>Descrição geral

Consultas antes e depois são usadas para demonstrar o valor dos sinónimos. Neste exemplo, uma aplicação de amostra executa consultas e devolve resultados num índice de amostra "hotéis" preenchido com dois documentos. Em primeiro lugar, a aplicação executa consultas de pesquisa usando termos e frases que não aparecem no índice. Em segundo lugar, o código permite a funcionalidade de sinónimos e, em seguida, reemite as mesmas consultas, desta vez devolvendo resultados com base em fósforos no mapa do sinónimo. 

O código abaixo demonstra o fluxo geral.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>Consultas "antes"

No `RunQueriesWithNonExistentTermsInIndex`, pode emitir consultas de pesquisa com "cinco estrelas", "internet" e "economia E hotel".

As consultas de frase, como "cinco estrelas", devem ser incluídas em aspas, e também podem precisar de caracteres de fuga dependendo do seu cliente.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

Nenhum dos dois documentos indexados contém os termos, pelo que obtemos a seguinte saída a partir da `RunQueriesWithNonExistentTermsInIndex` primeira:  **nenhum documento combinado**.

## <a name="enable-synonyms"></a>Ativar sinónimos

Após a execução das consultas "antes", o código de amostra permite sinónimos. A ativação dos sinónimos é um processo de dois passos. Primeiro, defina e carrele as regras do sinónimo. Segundo, configurar campos para usá-los. O processo está destacado em `UploadSynonyms` e em `EnableSynonymsInHotelsIndex`.

1. Adicione um mapa de sinónimos ao seu serviço de pesquisa. No `UploadSynonyms`, definimos quatro regras no nosso mapa de sinónimos "desc-synonymmap" e carregamos para o serviço.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Configure os campos pesquisáveis para utilizar o mapa de sinónimos na definição do índice. No `AddSynonymMapsToFields`, ativamos sinónimos em dois campos `category` e `tags`, ao definir a propriedade `SynonymMapNames` para o nome do mapa de sinónimos carregado recentemente.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Ao adicionar um mapa de sinónimos, as reconstruções do índice não são necessárias. Pode adicionar um mapa de sinónimos ao seu serviço e, em seguida, corrigir definições de campo existente em qualquer índice para utilizar o novo mapa de sinónimos. A adição de novos atributos não tem qualquer impacto na disponibilidade do índice. O mesmo aplica-se na desativação de sinónimos num campo. Pode definir simplesmente a propriedade `SynonymMapNames` numa lista vazia.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>Consultas de "após"

Depois de o mapa de sinónimos ser carregado e o índice ser atualizado para utilizar o mapa de sinónimos, a segunda chamada `RunQueriesWithNonExistentTermsInIndex` produz o seguinte:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

A primeira consulta encontra o documento a partir da regra `five star=>luxury`. A segunda consulta expande a pesquisa com `internet,wifi` e a terceira com `hotel, motel` e `economy,inexpensive=>budget` ao encontrar os documentos que corresponderam.

Adicionar sinónimos altera completamente a experiência de pesquisa. Neste exemplo, as consultas originais não devolveram resultados significativos, embora os documentos do nosso índice fossem relevantes. Ao ativar os sinónimos, podemos expandir um índice para incluir termos na utilização comum, sem alterações aos dados subjacentes no índice.

## <a name="clean-up-resources"></a>Limpar os recursos

A forma mais rápida de limpar depois de um exemplo é eliminando o grupo de recursos que contém o serviço de Pesquisa Cognitiva Azure. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página geral do serviço de Pesquisa Cognitiva Azure.

## <a name="next-steps"></a>Passos seguintes

Este exemplo demonstrou a funcionalidade de sinónimos no código C# para criar e publicar regras de mapeamento e, em seguida, chamar o mapa de sinónimo numa consulta. Encontram-se mais informações na documentação de referência do [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) e [API REST](/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Como usar sinónimos em Pesquisa Cognitiva Azure](search-synonyms.md)