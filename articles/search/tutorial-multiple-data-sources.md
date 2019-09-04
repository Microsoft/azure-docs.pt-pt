---
title: C#Destina Indexar várias fontes de dados-Azure Search
description: Saiba como importar dados de várias fontes de dados para um único índice de Azure Search.
author: RobDixon22
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: heidist
ms.openlocfilehash: d55a586d3dfb22b5dad377ff656b8d6a6c940bdb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241842"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Destina Combinar dados de várias fontes de dados em um índice Azure Search

Azure Search pode importar, analisar e indexar dados de várias fontes de dados em um único índice de pesquisa combinado. Isso dá suporte a situações em que os dados estruturados são agregados com dados de texto menos estruturados ou até mesmo sem formatação de outras fontes, como documentos de texto, HTML ou JSON.

Este tutorial descreve como indexar dados do hotel de uma fonte de dados Azure Cosmos DB e mesclá-los com detalhes de sala do Hotel desenhados de documentos do armazenamento de BLOBs do Azure. O resultado será um índice de pesquisa de Hotel combinado que contém tipos de dados complexos.

Este tutorial usa C#o SDK do .net para Azure Search e o portal do Azure para realizar as seguintes tarefas:

> [!div class="checklist"]
> * Carregar dados de exemplo e criar fontes de dados
> * Identificar a chave do documento
> * Definir e criar o índice
> * Indexar dados do hotel de Azure Cosmos DB
> * Mesclar dados de sala do Hotel do armazenamento de BLOBs

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste guia de início rápido. 

