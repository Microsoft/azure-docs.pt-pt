---
title: C# tutorial indexando várias fontes de dados do Azure
titleSuffix: Azure Cognitive Search
description: Aprenda a importar dados de várias fontes de dados para um único índice de Pesquisa Cognitiva Azure usando indexantes. Este tutorial e o código de amostra estão em C#.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 06b80b5fe14a7a913d8ad8454c6568b04fe01c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819793"
---
# <a name="tutorial-index-from-multiple-data-sources-using-the-net-sdk"></a>Tutorial: Índice de várias fontes de dados utilizando o .NET SDK

A Azure Cognitive Search pode importar, analisar e indexar dados de várias fontes de dados para um único índice de pesquisa consolidado. Isto suporta situações em que os dados estruturados são agregados com dados de texto menos estruturados ou mesmo simples de outras fontes, como texto, HTML ou documentos JSON.

Este tutorial descreve como indexar os dados do hotel a partir de uma fonte de dados DB da Azure Cosmos e fundir isso com detalhes do quarto de hotel extraídos de documentos de armazenamento Azure Blob. O resultado será um índice combinado de pesquisa de hotéis contendo tipos de dados complexos.

Este tutorial utiliza C# e o [.NET SDK](/dotnet/api/overview/azure/search). Neste tutorial, realizará as seguintes tarefas:

> [!div class="checklist"]
> * Carre faça upload de dados de amostras e crie fontes de dados
> * Identificar a chave do documento
> * Definir e criar o índice
> * Dados do hotel indexados da Azure Cosmos DB
> * Fundir dados do quarto de hotel a partir do armazenamento de blob

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Azure Cosmos DB](../cosmos-db/create-cosmosdb-resources-portal.md)
+ [Armazenamento do Azure](../storage/common/storage-account-create.md)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexantes e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de ter espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

