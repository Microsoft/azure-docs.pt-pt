---
title: 'Tutorial: Index data in C# from Azure SQL databases'
titleSuffix: Azure Cognitive Search
description: In this C# tutorial, connect to Azure SQL database, extract searchable data, and load it into an Azure Cognitive Search index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 36215403f99cc86ab4fb111ce95a6b3190063d7b
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406718"
---
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Import Azure SQL database in C# using Azure Cognitive Search indexers

Learn how to configure an indexer for extracting searchable data from a sample Azure SQL database. [Indexers](search-indexer-overview.md) are a component of Azure Cognitive Search that crawl external data sources, populating a [search index](search-what-is-an-index.md) with content. Of all indexers, the indexer for Azure SQL Database is the most widely used. 

É útil dominar a configuração de indexadores, porque simplifica a quantidade de código que tem de escrever e manter. Em vez de preparar e enviar um conjunto de dados JSON compatível com esquema, pode anexar um indexador a uma origem de dados, fazer com que este extraia dados e inseri-los num índice e, opcionalmente, executar esse índice numa agenda periódica para recolher as alterações na origem subjacente.

In this tutorial, use the [Azure Cognitive Search .NET client libraries](https://aka.ms/search-sdk) and a .NET Core console application to perform the following tasks:

> [!div class="checklist"]
> * Adicionar informações do serviço de pesquisa para as definições da aplicação
> * Preparar um conjunto de dados externo na base de dados SQL do Azure 
> * Rever o índice e as definições do indexador no código de exemplo
> * Executar o código do indexador para importar dados
> * Pesquisar o índice
> * Ver a configuração do indexador no portal

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

The following services, tools, and data are used in this quickstart. 

[Create an Azure Cognitive Search service](search-create-service-portal.md) or [find an existing service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under your current subscription. You can use a free service for this tutorial.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) stores the external data source used by an indexer. A solução de exemplo fornece um ficheiro de dados do SQL para criar a tabela. Steps for creating the service and database are provided in this tutorial.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), any edition, can be used to run the sample solution. Sample code and instructions were tested on the free Community edition.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) provides the sample solution, located in the Azure samples GitHub repository. Download and extract the solution. By default, solutions are read-only. Right-click the solution and clear the read-only attribute so that you can modify files.

> [!Note]
> If you are using the free Azure Cognitive Search service, you are limited to three indexes, three indexers, and three data sources. Este tutorial cria um de cada. Confirme que o seu serviço tem espaço para aceitar os recursos novos.

## <a name="get-a-key-and-url"></a>Get a key and URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. A search service is created with both, so if you added Azure Cognitive Search to your subscription, follow these steps to get the necessary information:

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, get the URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. In **Settings** > **Keys**, get an admin key for full rights on the service. There are two interchangeable admin keys, provided for business continuity in case you need to roll one over. You can use either the primary or secondary key on requests for adding, modifying, and deleting objects.

