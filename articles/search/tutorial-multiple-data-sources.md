---
title: 'Tutorial: Índice de várias origens de dados - Azure Search'
description: Saiba como importar dados de várias origens de dados para um único índice da Azure Search.
author: RobDixon22
manager: HeidiSteen
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: v-rodixo
ms.custom: seodec2018
ms.openlocfilehash: 8ce3c66432f3d2d0cb973886498aa46e7820698c
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485273"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C#Tutorial: Combinar dados de várias origens de dados num índice da Azure Search

O Azure Search pode importar, analisar e indexar dados de várias origens de dados para um índice de pesquisa combinado único. Isso oferece suporte a situações em que os dados estruturados são agregados com dados de texto sem formatação de menos estruturadas ou até mesmo de outras origens, como texto, HTML, ou documentos JSON.

Este tutorial descreve como indexar hotel dados de uma origem de dados do Azure Cosmos DB e intercalar que com os detalhes de sala de hotel desenhados de documentos do armazenamento de Blobs do Azure. O resultado será um índice de pesquisa de hotel combinado que contém os tipos de dados complexos.

Este tutorial utiliza o C#, o SDK de .NET para Azure Search e o portal do Azure efetuar as seguintes tarefas:

> [!div class="checklist"]
> * Carregar dados de exemplo e criar origens de dados
> * Identificar a chave do documento
> * Definir e criar o índice
> * Dados de hotel de índice do Azure Cosmos DB
> * Intercalar dados de sala de hotel de armazenamento de BLOBs

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes serviços, ferramentas e dados são utilizados neste início rápido. 