O código-fonte para este tutorial encontra-se no repositório GitHub, de [pesquisa de azure-search-dotnet,](https://github.com/Azure-Samples/azure-search-dotnet-samples) na pasta [de múltiplas fontes de dados.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial usa a Azure Cognitive Search para indexar e consultas, Azure Cosmos DB para um conjunto de dados, e armazenamento Azure Blob para o segundo conjunto de dados. 

Se possível, crie todos os serviços na mesma região e grupo de recursos para proximidade e gestão. Na prática, os seus serviços podem ser em qualquer região.

Esta amostra usa dois pequenos conjuntos de dados que descrevem sete hotéis fictícios. Um conjunto descreve os hotéis em si, e será carregado em uma base de dados DB Azure Cosmos. O outro conjunto contém detalhes do quarto de hotel, e é fornecido como sete ficheiros JSON separados para serem enviados para o Azure Blob Storage.

### <a name="start-with-cosmos-db"></a>Comece com cosmos DB

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, navegue na sua página geral da conta Azure Cosmos DB.

1. Selecione **Data Explorer** e, em seguida, selecione **New Database**.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-newdb.png" alt-text="Criar uma nova base de dados" border="false":::

1. Insira o nome **hotel-quartos-db**. Aceite valores predefinidos para as definições restantes.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-dbname.png" alt-text="Criar uma nova base de dados" border="false":::

1. Criar um novo recipiente. Use a base de dados existente que acabou de criar. **Insira hotéis** para o nome do recipiente e use **/HotelId** para a chave partição.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-add-container.png" alt-text="Criar uma nova base de dados" border="false":::

1. Selecione **Itens** em **hotéis**e, em seguida, clique em **Enviar artigo** na barra de comando. Navegue para e, em seguida, selecione o **ficheiro cosmosdb/HotelsDataSubset_CosmosDb.jsna** pasta do projeto.

   :::image type="content" source="media/tutorial-multiple-data-sources/cosmos-upload.png" alt-text="Criar uma nova base de dados" border="false":::

1. Use o botão Refresh para refrescar a sua visão dos itens na coleção de hotéis. Devia ver sete novos documentos da base de dados listados.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

1. Inscreva-se no [portal Azure,](https://portal.azure.com)navegue para a sua conta de armazenamento Azure, clique em **Blobs**e clique **em + Recipiente**.

1. [Crie um recipiente de blob](../storage/blobs/storage-quickstart-blobs-portal.md) chamado **quartos de hotel** para armazenar os ficheiros JSON do quarto de hotel. Pode definir o Nível de Acesso Público a qualquer um dos seus valores válidos.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-add-container.png" alt-text="Criar uma nova base de dados" border="false":::

1. Depois de criado o recipiente, abra-o e selecione **upload** na barra de comando. Navegue para a pasta que contém os ficheiros de amostragem. Selecione todos e, em seguida, clique em **Upload**.

   :::image type="content" source="media/tutorial-multiple-data-sources/blob-upload.png" alt-text="Criar uma nova base de dados" border="false":::

Após o envio concluído, os ficheiros devem aparecer na lista para o recipiente de dados.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é Azure Cognitive Search, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Free para completar este walkthrough. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma api-chave de administrador e URL para pesquisa cognitiva Azure

Para interagir com o seu serviço de Pesquisa Cognitiva Azure, necessitará do URL de serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso apenas de leitura.

   :::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Criar uma nova base de dados" border="false":::

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="2---set-up-your-environment"></a>2 - Configurar o seu ambiente

1. Inicie o Visual Studio 2019 e no menu **Ferramentas,** selecione **NuGet Package Manager** e, em seguida, **Gerencie pacotes NuGet para solução...**. 

1. No separador **Procurar,** encontrar e instalar **o Microsoft.Azure.Search** (versão 9.0.1, ou mais tarde). Terá de clicar através de diálogos adicionais para completar a instalação.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Criar uma nova base de dados" border="false":::

1. Procure o **Microsoft.Extensions.Configuration.Jsno** pacote NuGet e instale-o também.

1. Abra o ficheiro de solução **AzureSearchMultipleDataSources.sln**.

1. No Solution Explorer, edite o **appsettings.jsno** ficheiro para adicionar informações de ligação.  

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

As duas primeiras entradas utilizam as teclas URL e administração do seu serviço de Pesquisa Cognitiva Azure. Dado um ponto final `https://mydemo.search.windows.net` de, por exemplo, o nome de serviço a fornecer é `mydemo` .

As próximas entradas especificam nomes de conta e informações de cadeia de ligação para as fontes de dados Azure Blob Storage e Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 - Campos-chave do mapa

A fusão do conteúdo requer que ambos os fluxos de dados estejam a direcionar os mesmos documentos no índice de pesquisa. 

Na Pesquisa Cognitiva Azure, o campo chave identifica exclusivamente cada documento. Cada índice de pesquisa deve ter exatamente um campo chave do tipo `Edm.String` . Este campo-chave deve estar presente para cada documento numa fonte de dados adicionada ao índice. (Na verdade, é o único campo necessário.)

Ao indexar dados de várias fontes de dados, certifique-se de que cada linha ou documento de entrada contém uma chave de documento comum para fundir dados de dois documentos de origem fisicamente distintos num novo documento de pesquisa no índice combinado. 

Muitas vezes requer algum planeamento frontal para identificar uma chave de documento significativa para o seu índice, e certifique-se de que existe em ambas as fontes de dados. Nesta demonstração, a `HotelId` chave para cada hotel em Cosmos DB também está presente nos quartos JSON blobs no armazenamento Blob.

Os indexantes de Pesquisa Cognitiva Azure podem usar mapeamentos de campo para renomear e até mesmo reformar campos de dados durante o processo de indexação, de modo que os dados de origem podem ser direcionados para o campo de índice correto. Por exemplo, em Cosmos DB, o identificador do hotel chama-se **`HotelId`** . Mas nos ficheiros da bolha JSON para os quartos de hotel, o identificador do hotel **`Id`** chama-se. O programa trata disto mapeando o **`Id`** campo das bolhas para o **`HotelId`** campo chave no índice.

> [!NOTE]
> Na maioria dos casos, as chaves de documento geradas automaticamente, como as criadas por padrão por alguns indexantes, não fazem boas chaves documentais para índices combinados. Em geral, irá querer utilizar um valor-chave significativo e único que já exista ou possa ser facilmente adicionado às suas fontes de dados.

## <a name="4---explore-the-code"></a>4 - Explorar o código

Uma vez que as definições de dados e configurações estejam em vigor, o programa de amostras em **AzureSearchMultipleDataSources.sln** deve estar pronto para construir e executar.

Esta simples aplicação de consola C#/.NET executa as seguintes tarefas:

* Cria um novo índice baseado na estrutura de dados da classe C# Hotel (que também faz referência às classes Address e Room).
* Cria uma nova fonte de dados e um indexante que mapeia dados de DB do Azure Cosmos para indexar campos. Estes são ambos objetos na Pesquisa Cognitiva Azure.
* Executa o indexante para carregar os dados do Hotel da Cosmos DB.
* Cria uma segunda fonte de dados e um indexante que mapeia dados de bolhas JSON para campos de índice.
* Executa o segundo indexante para carregar os dados dos Quartos a partir do armazenamento do Blob.

 Antes de executar o programa, desemo minuto para estudar o código e as definições de índice e indexante para esta amostra. O código relevante está em dois ficheiros:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criam o índice de Pesquisa Cognitiva Azure, fontes de dados e indexadores, e carregam os resultados combinados no índice.

### <a name="create-an-index"></a>Criar um índice

Este programa de amostragem utiliza o .NET SDK para definir e criar um índice de Pesquisa Cognitiva Azure. Aproveita a classe [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma estrutura de índice a partir de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências às classes Address e Room. O FieldBuilder perfura através de múltiplas definições de classe para gerar uma estrutura de dados complexa para o índice. As tags de metadados são usadas para definir os atributos de cada campo, tais como se é pesmável ou ordenado.

Os seguintes excertos do ficheiro **Hotel.cs** mostram como um único campo, e uma referência a outra classe de modelo de dados, podem ser especificados.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

No ficheiro **Program.cs,** o índice é definido com um nome e uma coleção de campo gerada pelo `FieldBuilder.BuildForType<Hotel>()` método, e depois criado da seguinte forma:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar fonte de dados Esb DB da Azure Cosmos

Em seguida, o programa principal inclui lógica para criar a fonte de dados DB Azure Cosmos para os dados dos hotéis.

Primeiro, concatena o nome da base de dados Azure Cosmos DB para a cadeia de ligação. Em seguida, define o objeto de origem de dados, incluindo configurações específicas para fontes DB Azure Cosmos, como a propriedade [useChangeDetection].

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

Após a criação da fonte de dados, o programa configura um indexante DB Azure Cosmos chamado **hotel-quartos-cosmos-indexante**.

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
O programa eliminará quaisquer indexantes existentes com o mesmo nome antes de criar o novo, caso pretenda executar este exemplo mais de uma vez.

Este exemplo define um calendário para o indexante, de modo que será executado uma vez por dia. Pode remover a propriedade do agendamento desta chamada se não quiser que o indexante volte a funcionar automaticamente no futuro.

### <a name="index-azure-cosmos-db-data"></a>Dados do Índice Azure Cosmos DB

Uma vez criada a fonte de dados e o indexante, o código que executa o indexante é breve:

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

Este exemplo inclui um simples bloco de captura para relatar quaisquer erros que possam ocorrer durante a execução.

Após a execução do indexante DB Azure Cosmos, o índice de pesquisa conterá um conjunto completo de documentos do hotel de amostra. No entanto, o campo de quartos para cada hotel será uma matriz vazia, uma vez que a fonte de dados do Azure Cosmos não continha detalhes do quarto. Em seguida, o programa irá retirar do armazenamento Blob para carregar e fundir os dados da sala.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar fonte de dados de armazenamento Blob e indexante

Para obter os detalhes do quarto, o programa configura primeiro uma fonte de dados de armazenamento Blob para fazer referência a um conjunto de ficheiros de blob JSON individuais.

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

Após a criação da fonte de dados, o programa configura um indexante blob chamado **hotel-rooms-blob-indexer**.

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

As bolhas JSON contêm um campo chave nomeado **`Id`** em vez de **`HotelId`** . O código utiliza a `FieldMapping` classe para dizer ao indexante para direcionar o valor de campo para a chave de documento no **`Id`** **`HotelId`** índice.

Os indexantes de armazenamento blob podem usar parâmetros que identifiquem o modo de análise a ser utilizado. O modo de análise difere para bolhas que representam um único documento, ou vários documentos dentro da mesma bolha. Neste exemplo, cada bolha representa um único documento de índice, pelo que o código utiliza o `IndexingParameters.ParseJson()` parâmetro.

Para obter mais informações sobre os parâmetros de análise do indexante para as bolhas JSON, consulte [as bolhas índice JSON](search-howto-index-json-blobs.md). Para obter mais informações sobre a especificação destes parâmetros utilizando o .NET SDK, consulte a classe [IndexerParametersExtension.](/dotnet/api/microsoft.azure.search.models.indexingparametersextensions)

O programa eliminará quaisquer indexantes existentes com o mesmo nome antes de criar o novo, caso pretenda executar este exemplo mais de uma vez.

Este exemplo define um calendário para o indexante, de modo que será executado uma vez por dia. Pode remover a propriedade do agendamento desta chamada se não quiser que o indexante volte a funcionar automaticamente no futuro.

### <a name="index-blob-data"></a>Dados de blob de índice

Uma vez criada a fonte de dados de armazenamento Blob e o indexante, o código que executa o indexante é simples:

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

Como o índice já foi preenchido com dados do hotel da base de dados DB Azure Cosmos, o indexante blob atualiza os documentos existentes no índice e adiciona os detalhes do quarto.

> [!NOTE]
> Se tiver os mesmos campos não-chave em ambas as suas fontes de dados, e os dados dentro desses campos não corresponderem, então o índice conterá os valores de qualquer indexante que tenha sido mais recentemente. No nosso exemplo, ambas as fontes de dados contêm um campo **HotelName.** Se por alguma razão os dados neste campo forem diferentes, para documentos com o mesmo valor-chave, então os dados do **HotelName** da fonte de dados que foi indexado mais recentemente serão o valor armazenado no índice.

## <a name="5---search"></a>5 - Pesquisar

Pode explorar o índice de pesquisa povoado após o programa ter sido executado, utilizando o [**explorador de Pesquisa**](search-explorer.md) no portal.

No portal Azure, abra a página **geral** do serviço de pesquisa e encontre o índice **de amostra de quartos de hotel** na lista de **Índices.**

  :::image type="content" source="media/tutorial-multiple-data-sources/index-list.png" alt-text="Criar uma nova base de dados" border="false":::

Clique no índice de amostra de quartos de hotel na lista. Verá uma interface do Explorador de Pesquisa para o índice. Insira uma consulta para um termo como "Luxo". Você deve ver pelo menos um documento nos resultados, e este documento deve mostrar uma lista de objetos de sala na sua matriz de quartos.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra deste tutorial verifica os objetos existentes e elimina-os para que possa voltar a verificar o seu código.

Também pode utilizar o portal para eliminar índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o conceito de ingerir dados de várias fontes, vamos olhar mais de perto para a configuração do indexante, a começar pelo Cosmos DB.

> [!div class="nextstepaction"]
> [Configurar um indexador Azure Cosmos DB](search-howto-index-cosmosdb.md)