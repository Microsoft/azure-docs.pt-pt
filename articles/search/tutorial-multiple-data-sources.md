---
title: 'C#Tutorial: Indexar várias fontes de dados'
titleSuffix: Azure Cognitive Search
description: Aprenda a importar dados de várias fontes de dados para um único índice de Pesquisa Cognitiva Azure usando indexadores. Este tutorial e código C#de amostra estão dentro .
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 8e75d9de45c64813ac75de635371d2435fb9261f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271486"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Tutorial: Dados do índice de várias fontes de dados emC#

A Pesquisa Cognitiva Azure pode importar, analisar e indexar dados de várias fontes de dados para um único índice de pesquisa consolidado. Isto suporta situações em que os dados estruturados são agregados com dados de texto menos estruturados ou mesmo simples de outras fontes, como documentos de texto, HTML ou JSON.

Este tutorial descreve como indexar dados de hotéis a partir de uma fonte de dados da Azure Cosmos DB e fundir isso com detalhes do quarto de hotel extraídos de documentos de armazenamento Azure Blob. O resultado será um índice combinado de pesquisa de hotéis contendo tipos de dados complexos.

Este tutorial C# utiliza e o [.NET SDK](https://aka.ms/search-sdk). Neste tutorial, realizará as seguintes tarefas:

