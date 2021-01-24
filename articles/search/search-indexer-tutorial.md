---
title: C# tutorial indexante dados Azure SQL
titleSuffix: Azure Cognitive Search
description: Neste tutorial C#, ligue-se à Base de Dados Azure SQL, extraia dados pesquisáveis e carregue-os num índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2ca5f42120661b887d07e697596f41cb7a7fce4
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745771"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Tutorial: Índice Azure SQL dados usando o .NET SDK

Configure um [indexante](search-indexer-overview.md) para extrair dados pesquisáveis da Base de Dados Azure SQL, enviando-os para um índice de pesquisa em Azure Cognitive Search. 

Este tutorial utiliza C# e o [.NET SDK](/dotnet/api/overview/azure/search) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fonte de dados que se conecta à Base de Dados Azure SQL
> * Criar um indexador
> * Executar um indexante para carregar dados em um índice
> * Consultar um índice como um passo de verificação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexantes e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de ter espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

O código fonte para este tutorial está na pasta [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) no repositório GitHub da [Azure-Samples/search-dotnet-start-](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub.

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial utiliza a Azure Cognitive Search para indexação e consultas, e a Base de Dados Azure SQL como uma fonte de dados externa. Se possível, crie ambos os serviços na mesma região e grupo de recursos para proximidade e gestão. Na prática, a Base de Dados Azure SQL pode estar em qualquer região.

### <a name="start-with-azure-sql-database"></a>Comece com a Base de Dados Azure SQL

Neste passo, crie uma fonte de dados externa na Base de Dados Azure SQL que um indexante pode rastejar. Você pode usar o portal Azure e os *hotéis.sql* ficheiro a partir do download da amostra para criar o conjunto de dados na Base de Dados Azure SQL. A Azure Cognitive Search consome linhas achatadas, como uma gerada a partir de uma vista ou consulta. O ficheiro SQL na solução de exemplo cria e preenche uma única tabela.

Se tiver um recurso de Base de Dados Azure SQL existente, pode adicionar-lhe a tabela de hotéis, a partir do passo 4.

1. [Inscreva-se no portal Azure](https://portal.azure.com/).

1. Encontre ou crie uma **Base de Dados SQL.** Pode utilizar as predefinições e o nível mais baixo do escalão de preço. Uma vantagem da criação de um servidor é que pode especificar um nome de utilizador e uma palavra-passe de administrador, que são necessários para criar e carregar tabelas num passo posterior.

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="Página Nova base de dados" border="false":::

1. Clique **em Rever + criar** para implementar o novo servidor e base de dados. Aguarde que o servidor e a base de dados sejam implementados.

1. No painel de navegação, clique no **editor de consulta (pré-visualização)** e introduza o nome de utilizador e a palavra-passe da administração do servidor. 

   Se o acesso for negado, copie o endereço IP do cliente a partir da mensagem de erro e, em seguida, clique no link **de firewall do servidor set** para adicionar uma regra que permite o acesso a partir do computador do seu cliente, utilizando o IP do seu cliente para o intervalo. Pode levar vários minutos para que a regra faça efeito.

1. Em Editor de Consulta, clique em **abrir a consulta** e navegar para a localização dos *hotéis.sql* ficheiro no seu computador local. 

1. Selecione o ficheiro e clique em **Abrir**. O script deverá ser parecido com o da captura de ecrã seguinte:

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="Script de SQL" border="false":::

1. Clique em **Executar** para executar a consulta. No painel Resultados, deverá ver uma mensagem de consulta bem-sucedida para três linhas.

1. Para devolver um conjunto de linhas a partir desta tabela, pode executar a consulta seguinte como passo de verificação:

    ```sql
    SELECT * FROM Hotels
    ```

1. Copie o fio de ligação ADO.NET para a base de dados. Em **Definições**  >  **Cadeias de Ligação**, copie a cadeia de ligação ADO.NET, semelhante ao exemplo abaixo.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Você precisará desta cadeia de ligação no próximo exercício, configurando o seu ambiente.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O próximo componente é a Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o nível Free para completar este walkthrough. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma api-chave de administrador e URL para pesquisa cognitiva Azure

As chamadas da API requerem o URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se adicionar Azure Cognitive Search à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure,](https://portal.azure.com/)e na página **geral do** seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições**  >  **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio, caso precise de rolar uma. Pode utilizar a tecla primária ou secundária nos pedidos de adição, modificação e eliminação de objetos.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obtenha uma chave de acesso http e acesso" border="false":::

## <a name="2---set-up-your-environment"></a>2 - Configurar o seu ambiente

1. Inicie o Visual Studio e abra **DotNetHowToIndexers.sln**.

1. No Solution Explorer, abra **appsettings.js** para fornecer informações de ligação.

1. Pois, `SearchServiceEndPoint` se o URL completo na página geral do serviço for " ", https://my-demo-service.search.windows.net então o valor a fornecer é esse URL.

1. Para `AzureSqlConnectionString` , o formato de corda é semelhante a este: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceEndPoint": "<placeholder-search-url>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-search-service>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Na cadeia de ligação, certifique-se de que a cadeia de ligação contém uma senha válida. Enquanto a base de dados e os nomes de utilizador copiarem, a palavra-passe deve ser inserida manualmente.

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Os indexantes requerem um objeto de origem de dados e um índice. O código relevante está em dois ficheiros:

  + **hotel.cs,** contendo um esquema que define o índice
  + **Program.cs,** contendo funções para criar e gerir estruturas no seu serviço

### <a name="in-hotelcs"></a>Em hotel.cs

O esquema do índice define a coleção de campos, incluindo atributos que especificam as operações permitidas, como, por exemplo, se um determinado campo é de pesquisa em texto completo, filtrável ou ordenável, conforme mostrado na definição de campo abaixo para HotelName. A [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) é pes pesmável por definição por texto completo. Outros atributos são atribuídos explicitamente.

```csharp
. . . 
[SearchableField(IsFilterable = true, IsSortable = true)]
[JsonPropertyName("hotelName")]
public string HotelName { get; set; }
. . .
```

Os esquemas também podem incluir outros elementos, como perfis de classificação para otimizar as classificações de pesquisas, analisadores personalizados e outros construtores. No entanto, para a nossa finalidade, o esquema é definido de forma escassa, consistindo apenas em campos encontrados nos conjuntos de dados de exemplo.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui lógica para criar [um cliente indexante,](/dotnet/api/azure.search.documents.indexes.models.searchindexer)um índice, uma fonte de dados e um indexante. Verifica e elimina os recursos existentes com o mesmo nome, no pressuposto de que poderá executar este programa várias vezes.

O objeto de origem de dados está configurado com configurações específicas dos recursos da Base de Dados Azure SQL, incluindo [indexação parcial ou incremental](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) para alavancar as funcionalidades de deteção de [alterações incorporadas](/sql/relational-databases/track-changes/about-change-tracking-sql-server) do Azure SQL. A base de dados de hotéis de demonstração de origem em Azure SQL tem uma coluna de "soft delete" chamada **IsDeleted**. Quando esta coluna é definida como verdadeira na base de dados, o indexante remove o documento correspondente do índice de Pesquisa Cognitiva Azure.

```csharp
Console.WriteLine("Creating data source...");

var dataSource =
      new SearchIndexerDataSourceConnection(
         "hotels-sql-ds",
         SearchIndexerDataSourceType.AzureSql,
         configuration["AzureSQLConnectionString"],
         new SearchIndexerDataContainer("hotels"));

indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
```

Um objeto indexante é agnóstico de plataforma, onde a configuração, o agendamento e a invocação são os mesmos independentemente da fonte. Este indexante de exemplo inclui um horário, uma opção de reset que limpa o histórico do indexante, e chama um método para criar e executar o indexante imediatamente. Para criar ou atualizar um indexante, utilize [CreateOrUpdateIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.createorupdateindexerasync).

```csharp
Console.WriteLine("Creating Azure SQL indexer...");

var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
      StartTime = DateTimeOffset.Now
};

var parameters = new IndexingParameters()
{
      BatchSize = 100,
      MaxFailedItems = 0,
      MaxFailedItemsPerBatch = 0
};

// Indexer declarations require a data source and search index.
// Common optional properties include a schedule, parameters, and field mappings
// The field mappings below are redundant due to how the Hotel class is defined, but 
// we included them anyway to show the syntax 
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
      Description = "Data indexer",
      Schedule = schedule,
      Parameters = parameters,
      FieldMappings =
      {
         new FieldMapping("_id") {TargetFieldName = "HotelId"},
         new FieldMapping("Amenities") {TargetFieldName = "Tags"}
      }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

As execuções do indexante são normalmente programadas, mas durante o desenvolvimento é melhor executar o indexante imediatamente utilizando [o RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync).

```csharp
Console.WriteLine("Running Azure SQL indexer...");

try
{
      await indexerClient.RunIndexerAsync(indexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

## <a name="4---build-the-solution"></a>4 - Construir a solução

Prima F5 para construir e executar a solução. O programa é executado no modo de depuração. O estado de cada operação é reportado numa janela da consola.

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="Saída da consola" border="false":::

O seu código é executado localmente no Visual Studio, conectando-se ao seu serviço de pesquisa no Azure, que por sua vez se conecta à Base de Dados Azure SQL e recupera o conjunto de dados. Com estas muitas operações, existem vários pontos potenciais de falha. Se tiver um erro, verifique primeiro as seguintes condições:

+ As informações de ligação do serviço de pesquisa que fornece são o URL completo. Se introduziu apenas o nome de serviço, as operações param na criação de índices, com uma falha na ligação do erro.

+ A informação de ligação da base de dados em **appsettings.json**. Deve ser a cadeia de ligação ADO.NET obtida no portal e modificada para incluir um nome de utilizador e uma palavra-passe válidas na sua base de dados. A conta de utilizador tem de ter permissão para obter dados. O endereço IP do seu cliente local deve ser autorizado a aceder através da firewall.

+ Limites dos recursos. Recorde-se que o nível Livre tem limites de 3 índices, indexadores e fontes de dados. Um serviço que esteja no limite máximo não pode criar objetos novos.

## <a name="5---search"></a>5 - Pesquisar

Use o portal Azure para verificar a criação de objetos e, em seguida, use **o explorador de pesquisa** para consultar o índice.

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página **geral do** seu serviço de pesquisa, abra cada lista por sua vez para verificar se o objeto foi criado. **Os índices**, **Indexantes** e **Fontes de Dados** terão "hotéis", "azure-sql-indexer", e "azure-sql", respectivamente.

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="Mosaicos de indexador e origem de dados" border="false":::

1. Selecione o índice de hotéis. Na página dos hotéis, **o Explorador de Pesquisa** é o primeiro separador. 

1. Clique **em Procurar** para emitir uma consulta vazia. 

   As três entradas no índice são devolvidas como documentos JSON. O Explorador de Pesquisa devolve documentos em JSON, para que possa ver a estrutura inteira.

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="Consulta de um índice" border="false":::
   
1. Em seguida, introduza uma cadeia de pesquisa: `search=river&$count=true`. 

   Esta consulta invoca a pesquisa de texto completo para o termo `river` e o resultado inclui um contagem dos documentos correspondentes. Devolver a contagem de documentos correspondentes é útil em cenários de teste, em que tem um índice grande com milhares ou milhões de documentos. Neste caso, apenas um documento corresponde à consulta.

1. Por último, introduza uma cadeia de pesquisa que limite a saída JSON para campos de interesse: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A resposta da consulta é reduzida aos campos selecionados, resultando numa saída mais concisa.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração de design é eliminar os objetos da Azure Cognitive Search e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra deste tutorial verifica os objetos existentes e elimina-os para que possa voltar a verificar o seu código.

Também pode utilizar o portal para eliminar índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando se está a trabalhar na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação de todos os recursos ou grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximos passos

Agora que está familiarizado com o básico da indexação da base de dados SQL, vamos olhar mais de perto para a configuração do indexante.

> [!div class="nextstepaction"]
> [Configurar um indexante de base de dados SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)