- [Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

- [Criar uma conta do Azure Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de hotel de exemplo.

- [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) dados de BLOBs para armazenar o JSON de exemplo.

- [Instalar o Visual Studio](https://visualstudio.microsoft.com/) para utilizar como o IDE.

### <a name="install-the-project-from-github"></a>Instale o projeto do GitHub

1. Localize o repositório de exemplo no GitHub: [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **clonar ou transferir** e fazer sua cópia local privada do repositório.
1. Abra o Visual Studio e instale o pacote NuGet de pesquisa do Microsoft Azure, se ainda não estiver instalado. Na **ferramentas** menu, selecione **Gestor de pacotes NuGet** e, em seguida, **gerir pacotes NuGet para solução de...** . Selecione o **procurar** separador, em seguida, escreva "Azure Search" na caixa de pesquisa. Instale **Microsoft.Azure.Search** quando for apresentada na lista (versão 9.0.1, ou posterior). Terá de clicar nas caixas de diálogo adicionais para concluir a instalação.

    ![Usando o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Com o Visual Studio, navegue para o seu repositório local e abra o ficheiro de solução **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Obter uma chave e o URL

Para interagir com o serviço Azure Search, terá do URL do serviço e uma chave de acesso. É criado um serviço de pesquisa com ambos os elementos, pelo que, se tiver adicionado o Azure Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inicie sessão no portal do Azure](https://portal.azure.com/)e no seu serviço de pesquisa **descrição geral** página, obter o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Na **configurações** > **chaves**, obter uma chave de administrador para todos os direitos no serviço. Existem duas chaves de administração intercambiáveis, fornecidas para a continuidade do negócio, caso seja necessário fazer o rollover um. Pode utilizar tanto a chave primária ou secundária em pedidos para adicionar, modificar e eliminar objetos.

![Obter uma chave de acesso e de ponto final HTTP](media/search-get-started-postman/get-url-key.png "obter uma chave de acesso e de ponto final HTTP")

Todos os pedidos requerem uma chave de api em cada pedido enviado ao seu serviço. Uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Preparar os dados do Azure Cosmos DB de exemplo

Este exemplo usa dois pequenos conjuntos de dados que descrevem sete hotéis fictícia. Um conjunto descreve os hotéis propriamente ditas e será carregado num banco de dados do Azure Cosmos DB. O outro conjunto contém detalhes de sala de hotel e é fornecido como sete arquivos JSON separados para ser carregado para o armazenamento de Blobs do Azure.

1. [Inicie sessão no portal do Azure](https://portal.azure.com)e, em seguida, navegue até sua página de descrição geral de conta do Azure Cosmos DB.

1. Na barra de menus, clique em Adicionar contentor. Especifique "Criar nova base de dados" e utilize o nome **hotel-salas-db**. Introduza **salas de hotel** para o nome da coleção, e **/HotelId** da chave de partição. Clique em **OK** para criar a base de dados e o contentor.

   ![Adicionar contentor do Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-add-container.png "adicionar um contentor do Azure Cosmos DB")

1. Vá para o Cosmos DB Data Explorer e selecione o **itens** elemento sob o **hotéis** contentor no **hotel-salas-db** base de dados. Em seguida, clique em **carregar Item** na barra de comandos.

   ![Carregar para a coleção do Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "carregar para a coleção do Cosmos DB")

1. No painel de carregamento, clique no botão de pasta e, em seguida, navegue para o ficheiro **cosmosdb/HotelsDataSubset_CosmosDb.json** na pasta de projeto. Clique em **OK** para iniciar o carregamento.

   ![Selecionar ficheiro para carregar](media/tutorial-multiple-data-sources/cosmos-upload2.png "selecione ficheiro a carregar")

1. Utilize o botão de atualização para atualizar a vista dos itens na coleção hotéis. Deverá ver sete novos da base de dados documentos listados.

## <a name="prepare-sample-blob-data"></a>Preparar os dados de blob de exemplo

1. [Inicie sessão no portal do Azure](https://portal.azure.com), navegue até à sua conta de armazenamento do Azure, clique em **Blobs**e, em seguida, clique em **+ contentor**.

1. [Criar um contentor de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) com o nome **salas de hotel** para armazenar os ficheiros de JSON de sala de hotel de exemplo. Pode definir o nível de acesso público a qualquer um dos respetivos valores válidos.

   ![Criar um contentor de BLOBs](media/tutorial-multiple-data-sources/blob-add-container.png "criar um contentor de BLOBs")

1. Depois do contentor é criado, abra-o e selecione **carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "carregar na barra de comando")

1. Navegue para a pasta que contém os ficheiros de exemplo. Selecione todos eles e, em seguida, clique em **carregar**.

   ![Carregar ficheiros](media/tutorial-multiple-data-sources/blob-upload.png "carregar ficheiros")

Após a conclusão do carregamento, os ficheiros devem aparecer na lista para o contentor de dados.

## <a name="set-up-connections"></a>Configurar ligações

Informações de ligação para o serviço de pesquisa e as origens de dados são especificadas na **appSettings** arquivo da solução. 

1. No Visual Studio, abra a **AzureSearchMultipleDataSources.sln** ficheiro.

1. No Solution Explorer, editar a **appSettings** ficheiro.  

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

As duas primeiras entradas utilizam as chaves de administração e do URL para o serviço Azure Search. Tendo em conta um ponto final de `https://mydemo.search.windows.net`, por exemplo, é o nome do serviço para fornecer `mydemo`.

As entradas seguintes especificar nomes de contas e informações de cadeia de ligação para o armazenamento de Blobs do Azure e a origens de dados do Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Identificar a chave do documento

No Azure Search, o campo de chave identifica exclusivamente cada documento no índice. Todos os índices de pesquisa tem de ter exatamente um campo de chave do tipo `Edm.String`. Esse campo de chave tem de estar presente para cada documento numa origem de dados que é adicionado ao índice. (Na verdade, é o único campo obrigatório.)

Durante a indexação dos dados de várias origens de dados, cada valor de chave de origem de dados tem de mapear para o mesmo campo de chave no índice combinado. Muitas vezes requer um planejamento prévio identificar uma chave de documento significativo para o índice e certifique-se de que existe em cada origem de dados.

Indexadores do Azure Search podem utilizar os mapeamentos de campo para mudar o nome e até mesmo reformatar os campos de dados durante o processo de indexação, para que os dados de origem podem ser direcionados para o campo de índice correto.

Por exemplo, nos nossos dados do Azure Cosmos DB de exemplo, o identificador de hotel é designado **HotelId**. Mas o identificador de hotel com o nome dos ficheiros de blob JSON para os ambientes de hotel, **Id**. O programa lida com isso, mapeando os **Id** campo de blobs para o **HotelId** campo de chave no índice.

> [!NOTE]
> Na maioria dos casos chaves de documento gerado automaticamente, como aqueles criados por predefinição, algumas indexadores, não faça chaves de documento boa para índices combinadas. Em geral irá querer utilizar um valor de chave com significado, exclusivo que já existe no, ou pode ser adicionados facilmente para as origens de dados.

## <a name="understand-the-code"></a>Compreender o código

Assim que são as definições de configuração e dados no local, o exemplo de programa na **AzureSearchMultipleDataSources.sln** deve estar pronto para criar e executar.

Esta simples C#/aplicação de consola .NET realiza as seguintes tarefas:
* Cria um novo índice da Azure Search com base na estrutura de dados do C# classe de Hotel (que também faz referência as classes de endereço e espaço).
* Cria uma origem de dados do Azure Cosmos DB e um indexador que mapeia os dados do Azure Cosmos DB para campos de índice.
* É executado o indexador do Azure Cosmos DB para carregar dados de Hotel.
* Cria uma origem de dados do armazenamento de Blobs do Azure e um indexador que mapeia os dados de blob JSON para campos de índice.
* É executado o indexador do armazenamento de Blobs do Azure para carregar dados de salas.

 Antes de executar o programa, reserve um tempo para estudar o código e as definições de índice e indexador para este exemplo. O código relevante está em dois ficheiros:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criar o índice do Azure Search, origens de dados e indexadores e carregar os resultados combinados para o índice.

### <a name="define-the-index"></a>Definir o índice

Este programa de exemplo utiliza o SDK do .NET para definir e criar um índice da Azure Search. Tira partido do [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) classe para gerar uma estrutura de índice a partir de um C# classe de modelo de dados.

O modelo de dados é definido pela classe Hotel, que também contém referências para as classes de endereço e a sala. A FieldBuilder aprofunda por meio de várias definições de classe para gerar uma estrutura de dados complexos para o índice. Marcas de metadados são utilizadas para definir os atributos de cada campo, como seja pesquisável ou ordenável.

Os seguintes fragmentos do **Hotel.cs** ficheiro Mostrar como um único campo e uma referência a outra classe de modelo de dados, podem ser especificados.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

Na **Program.cs** arquivo, o índice é definido com um nome e uma coleção de campos gerados pelo `FieldBuilder.BuildForType<Hotel>()` método e, em seguida, criou da seguinte forma:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar origem de dados do Azure Cosmos DB e indexador

Em seguida, o programa principal inclui lógica para criar a origem de dados do Azure Cosmos DB para os dados de hotéis.

Em primeiro lugar, ele concatena o nome de base de dados do Azure Cosmos DB para a cadeia de ligação. Em seguida, ele define o objeto de origem de dados, incluindo as definições específicas para origens de Azure Cosmos DB, como a propriedade [useChangeDetection].

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

Depois de criar a origem de dados, o programa configura um indexador do Azure Cosmos DB com o nome **hotel-salas-cosmos-indexador**.

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
O programa irá eliminar quaisquer indexadores existentes com o mesmo nome antes de criar o um novo, no caso de que pretende executar este exemplo mais de uma vez.

Este exemplo define uma agenda para o indexador, para que ele será executado uma vez por dia. Pode remover a propriedade de agenda dessa chamada, se não pretender que o indexador automaticamente executar novamente no futuro.

### <a name="index-azure-cosmos-db-data"></a>Dados de índice do Azure Cosmos DB

Quando a origem de dados e o indexador tem sido criadas, o código que executa o indexador é breve:

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

Este exemplo inclui um bloco simples try-catch para relatar erros que possam ocorrer durante a execução.

Após a execução do indexador do Azure Cosmos DB, o índice de pesquisa irá conter um conjunto completo de documentos de hotel de exemplo. No entanto o campo de salas de cada hotel será uma matriz vazia, uma vez que a origem de dados do Azure Cosmos DB contidos não existem detalhes de espaço. Em seguida, o programa irá extrair do armazenamento de BLOBs para carregar e intercalar os dados de espaço.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar origem de dados do armazenamento de BLOBs e indexador

Para obter os detalhes de sala o programa primeiro configura uma origem de dados do armazenamento de BLOBs para fazer referência a um conjunto de ficheiros de blob JSON individuais.

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

Depois de criar a origem de dados, o programa configura um indexador de Blobs com o nome **hotel-salas-BLOBs-indexador**.

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

Os blobs JSON contém um campo de chave com o nome **Id** em vez de **HotelId**. O código utiliza a `FieldMapping` classe para informar o indexador para direcionar o **Id** campo valor para o **HotelId** chave do documento no índice.

Os indexadores de armazenamento de BLOBs podem utilizar os parâmetros que identificam o modo de análise a utilizar. O modo de análise é diferente para os blobs que representam um documento único ou vários documentos dentro do mesmo blob. Neste exemplo, cada blob representa um documento de índice único, para que o código utiliza o `IndexingParameters.ParseJson()` parâmetro.

Para obter mais informações sobre parâmetros para JSON blobs de análise de indexador, consulte [blobs JSON de indexação](search-howto-index-json-blobs.md). Para obter mais informações sobre como especificar estes parâmetros com o SDK .NET, consulte a [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) classe.

O programa irá eliminar quaisquer indexadores existentes com o mesmo nome antes de criar o um novo, no caso de que pretende executar este exemplo mais de uma vez.

Este exemplo define uma agenda para o indexador, para que ele será executado uma vez por dia. Pode remover a propriedade de agenda dessa chamada, se não pretender que o indexador automaticamente executar novamente no futuro.

### <a name="index-blob-data"></a>Dados de Blobs do índice

Assim que o indexador e origem de dados do armazenamento de BLOBs foram criados, o código que executa o indexador é simples:

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

Uma vez que o índice já tiver sido preenchido com dados de hotel da base de dados do Azure Cosmos DB, o indexador blob atualiza os documentos existentes no índice e adiciona os detalhes de espaço.

> [!NOTE]
> Se tiver os mesmos campos de sem chave no ambos suas origens de dados e os dados dentro desses campos não corresponde a, em seguida, o índice irá conter os valores de qualquer que seja indexador última execução. No nosso exemplo, ambas as origens de dados contêm uma **HotelName** campo. Se por alguma razão os dados neste campo é diferente, para documentos com o mesmo valor de chave, o **HotelName** dados da origem de dados que foi indexado mais recentemente será o valor armazenado no índice.

## <a name="search-your-json-files"></a>Pesquisar os ficheiros JSON

Pode explorar o índice de pesquisa preenchida após a execução do programa, utilizando o [ **Explorador de pesquisa** ](search-explorer.md) no portal.

No portal do Azure, abra o serviço de pesquisa **descrição geral** página e encontre a **hotel-salas-sample** de índice no **índices** lista.

  ![Lista de índices da Azure Search](media/tutorial-multiple-data-sources/index-list.png "índices de lista do Azure Search")

Clique no índice de exemplo de hotel salas na lista. Verá uma interface do Explorador de pesquisa para o índice. Introduza uma consulta por um período como "Luxo". Deverá ver pelo menos um documento nos resultados e, neste documento deve mostrar uma lista de objetos de espaço na sua matriz de ambientes.

## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar após o tutorial é eliminar o grupo de recursos que contém o serviço Azure Search. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos é na página de descrição geral do serviço Azure Search.

## <a name="next-steps"></a>Passos Seguintes

Existem várias abordagens e várias opções para indexar JSON blobs. Se sua fonte de dados inclui conteúdo JSON, pode rever estas opções para ver o que funciona melhor para o seu cenário.

> [!div class="nextstepaction"]
> [Como indexar blobs JSON com o indexador Blob do Azure Search](search-howto-index-json-blobs.md)

Pode querer aumentar os dados de índice estruturado de uma origem de dados com cognitively plena dados de blobs não estruturados ou conteúdo de texto completo. O tutorial seguinte mostra como utilizar os serviços cognitivos, juntamente com o Azure Search, utilizando o SDK .NET.

> [!div class="nextstepaction"]
> [Chamar APIs serviços cognitivos num pipeline de indexação do Azure Search](cognitive-search-tutorial-blob-dotnet.md)
