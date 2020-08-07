---
title: 'Quickstart: Criar um índice de pesquisa em .NET'
titleSuffix: Azure Cognitive Search
description: Neste início rápido C#, aprenda a criar um índice, a carregar dados e a executar consultas utilizando a biblioteca de clientes Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/05/2020
ms.openlocfilehash: b621b16d789e16a6f44536a6e8c18b5aa3690d74
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905453"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Quickstart: Criar um índice de pesquisa utilizando a biblioteca de clientes Azure.Search.Documents

Utilize a nova [biblioteca de clientesAzure.Search.Doc(versão 11)](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet) para criar uma aplicação de consola .NET Core em C# que cria, carrega e consulta um índice de pesquisa.

[Faça o download do código fonte](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart-v11) para começar com um projeto acabado ou siga os passos deste artigo para criar o seu próprio.

> [!NOTE]
> À procura de uma versão anterior? Consulte [Criar um índice de pesquisa utilizando microsoft.Azure.Search v10](search-get-started-dotnet-v10.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, tenha as seguintes ferramentas e serviços:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) Pode utilizar um serviço gratuito para este arranque rápido. 

+ [Estúdio Visual,](https://visualstudio.microsoft.com/downloads/)qualquer edição. O código de amostra foi testado na edição comunitária gratuita do Visual Studio 2019.

<a name="get-service-info"></a>

## <a name="get-a-key-and-endpoint"></a>Obtenha uma chave e ponto final

As chamadas para o serviço requerem um ponto final URL e uma chave de acesso em cada pedido. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

2. Em **Settings**  >  **Definições Teclas**, obtenha uma chave de administração para todos os direitos sobre o serviço, necessários para criar ou eliminar objetos. Há duas chaves primárias e secundárias intercambiáveis. Pode usar qualquer um.

   ![Obtenha uma chave de acesso http e acesso](media/search-get-started-postman/get-url-key.png "Obtenha uma chave de acesso http e acesso")

Todos os pedidos requerem uma chave API em cada pedido enviado ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Inicie o Visual Studio e crie um novo projeto de App de Consola que possa funcionar em .NET Core. 

### <a name="install-the-nuget-package"></a>Instale o pacote NuGet

Após a criação do projeto, adicione a biblioteca do cliente. O [pacoteAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) é composto por uma biblioteca de clientes que fornece todas as APIs utilizadas para trabalhar com um serviço de pesquisa em .NET.

1. In **Tools**  >  **NuGet Package Manager**, selecione Gerir **pacotes nuget para solução...**. 

1. Clique em **Browse** (Procurar).

1. Procure `Azure.Search.Documents` e selecione a versão 11.0.0.

1. Clique em **Instalar** no direito de adicionar o conjunto ao seu projeto e solução.

### <a name="create-a-search-client"></a>Criar um cliente de pesquisa

1. Em **Program.cs,** altere o espaço de nome para `AzureSearch.SDK.Quickstart.v11` e, em seguida, adicione as `using` seguintes diretivas.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Criar dois clientes: [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) cria o índice e [o SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) funciona com um índice existente. Ambos precisam do ponto final de serviço e de uma chave API de administração para autenticação com direitos de criação/eliminação.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient qryclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - Criar um índice

Este quickstart constrói um índice de Hotéis que você carregará com dados do hotel e executar consultas. Neste passo, definir os campos no índice. Cada definição de campo inclui um nome, tipo de dados e atributos que determinam como o campo é usado.

Neste exemplo, os métodos sincronizados da biblioteca Azure.Search.Documents são usados para a simplicidade e legibilidade. No entanto, para cenários de produção, deve utilizar métodos assíncronos para manter a sua aplicação escalável e responsiva. Por exemplo, utilizaria [o CreateIndexAsync](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) em vez de [CreateIndex.](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)

1. Adicione uma definição de classe vazia ao seu projeto: **Hotel.cs**

1. Em **Hotel.cs,** defina a estrutura de um documento hoteleiro.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. Em **Program.cs,** especifique os campos e atributos. [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) e [CreateIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) são usados para criar um índice.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Os atributos no campo determinam como é usado numa aplicação. Por exemplo, o `IsFilterable` atributo deve ser atribuído a todos os campos que suportem uma expressão de filtro.

Em contraste com as versões anteriores do .NET SDK que requerem [issarchable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable) em campos de cordas pesquisáveis, pode utilizar [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield) e [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield) para simplificar definições de campo.

À semelhança das versões anteriores, outros atributos ainda são necessários na própria definição. Por exemplo, [IsFilterable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)e [IsFacetable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) devem ser explicitamente atribuídos, como mostrado na amostra acima. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documentos de carga

A azure Cognitive Search procura sobre o conteúdo armazenado no serviço. Neste passo, você carregará documentos JSON que estejam em conformidade com o índice hotel que acabou de criar.

Na Pesquisa Cognitiva Azure, os documentos são estruturas de dados que são entradas para indexação e saídas de consultas. Tal como obtido a partir de uma fonte de dados externa, as entradas de documentos podem ser linhas numa base de dados, bolhas no armazenamento blob ou documentos JSON no disco. Neste exemplo, estamos pegando um atalho e incorporando documentos JSON para cinco hotéis no próprio código. 

Ao carregar documentos, deve utilizar um objeto [IndexDocumentsBatch.](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) Um IndexDocumentsBatch contém uma coleção de [Ações,](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)cada uma das quais contém um documento e uma propriedade que diz à Azure Cognitive Search que ação a executar ([upload, merge, delete e mergeOrUpload).](search-what-is-data-import.md#indexing-actions)

1. Em **Program.cs**, crie uma série de documentos e ações de índice, e depois passe a matriz para `ndexDocumentsBatch` os documentos abaixo em conformidade com o índice hotels-quickstart-v11, tal como definido pela classe hoteleira.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    qryclient.IndexDocuments(batch, idxoptions);

    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Uma vez inicializando o objeto [IndexDocumentsBatch,](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) pode enviá-lo para o índice chamando [IndexDocuments](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.indexdocuments) no seu objeto [SearchClient.](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient)

    O atraso de 2 segundos compensa a indexação, que é assíncronia, para que todos os documentos possam ser indexados antes de as consultas serem executadas. A codificação num atraso é normalmente apenas necessária em demos, testes e aplicações de amostra.

## <a name="3---search-an-index"></a>3 - Pesquisar um índice

Pode obter resultados de consulta assim que o primeiro documento estiver indexado, mas os testes reais do seu índice devem aguardar até que todos os documentos estejam indexados.

Esta secção adiciona duas peças de funcionalidade: lógica de consulta e resultados. Para consultas, utilize o método ['Procurar'.](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.search) Este método requer o texto de pesquisa (a cadeia de consulta) bem como outras [opções](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions).

A classe [SearchResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1) representa os resultados.

1. Em **Program.cs**, crie um método WriteDocuments que imprime os resultados de pesquisa para a consola.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Crie um método RunQueries para executar consultas e obter resultados de retorno. Os resultados são objetos do hotel.

    ```csharp
    private static void RunQueries(SearchClient qryclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = qryclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = qryclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

Este exemplo mostra as duas [formas de combinar termos numa consulta:](search-query-overview.md#types-of-queries)pesquisa por texto completo e filtros:

+ Consultas de pesquisa por texto completo para obter um ou mais termos nos campos pes pes pesjáveis no seu índice. A primeira consulta é a pesquisa completa por texto. Pode pesquisar por palavras ou melhor. A pesquisa por texto completo produz pontuações de relevância usadas para classificar os resultados.

+ O filtro é uma expressão booleana que é avaliada sobre os campos [IsFilterable](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) num índice. As consultas de filtro incluem ou excluem valores. Como tal, não existe uma pontuação de relevância associada a uma consulta de filtro. As duas últimas consultas demonstram a procura de filtros.

Pode utilizar a pesquisa por texto completo e os filtros juntos ou separadamente.

Tanto as pesquisas como os filtros são realizados utilizando o método [SearchClient.Search.](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.search) Uma consulta de pesquisa pode ser passada na `searchText` cadeia, enquanto uma expressão de filtro pode ser passada na propriedade [Filter](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions.filter) da classe [SearchOptions.](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions) Para filtrar sem procurar, basta passar `"*"` para o parâmetro do método De `searchText` [pesquisa.](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient.search) Para pesquisar sem filtrar, deixe a `Filter` propriedade desaparada, ou não passe em qualquer `SearchOptions` caso.

## <a name="run-the-program"></a>Executar o programa

Prima F5 para reconstruir a app e executar o programa na sua totalidade. 

A saída inclui mensagens de [Console.WriteLIne,](https://docs.microsoft.com/dotnet/api/system.console.writeline)com a adição de informações e resultados de consulta.

## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Neste início rápido C#, trabalhou através de uma série de tarefas para criar um índice, carregá-lo com documentos e executar consultas. Em diferentes fases, tomamos atalhos para simplificar o código de legibilidade e compreensão. Se se sentir confortável com os conceitos básicos, recomendamos o próximo artigo para uma exploração de abordagens e conceitos alternativos que irão aprofundar o seu conhecimento. 

> [!div class="nextstepaction"]
> [Como desenvolver em .NET](search-howto-dotnet-sdk.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)