- [Crie um serviço de Azure Search](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

- [Crie uma conta de Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de Hotel de exemplo.

- [Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de blob JSON de exemplo.

- [Instale o Visual Studio](https://visualstudio.microsoft.com/) para usar como o IDE.

### <a name="install-the-project-from-github"></a>Instalar o projeto do GitHub

1. Localize o repositório de exemplo no GitHub: [Azure-Search-dotnet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **clonar ou baixar** e faça sua cópia local privada do repositório.
1. Abra o Visual Studio e instale o pacote NuGet de pesquisa Microsoft Azure, se ainda não estiver instalado. No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** e, em seguida, **gerenciar pacotes NuGet para a solução...** . Selecione a guia **procurar** e digite "Azure Search" na caixa de pesquisa. Instale o **Microsoft. Azure. Search** quando ele aparecer na lista (versão 9.0.1 ou posterior). Você precisará clicar em caixas de diálogo adicionais para concluir a instalação.

    ![Usando o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Usando o Visual Studio, navegue até o repositório local e abra o arquivo de solução **AzureSearchMultipleDataSources. sln**.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço de Azure Search, você precisa da URL do serviço e de uma chave de acesso. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade http e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade http e uma chave de acesso")

Todas as solicitações exigem uma chave de API em cada solicitação enviada ao seu serviço. Uma chave válida estabelece confiança, por solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Preparar dados de Azure Cosmos DB de exemplo

Este exemplo usa dois conjuntos pequenos de dados que descrevem sete Hotéis fictícios. Um conjunto descreve os mesmos hotéis e será carregado em um banco de dados Azure Cosmos DB. O outro conjunto contém detalhes de sala de Hotel e é fornecido como sete arquivos JSON separados a serem carregados no armazenamento de BLOBs do Azure.

1. [Entre no portal do Azure](https://portal.azure.com)e, em seguida, navegue na página Visão geral da conta do Azure Cosmos DB.

1. Na barra de menus, clique em Adicionar contêiner. Especifique "criar novo banco de dados" e use o nome **Hotel-Rooms-DB**. Insira **Hotéis** para o nome da coleção e **/HotelId** para a chave de partição. Clique em **OK** para criar o banco de dados e o contêiner.

   ![Adicionar Azure Cosmos DB contêiner](media/tutorial-multiple-data-sources/cosmos-add-container.png "Adicionar um contêiner de Azure Cosmos DB")

1. Vá para o Cosmos DB Data Explorer e selecione o elemento **itens** no contêiner **Hotéis** no banco de dados **Hotel-Rooms-DB** . Em seguida, clique em **carregar item** na barra de comandos.

   ![Carregar na coleção de Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Carregar na coleção de Cosmos DB")

1. No painel de carregamento, clique no botão pasta e, em seguida, navegue até o arquivo **cosmosdb/HotelsDataSubset_CosmosDb. JSON** na pasta do projeto. Clique em **OK** para iniciar o carregamento.

   ![Selecionar arquivo para carregar](media/tutorial-multiple-data-sources/cosmos-upload2.png "Selecionar arquivo para carregar")

1. Use o botão atualizar para atualizar a exibição dos itens na coleção de hotéis. Você deve ver sete novos documentos de banco de dados listados.

## <a name="prepare-sample-blob-data"></a>Preparar dados de blob de exemplo

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, cliqueem BLOBs e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) chamado **Hotel-Rooms** para armazenar os arquivos JSON de exemplo de quarto de Hotel. Você pode definir o nível de acesso público para qualquer um de seus valores válidos.

   ![Criar um contêiner de BLOBs](media/tutorial-multiple-data-sources/blob-add-container.png "Criar um contêiner de BLOBs")

1. Depois que o contêiner for criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Carregar na barra de comandos")

1. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e clique em **carregar**.

   ![Carregar arquivos](media/tutorial-multiple-data-sources/blob-upload.png "Carregar arquivos")

Depois que o carregamento for concluído, os arquivos deverão aparecer na lista para o contêiner de dados.

## <a name="set-up-connections"></a>Configurar ligações

As informações de conexão para o serviço de pesquisa e as fontes de dados são especificadas no arquivo **appSettings. JSON** na solução. 

1. No Visual Studio, abra o arquivo **AzureSearchMultipleDataSources. sln** .

1. Em Gerenciador de Soluções, edite o arquivo **appSettings. JSON** .  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "BlobStorageAccountName": "Put your Azure Storage account name here",
  "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
  "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
  "CosmosDBDatabaseName": "hotel-rooms-db"
}
```

As duas primeiras entradas usam a URL e as chaves de administração para seu serviço de Azure Search. Dado um ponto de `https://mydemo.search.windows.net`extremidade de, por exemplo, o nome do serviço `mydemo`a ser fornecido é.

As próximas entradas especificam nomes de conta e informações de cadeia de conexão para o armazenamento de BLOBs do Azure e Azure Cosmos DB fontes de dados.

### <a name="identify-the-document-key"></a>Identificar a chave do documento

Em Azure Search, o campo de chave identifica exclusivamente cada documento no índice. Cada índice de pesquisa deve ter exatamente um campo de chave `Edm.String`do tipo. Esse campo de chave deve estar presente para cada documento em uma fonte de dados que é adicionada ao índice. (Na verdade, é o único campo obrigatório.)

Ao indexar dados de várias fontes de dados, cada valor de chave da fonte de dados deve mapear para o mesmo campo de chave no índice combinado. Geralmente, é necessário um planejamento antecipado para identificar uma chave de documento significativa para o índice e verificar se ele existe em todas as fontes de dados.

Azure Search indexadores podem usar mapeamentos de campo para renomear e até mesmo reformatar campos de dados durante o processo de indexação, para que os dados de origem possam ser direcionados para o campo de índice correto.

Por exemplo, em nossos dados de Azure Cosmos DB de exemplo, o identificador doHotel é chamado de hotelid. Mas nos arquivos de blob JSON para as salas de Hotel, o identificador de Hotel é chamado **ID**. O programa manipula isso mapeando o campo **ID** dos BLOBs para o campo chave do **hotelid** no índice.

> [!NOTE]
> Na maioria dos casos, as chaves de documento geradas automaticamente, como as criadas por padrão por alguns indexadores, não fazem boas chaves de documento para índices combinados. Em geral, você desejará usar um valor de chave exclusivo e significativo que já exista no, ou pode ser facilmente adicionado às suas fontes de dados.

## <a name="understand-the-code"></a>Compreender o código

Depois que os dados e as definições de configuração estiverem em vigor, o programa de exemplo em **AzureSearchMultipleDataSources. sln** deverá estar pronto para ser compilado e executado.

Esse aplicativo C#simples de console/.net executa as seguintes tarefas:
* Cria um novo índice de Azure Search com base na estrutura de dados C# da classe Hotel (que também faz referência às classes address e Room).
* Cria uma Azure Cosmos DB fonte de dados e um indexador que mapeia Azure Cosmos DB dados para campos de índice.
* Executa o indexador Azure Cosmos DB para carregar dados do hotel.
* Cria uma fonte de dados de armazenamento de BLOBs do Azure e um indexador que mapeia dados de blob JSON para campos de índice.
* Executa o indexador de armazenamento de BLOBs do Azure para carregar dados de salas.

 Antes de executar o programa, Reserve um minuto para estudar o código e as definições de índice e indexador para este exemplo. O código relevante está em dois ficheiros:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criam o índice Azure Search, as fontes de dados e os indexadores e carregam os resultados combinados no índice.

### <a name="define-the-index"></a>Definir o índice

Este programa de exemplo usa o SDK do .NET para definir e criar um índice de Azure Search. Ele aproveita a classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma estrutura de índice a partir de C# uma classe de modelo de dados.

O modelo de dados é definido pela classe Hotel, que também contém referências às classes address e Room. O FieldBuilder faz uma busca detalhada por várias definições de classe para gerar uma estrutura de dados complexa para o índice. As marcas de metadados são usadas para definir os atributos de cada campo, como se é pesquisável ou classificável.

Os trechos de código a seguir do arquivo **Hotel.cs** mostram como um único campo e uma referência a outra classe de modelo de dados podem ser especificados.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

No arquivo **Program.cs** , o índice é definido com um nome e uma coleção de campos gerados pelo `FieldBuilder.BuildForType<Hotel>()` método e, em seguida, criado da seguinte maneira:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar Azure Cosmos DB fonte de dados e o indexador

Em seguida, o programa principal inclui a lógica para criar a fonte de dados Azure Cosmos DB para os dados de hotéis.

Primeiro, ele concatena o nome do banco de dados Azure Cosmos DB para a cadeia de conexão. Em seguida, ele define o objeto de fonte de dados, incluindo configurações específicas para Azure Cosmos DB fontes, como a propriedade [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Depois que a fonte de dados é criada, o programa configura um Azure Cosmos DB indexador chamado **Hotel-Rooms-Cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
O programa excluirá todos os indexadores existentes com o mesmo nome antes de criar o novo, caso você queira executar esse exemplo mais de uma vez.

Este exemplo define um agendamento para o indexador, para que ele seja executado uma vez por dia. Você pode remover a propriedade de agendamento dessa chamada se não quiser que o indexador seja executado automaticamente no futuro.

### <a name="index-azure-cosmos-db-data"></a>Índice Azure Cosmos DB dados

Depois que a fonte de dados e o indexador tiverem sido criados, o código que executa o indexador será breve:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Este exemplo inclui um simples bloco try-catch para relatar quaisquer erros que possam ocorrer durante a execução.

Depois que o indexador de Azure Cosmos DB tiver sido executado, o índice de pesquisa conterá um conjunto completo de documentos de Hotel de exemplo. No entanto, o campo de salas para cada Hotel será uma matriz vazia, já que a fonte de dados Azure Cosmos DB não continha detalhes de sala. Em seguida, o programa fará pull do armazenamento de BLOBs para carregar e mesclar os dados da sala.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar fonte de dados de armazenamento de BLOBs e indexador

Para obter os detalhes da sala, o programa primeiro configura uma fonte de dados de armazenamento de BLOBs para fazer referência a um conjunto de arquivos de blob JSON individuais.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Depois que a fonte de dados é criada, o programa configura um indexador de blob chamado **Hotel-Rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Os BLOBs JSON contêm um campo de chave chamado **ID** em vez de **hotelid**. O código usa a `FieldMapping` classe para instruir o indexador a direcionar o valor do campo **ID** para a chave do documento **hotelid** no índice.

Indexadores de armazenamento de BLOBs podem usar parâmetros que identificam o modo de análise a ser usado. O modo de análise é diferente para BLOBs que representam um único documento ou vários documentos dentro do mesmo BLOB. Neste exemplo, cada blob representa um único documento de índice, portanto, o código usa `IndexingParameters.ParseJson()` o parâmetro.

Para obter mais informações sobre os parâmetros de análise do indexador para BLOBs JSON, consulte [indexar BLOBs JSON](search-howto-index-json-blobs.md). Para obter mais informações sobre como especificar esses parâmetros usando o SDK do .NET, consulte a classe [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) .

O programa excluirá todos os indexadores existentes com o mesmo nome antes de criar o novo, caso você queira executar esse exemplo mais de uma vez.

Este exemplo define um agendamento para o indexador, para que ele seja executado uma vez por dia. Você pode remover a propriedade de agendamento dessa chamada se não quiser que o indexador seja executado automaticamente no futuro.

### <a name="index-blob-data"></a>Dados de blob de índice

Depois que a fonte de dados do armazenamento de BLOBs e o indexador tiverem sido criados, o código que executa o indexador será simples:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Como o índice já foi populado com dados do Hotel do banco de Azure Cosmos DB, o indexador de blob atualiza os documentos existentes no índice e adiciona os detalhes da sala.

> [!NOTE]
> Se você tiver os mesmos campos não-chave em ambas as fontes de dados e os dados dentro desses campos não corresponderem, o índice conterá os valores de qualquer indexador executado mais recentemente. Em nosso exemplo, ambas as fontes de dados contêm um campo hotelname. Se, por algum motivo, os dados nesse campo forem diferentes, para documentos com o mesmo valor de chave, os dados do hotelname da fonte de dados que foi indexado mais recentemente serão o valor armazenado no índice.

## <a name="search-your-json-files"></a>Pesquisar os ficheiros JSON

Você pode explorar o índice de pesquisa preenchido após a execução do programa, usando o [**Gerenciador de pesquisa**](search-explorer.md) no Portal.

No portal do Azure, abra a página **visão geral** do serviço de pesquisa e localize o índice **Hotel-Rooms-Sample** na lista **índices** .

  ![Lista de índices de Azure Search](media/tutorial-multiple-data-sources/index-list.png "Lista de índices de Azure Search")

Clique no índice Hotel-Rooms-Sample na lista. Você verá uma interface do Gerenciador de pesquisa para o índice. Insira uma consulta para um termo como "luxo". Você deve ver pelo menos um documento nos resultados e este documento deve mostrar uma lista de objetos de sala em sua matriz de salas.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar após o tutorial é eliminar o grupo de recursos que contém o serviço Azure Search. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página Visão geral do serviço de Azure Search.

## <a name="next-steps"></a>Passos Seguintes

Há várias abordagens e várias opções para indexação de BLOBs JSON. Se os dados de origem incluírem conteúdo JSON, você poderá examinar essas opções para ver o que funciona melhor para seu cenário.

> [!div class="nextstepaction"]
> [Como indexar BLOBs JSON usando Azure Search indexador de BLOB](search-howto-index-json-blobs.md)

Talvez você queira aumentar os dados de índice estruturados de uma fonte de dados com dados enriquecedos cognitivamente de BLOBs não estruturados ou conteúdo de texto completo. O tutorial a seguir mostra como usar serviços cognitivas junto com Azure Search, usando o SDK do .NET.

> [!div class="nextstepaction"]
> [Chamar API de Serviços Cognitivos em um pipeline de indexação de Azure Search](cognitive-search-tutorial-blob-dotnet.md)