![Get an HTTP endpoint and access key](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

All requests require an api-key on every request sent to your service. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-connections"></a>Configurar ligações
As informações de ligação dos serviços necessários estão especificadas no ficheiro **appsettings.json**, na solução. 

1. In Visual Studio, open the **DotNetHowToIndexers.sln** file.

1. In Solution Explorer, open **appsettings.json** so that you can populate each setting.  

The first two entries you can fill in right now, using the URL and admin keys for your Azure Cognitive Search service. Given an endpoint of `https://mydemo.search.windows.net`, the service name to provide is `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

The last entry requires an existing database. You'll create it in the next step.

## <a name="prepare-sample-data"></a>Prepare sample data

Neste passo, crie uma origem de dados externa que possa ser pesquisa por um indexador. Pode utilizar o portal do Azure e o ficheiro *hotels.sql* do exemplo para criar o conjunto de dados na Base de Dados SQL do Azure. Azure Cognitive Search consumes flattened rowsets, such as one generated from a view or query. O ficheiro SQL na solução de exemplo cria e preenche uma única tabela.

O exercício seguinte pressupõe que não existe nenhum servidor o base de dados e diz-lhe para criá-los ambos no passo 2. Opcionalmente, se tiver um recurso já existente, pode adicionar a tabela “hotels”, começando no passo 4.

1. [Sign in to the Azure portal](https://portal.azure.com/). 

2. Find or create an **Azure SQL Database** to create a database, server, and resource group. Pode utilizar as predefinições e o nível mais baixo do escalão de preço. Uma vantagem da criação de um servidor é que pode especificar um nome de utilizador e uma palavra-passe de administrador, que são necessários para criar e carregar tabelas num passo posterior.

   ![Página Nova base de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Clique em **Criar** para implementar o servidor e a base de dados novos. Aguarde que o servidor e a base de dados sejam implementados.

4. Abra a página Base de Dados SQL da base de dados nova, se ainda não estiver aberta. O nome do recurso deverá indicar *Base de dados SQL* e não *SQL Server*.

   ![Página Base de dados SQL](./media/search-indexer-tutorial/hotels-db.png)

4. On the navigation pane, click **Query editor (preview)** .

5. Clique em **Iniciar sessão** e introduza o nome de utilizador e a palavra-passe de administrador do servidor.

6. Clique em **Abrir consulta** e navegue para a localização de *hotels.sql*. 

7. Selecione o ficheiro e clique em **Abrir**. O script deverá ser parecido com o da captura de ecrã seguinte:

   ![Script de SQL](./media/search-indexer-tutorial/sql-script.png)

8. Clique em **Executar** para executar a consulta. No painel Resultados, deverá ver uma mensagem de consulta bem-sucedida para três linhas.

9. Para devolver um conjunto de linhas a partir desta tabela, pode executar a consulta seguinte como passo de verificação:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    A consulta prototípica, `SELECT * FROM Hotels`, não funciona no Editor de Consultas. Os dados de exemplo incluem coordenadas geográficas no campo Localização, que não são processadas no editor atualmente. Para obter uma lista de outras colunas a consultar, pode executar a instrução `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Agora que tem um conjunto de dados externo, copie a cadeia de ligação ADO.NET para a base de dados. Na página Base de Dados SQL da sua base de dados, aceda a **Definições** > **Cadeias de Ligação** e copie a cadeia de ligação ADO.NET.
 
    Esta cadeia é semelhante ao exemplo seguinte, modificado para utilizar um nome de base de dados, um nome de utilizador e uma palavra-passe válidos.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Cole a cadeia de ligação em "AzureSqlConnectionString" como a terceira entrada no ficheiro **appsettings.json**, no Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Compreender o código

Once the data and configuration settings are in place, the sample program in **DotNetHowToIndexers.sln** is ready to build and run. Antes de o fazer, dispense uns minutos para estudar as definições do índice e do indexador deste exemplo. O código relevante está em dois ficheiros:

  + **hotel.cs**, que contém o esquema que define o índice
  + **Program.cs**, que contém as funções para criar e gerir estruturas no seu serviço

### <a name="in-hotelcs"></a>Em hotel.cs

O esquema do índice define a coleção de campos, incluindo atributos que especificam as operações permitidas, como, por exemplo, se um determinado campo é de pesquisa em texto completo, filtrável ou ordenável, conforme mostrado na definição de campo abaixo para HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Os esquemas também podem incluir outros elementos, como perfis de classificação para otimizar as classificações de pesquisas, analisadores personalizados e outros construtores. No entanto, para a nossa finalidade, o esquema é definido de forma escassa, consistindo apenas em campos encontrados nos conjuntos de dados de exemplo.

Neste tutorial, o indexador extrai dados de uma origem de dados. In practice, you can attach multiple indexers to the same index, creating a consolidated searchable index from multiple data sources. Pode utilizar o mesmo par indexador-índice, variando apenas as origens de dados, ou um índice com combinações de vários indexadores e origens de dados, dependendo da flexibilidade de que precisar.

### <a name="in-programcs"></a>Em Program.cs

The main program includes logic for creating a client, an index, a data source, and an indexer. Verifica e elimina os recursos existentes com o mesmo nome, no pressuposto de que poderá executar este programa várias vezes.

The data source object is configured with settings that are specific to Azure SQL database resources, including [incremental indexing](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) for leveraging the built-in [change detection features](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) of Azure SQL. The demo hotels database in Azure SQL has a "soft delete" column named **IsDeleted**. When this column is set to true in the database, the indexer removes the corresponding document from the Azure Cognitive Search index.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

An indexer object is platform-agnostic, where  configuration, scheduling, and invocation are the same regardless of the source. This example indexer includes a schedule, a reset option that clears indexer history, and calls a method to create and run the indexer immediately.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Executar o indexador

Neste passo, compile e execute o programa. 

1. No Explorador de Soluções, faça duplo clique em **DotNetHowToIndexers** e selecione **Criar**.
2. Clique novamente em **DotNetHowToIndexers**, seguido de **Depurar** > **Iniciar instância nova**.

O programa é executado no modo de depuração. O estado de cada operação é reportado numa janela da consola.

  ![Script de SQL](./media/search-indexer-tutorial/console-output.png)

O código é executado localmente no Visual Studio e liga-se ao seu serviço de pesquisa no Azure, o qual, por sua vez, utiliza a cadeia de ligação para ligar à Base de Dados SQL do Azure e obter o conjunto de dados. Com tantas operações, existem diversos pontos de falha possíveis. Contudo, se receber um erro, veja as condições abaixo primeiro:

+ As informações de ligação do serviço de pesquisa que indicar estão limitadas ao nome do serviço deste tutorial. Se tiver introduzido o URL completo, as operações param na criação do índice, com um erro de falha ao ligar.

+ A informação de ligação da base de dados em **appsettings.json**. Deve ser a cadeia de ligação ADO.NET obtida no portal e modificada para incluir um nome de utilizador e uma palavra-passe válidas na sua base de dados. A conta de utilizador tem de ter permissão para obter dados.

+ Limites dos recursos. Recall that the Free tier has limits of 3 indexes, indexers, and data sources. Um serviço que esteja no limite máximo não pode criar objetos novos.

## <a name="search-the-index"></a>Pesquisar o índice 

No portal do Azure, na página Descrição Geral do serviço de pesquisa, clique em **Explorador de Pesquisa**, na parte superior, para submeter algumas consultas no novo índice.

1. Clique em **Alterar índice**, na parte superior, para selecionar o índice *hotels*.

2. Clique no botão **Pesquisar** para emitir uma pesquisa vazia. 

   As três entradas no índice são devolvidas como documentos JSON. O Explorador de Pesquisa devolve documentos em JSON, para que possa ver a estrutura inteira.

3. Em seguida, introduza uma cadeia de pesquisa: `search=river&$count=true`. 

   Esta consulta invoca a pesquisa de texto completo para o termo `river` e o resultado inclui um contagem dos documentos correspondentes. Devolver a contagem de documentos correspondentes é útil em cenários de teste, em que tem um índice grande com milhares ou milhões de documentos. Neste caso, apenas um documento corresponde à consulta.

4. Por último, introduza uma cadeia de pesquisa que limite a saída JSON para campos de interesse: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A resposta da consulta é reduzida aos campos selecionados, resultando numa saída mais concisa.

## <a name="view-indexer-configuration"></a>Ver a configuração do indexador

Todos os indexadores, incluindo o que acabou de criar programaticamente, são apresentados no portal. Pode abrir uma definição de indexador e ver a respetiva origem de dados ou configurar uma agenda de atualização para recolher linhas novas e alteradas.

1. [Sign in to the Azure portal](https://portal.azure.com/), and in your search service **Overview** page, click the links for **Indexes**, **Indexers**, and **Data Sources**.
3. Select individual objects to view or modify configuration settings.

   ![Mosaicos de indexador e origem de dados](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

The fastest way to clean up after a tutorial is by deleting the resource group containing the Azure Cognitive Search service. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. In the portal, the resource group name is on the Overview page of Azure Cognitive Search service.

## <a name="next-steps"></a>Passos seguintes

You can attach AI enrichment algorithms to an indexer pipeline. Como próximo passo, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Indexar Documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)