> [!div class="checklist"]
> * Faça upload de dados da amostra e crie fontes de dados
> * Identifique a chave do documento
> * Definir e criar o índice
> * Index dados do hotel da Azure Cosmos DB
> * Fundir dados do quarto de hotel a partir do armazenamento de blob

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Estúdio Visual 2019](https://visualstudio.microsoft.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa existente 

> [!Note]
> Pode utilizar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de que tem espaço ao seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

O código fonte para este tutorial está no repositório GitHub de [amostras de](https://github.com/Azure-Samples/azure-search-dotnet-samples) pesquisa azul,na pasta [de múltiplas fontes de dados.](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial utiliza a Pesquisa Cognitiva Azure para indexação e consultas, Azure Cosmos DB para um conjunto de dados, e armazenamento Azure Blob para o segundo conjunto de dados. 

Se possível, crie todos os serviços na mesma região e grupo de recursos para proximidade e gestão. Na prática, os seus serviços podem estar em qualquer região.

Esta amostra usa dois pequenos conjuntos de dados que descrevem sete hotéis fictícios. Um conjunto descreve os próprios hotéis, e será carregado numa base de dados Azure Cosmos DB. O outro conjunto contém detalhes do quarto de hotel, e é fornecido como sete ficheiros JSON separados para serem enviados para o Armazenamento De Blob Azure.

### <a name="start-with-cosmos-db"></a>Comece com Cosmos DB

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, navegue na sua página de visão geral da conta Azure Cosmos DB.

1. Selecione **Data Explorer** e, em seguida, selecione **New Database**.

   ![Criar uma nova base de dados](media/tutorial-multiple-data-sources/cosmos-newdb.png "Criar uma nova base de dados")

1. Insira o nome **hotel-quartos-db**. Aceite os valores predefinidos para as definições restantes.

   ![Configure base de dados](media/tutorial-multiple-data-sources/cosmos-dbname.png "Configure base de dados")

1. Crie um novo recipiente. Use a base de dados existente que acabou de criar. Insira **hotéis** para o nome do contentor e **use/HotelId** para a chave Partição.

   ![Adicionar recipiente](media/tutorial-multiple-data-sources/cosmos-add-container.png "Adicionar contentor")

1. Selecione **itens** em **hotéis**e, em seguida, clique em **Carregar item** na barra de comando. Navegue para e, em seguida, selecione o ficheiro **cosmosdb/HotelsDataSubset_CosmosDb.json** na pasta do projeto.

   ![Upload para coleção Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Upload para coleção Cosmos DB")

1. Utilize o botão Refresh para refrescar a sua visão dos itens da coleção de hotéis. Devia ver sete novos documentos de base de dados listados.

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

1. Inscreva-se no [portal Azure,](https://portal.azure.com)navegue na sua conta de armazenamento Azure, clique em **Blobs,** e depois clique em **+ Recipiente**.

1. [Crie um recipiente de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) chamado **quartos de hotel** para armazenar os arquivos JSON do quarto de hotel. Pode definir o Nível de Acesso Público a qualquer um dos seus valores válidos.

   ![Criar um recipiente de bolhas](media/tutorial-multiple-data-sources/blob-add-container.png "Criar um contentor de blobs")

1. Depois de criado o recipiente, abra-o e selecione **Upload** na barra de comando. Navegue na pasta que contém os ficheiros da amostra. Selecione todos e, em seguida, clique em **Carregar**.

   ![Carregar ficheiros](media/tutorial-multiple-data-sources/blob-upload.png "Carregar ficheiros")

Após o upload completo, os ficheiros devem aparecer na lista para o recipiente de dados.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O terceiro componente é a Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o free tier para completar esta passagem. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma chave de api-key e URL para pesquisa cognitiva azure

Para interagir com o seu serviço de Pesquisa Cognitiva Azure vai precisar do URL de serviço e de uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   Pegue a chave de consulta também. É uma boa prática emitir pedidos de consulta com acesso só para leitura.

   ![Obtenha o nome de serviço e as chaves de administração e consulta](media/search-get-started-nodejs/service-name-and-keys.png)

Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="2---set-up-your-environment"></a>2 - Instale o seu ambiente

1. Inicie o Visual Studio 2019 e no menu **Tools,** selecione **NuGet Package Manager** e, em seguida, **gere pacotes NuGet para solução...** . 

1. No separador **Browse,** encontre e instale o **Microsoft.Azure.Search** (versão 9.0.1, ou mais tarde). Terá de clicar em diálogos adicionais para completar a instalação.

    ![Usando o NuGet para adicionar bibliotecas Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Procure o pacote **Microsoft.Extensions.Configuration.Json** NuGet e instale-o também.

1. Abra o ficheiro de solução **AzureSearchMultipleDataSources.sln**.

1. No Solution Explorer, edite o ficheiro **appsettings.json** para adicionar informações de ligação.  

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

As duas primeiras entradas utilizam as teclas URL e administração para o seu serviço de Pesquisa Cognitiva Azure. Tendo em conta um ponto final de `https://mydemo.search.windows.net`, por exemplo, o nome de serviço a fornecer é `mydemo`.

As próximas entradas especificam nomes de conta e informações de cordas de ligação para as fontes de dados do Azure Blob Storage e Azure Cosmos DB.

## <a name="3---map-key-fields"></a>3 - Mapear campos-chave

A fusão do conteúdo requer que ambos os fluxos de dados estejam direcionados para os mesmos documentos no índice de pesquisa. 

Na Pesquisa Cognitiva Azure, o campo-chave identifica exclusivamente cada documento. Cada índice de pesquisa deve ter exatamente um campo-chave de `Edm.String`. Este campo-chave deve estar presente para cada documento numa fonte de dados adicionada ao índice. (Na verdade, é o único campo necessário.)

Ao indexar dados de várias fontes de dados, certifique-se de que cada linha ou documento contém uma chave de documento comum para fundir dados de dois documentos de origem fisicamente distintos num novo documento de pesquisa no índice combinado. 

Muitas vezes requer algum planeamento frontal para identificar uma chave de documento significativa para o seu índice, e certificar-se de que existe em ambas as fontes de dados. Nesta demonstração, a chave `HotelId` para cada hotel em Cosmos DB também está presente nos quartos JSON blobs no armazenamento Blob.

Os indexadores de pesquisa cognitiva azure podem usar mapeamentos de campo para mudar o nome e até mesmo reformar os campos de dados durante o processo de indexação, de modo que os dados de origem podem ser direcionados para o campo de índice correto. Por exemplo, em Cosmos DB, o identificador do hotel chama-se **`HotelId`** . Mas nos ficheiros da JSON para os quartos de hotel, o identificador do hotel **chama-se`Id`.** O programa lida com isto mapeando o campo **`Id`** desde as bolhas até ao **campo-chave`HotelId`** no índice.

> [!NOTE]
> Na maioria dos casos, as chaves de documento geradas automaticamente, como as criadas por padrão por alguns indexantes, não fazem boas chaves de documento para índices combinados. Em geral, você vai querer usar um valor-chave significativo e único que já existe, ou pode ser facilmente adicionado às suas fontes de dados.

## <a name="4---explore-the-code"></a>4 - Explore o código

Uma vez que os dados e configurações de configuração estejam em vigor, o programa de amostras em **AzureSearchMultipleDataSources.sln** deve estar pronto para construir e executar.

Esta C#aplicação de consola simples /.NET executa as seguintes tarefas:

* Cria um novo índice com base C# na estrutura de dados da classe Hotel (que também faz referência às aulas de Endereço e Quarto).
* Cria uma nova fonte de dados e um indexante que mapeia dados da Azure Cosmos DB para campos indexados. Estes são ambos objetos na Pesquisa Cognitiva Azure.
* Executa o indexante para carregar os dados do Hotel da Cosmos DB.
* Cria uma segunda fonte de dados e um indexante que mapeia os dados da bolha jSON para os campos de índice.
* Executa o segundo indexante para carregar os dados do Quarto do armazenamento blob.

 Antes de executar o programa, dedique um minuto para estudar o código e as definições indexadas e indexantes para esta amostra. O código relevante está em dois ficheiros:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criam o índice de pesquisa cognitiva Azure, fontes de dados e indexadores, e carregam os resultados combinados no índice.

### <a name="create-an-index"></a>Criar um índice

Este programa de amostras utiliza o SDK .NET para definir e criar um índice de Pesquisa Cognitiva Azure. Aproveita a classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma C# estrutura de índice a partir de uma classe de modelo de dados.

O modelo de dados é definido pela classe Hotel, que também contém referências às aulas de Endereço e Quarto. O FieldBuilder perfura através de várias definições de classe para gerar uma estrutura de dados complexa para o índice. As etiquetas de metadados são usadas para definir os atributos de cada campo, tais como se é pesquisável ou classificativa.

Os seguintes excertos do ficheiro **Hotel.cs** mostram como um único campo, e uma referência a outra classe de modelo de dados, podem ser especificados.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

No ficheiro **Program.cs,** o índice é definido com um nome e uma coleção de campo gerada pelo método `FieldBuilder.BuildForType<Hotel>()`, e depois criado da seguinte forma:

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

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar fonte de dados e indexador de dados Da Azure Cosmos DB

Em seguida, o programa principal inclui lógica para criar a fonte de dados do Azure Cosmos DB para os dados dos hotéis.

Primeiro, concatena o nome da base de dados Azure Cosmos DB à cadeia de ligação. Em seguida, define o objeto de origem de dados, incluindo configurações específicas para fontes de DB Do Azure Cosmos, como a propriedade [useChangeDetection].

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

Após a criação da fonte de dados, o programa configura um indexador Azure Cosmos DB chamado **hotel-rooms-cosmos indexer**.

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

Este exemplo define um calendário para o indexante, para que seja executado uma vez por dia. Pode remover a propriedade de horário desta chamada se não quiser que o indexante volte a funcionar automaticamente no futuro.

### <a name="index-azure-cosmos-db-data"></a>Dados do Index Azure Cosmos DB

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

Este exemplo inclui um simples bloco de tentativas para relatar quaisquer erros que possam ocorrer durante a execução.

Depois de o indexador Azure Cosmos DB ter funcionado, o índice de pesquisa conterá um conjunto completo de documentos do hotel de amostra. No entanto, o campo de quartos para cada hotel será uma matriz vazia, uma vez que a fonte de dados do Azure Cosmos DB não continha detalhes de quartos. Em seguida, o programa irá retirar do armazenamento Blob para carregar e fundir os dados da sala.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar fonte de dados de armazenamento blob e indexador

Para obter os detalhes da sala, o programa configura primeiro uma fonte de dados de armazenamento Blob para fazer referência a um conjunto de ficheiros de blob JSON individuais.

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

Após a criação da fonte de dados, o programa configura um indexador blob chamado **hotel-rooms-blob indexer**.

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

As bolhas JSON contêm um campo-chave chamado **`Id`** em vez de **`HotelId`** . O código usa a classe `FieldMapping` para dizer ao indexante para direcionar o valor de campo **`Id`** para a chave de documento **`HotelId`** no índice.

Os indexadores de armazenamento blob podem usar parâmetros que identifiquem o modo de análise a utilizar. O modo de análise difere para bolhas que representam um único documento, ou múltiplos documentos dentro da mesma bolha. Neste exemplo, cada bolha representa um único documento de índice, pelo que o código utiliza o parâmetro `IndexingParameters.ParseJson()`.

Para obter mais informações sobre os parâmetros de análise do indexante para as bolhas JSON, consulte [as bolhas Index JSON](search-howto-index-json-blobs.md). Para obter mais informações sobre a especificação destes parâmetros utilizando o .NET SDK, consulte a classe [IndexerParametersExtension.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions)

O programa eliminará quaisquer indexantes existentes com o mesmo nome antes de criar o novo, caso pretenda executar este exemplo mais de uma vez.

Este exemplo define um calendário para o indexante, para que seja executado uma vez por dia. Pode remover a propriedade de horário desta chamada se não quiser que o indexante volte a funcionar automaticamente no futuro.

### <a name="index-blob-data"></a>Dados de bolha de índice

Uma vez criada a fonte de dados de armazenamento blob e o indexante, o código que executa o indexante é simples:

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

Como o índice já foi povoado com dados do hotel da base de dados Do BD Azure Cosmos, o indexante blob atualiza os documentos existentes no índice e adiciona os detalhes da sala.

> [!NOTE]
> Se tiver os mesmos campos não-chave em ambas as suas fontes de dados, e os dados dentro desses campos não corresponderem, então o índice conterá os valores de qualquer indexante que tenha sido recentemente. No nosso exemplo, ambas as fontes de dados contêm um campo **HotelName.** Se por alguma razão os dados neste campo forem diferentes, para documentos com o mesmo valor-chave, então os dados do **HotelName** da fonte de dados que foi indexado mais recentemente serão o valor armazenado no índice.

## <a name="5---search"></a>5 - Pesquisa

Pode explorar o índice de pesquisa preenchido depois de o programa ter sido executado, utilizando o explorador de [**pesquisa**](search-explorer.md) no portal.

No portal Azure, abra a página de **visão geral** do serviço de pesquisa e encontre o índice de **amostras de quartos de hotel** na lista de **Índices.**

  ![Lista de índices de pesquisa cognitiva azure](media/tutorial-multiple-data-sources/index-list.png "Lista de índices de pesquisa cognitiva azure")

Clique no índice de amostras de quartos de hotel na lista. Verá uma interface do Search Explorer para o índice. Insira uma consulta para um termo como "Luxo". Você deve ver pelo menos um documento nos resultados, e este documento deve mostrar uma lista de objetos de quarto na sua matriz de quartos.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração do design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra para este tutorial verifica os objetos existentes e elimina-os para que possa reexecutar o seu código.

Também pode usar o portal para eliminar índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação De Todos os recursos ou grupos de Recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o conceito de ingerir dados de várias fontes, vamos olhar mais de perto para a configuração do indexador, começando pelo Cosmos DB.

> [!div class="nextstepaction"]
> [Configure um indexador Azure Cosmos DB](search-howto-index-cosmosdb.md)