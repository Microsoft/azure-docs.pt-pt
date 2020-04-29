---
title: 'Tutorial: Dados indexados das bases de dados Azure SQL em C # '
titleSuffix: Azure Cognitive Search
description: Neste tutorial C#, ligue-se à base de dados Azure SQL, extrai dados pesquisáveis e carregue-os num índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/28/2020
ms.openlocfilehash: 7660c89032ea3ef8371655b94b75c1f60603ee32
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78193973"
---
# <a name="tutorial-use-c-to-index-data-from-sql-databases-in-azure-cognitive-search"></a>Tutorial: Use C# para indexar dados das bases de dados SQL em Pesquisa Cognitiva Azure

Configure um [indexante](search-indexer-overview.md) para extrair dados pesquisáveis da base de dados Azure SQL, enviando-os para um índice de pesquisa em Pesquisa Cognitiva Azure. 

Este tutorial utiliza C# e o [.NET SDK](https://aka.ms/search-sdk) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fonte de dados que se conecta à Base de Dados Azure SQL
> * Criar um indexador
> * Executar um indexante para carregar dados em um índice
> * Consulta de um índice como passo de verificação

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa existente 

> [!Note]
> Pode utilizar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de que tem espaço ao seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Transferir ficheiros

O código fonte deste tutorial está na pasta [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) no repositório GitHub [azure-samples/search-dotnet-getting-start.](https://github.com/Azure-Samples/search-dotnet-getting-started)

## <a name="1---create-services"></a>1 - Criar serviços

Este tutorial utiliza a Pesquisa Cognitiva Azure para indexação e consultas, e a Base de Dados Azure SQL como fonte de dados externa. Se possível, crie tanto serviços na mesma região como grupo de recursos para proximidade e gestão. Na prática, a Base de Dados Azure SQL pode estar em qualquer região.

### <a name="start-with-azure-sql-database"></a>Comece com base de dados Azure SQL

Neste passo, crie uma fonte de dados externa na Base de Dados Azure SQL que um indexante possa rastejar. Você pode usar o portal Azure e o ficheiro *hotels.sql* a partir do download da amostra para criar o conjunto de dados na Base de Dados Azure SQL. A Pesquisa Cognitiva Azure consome rowsets achatados, como um gerado a partir de uma vista ou consulta. O ficheiro SQL na solução de exemplo cria e preenche uma única tabela.

Se você tem um recurso de base de dados Azure SQL existente, você pode adicionar a tabela de hotéis a ele, começando no passo 4.

1. [Inscreva-se no portal Azure.](https://portal.azure.com/)

1. Encontre ou crie uma Base de **Dados SQL**. Pode utilizar as predefinições e o nível mais baixo do escalão de preço. Uma vantagem da criação de um servidor é que pode especificar um nome de utilizador e uma palavra-passe de administrador, que são necessários para criar e carregar tabelas num passo posterior.

   ![Página Nova base de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png "Página Nova base de dados")

1. Clique em **Rever + criar** para implementar o novo servidor e base de dados. Aguarde que o servidor e a base de dados sejam implementados.

1. No painel de navegação, clique no editor da **Consulta (pré-visualização)** e introduza o nome de utilizador e a palavra-passe do administrador do servidor. 

   Se o acesso for negado, copie o endereço IP do cliente a partir da mensagem de erro e, em seguida, clique no link de firewall do **servidor set** para adicionar uma regra que permite o acesso a partir do seu computador cliente, utilizando o IP do seu cliente para o intervalo. Pode levar vários minutos para a regra fazer efeito.

1. No editor da Consulta, clique em **Abrir a consulta** e navegue até à localização do ficheiro *hotels.sql* no seu computador local. 

1. Selecione o ficheiro e clique em **Abrir**. O script deverá ser parecido com o da captura de ecrã seguinte:

   ![Script de SQL](./media/search-indexer-tutorial/sql-script.png "Script de SQL")

1. Clique em **Executar** para executar a consulta. No painel Resultados, deverá ver uma mensagem de consulta bem-sucedida para três linhas.

1. Para devolver um conjunto de linhas a partir desta tabela, pode executar a consulta seguinte como passo de verificação:

    ```sql
    SELECT * FROM Hotels
    ```

1. Copie a cadeia de ligação ADO.NET para a base de dados. Em **Configurações,** > **Connection Strings**copie a cadeia de ligação ADO.NET, semelhante ao exemplo abaixo.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Você precisará desta corda de ligação no próximo exercício, configurando o seu ambiente.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

O próximo componente é a Pesquisa Cognitiva Azure, que pode [criar no portal.](search-create-service-portal.md) Pode utilizar o free tier para completar esta passagem. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obtenha uma chave de api-key e URL para pesquisa cognitiva azure

As chamadas API requerem o URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Keys,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   ![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

## <a name="2---set-up-your-environment"></a>2 - Instale o seu ambiente

1. Inicie o Estúdio Visual e abra **o DotNetHowToIndexers.sln**.

1. No Solution Explorer, abra **as definições.json** para fornecer informações de ligação.

1. Pois, `searchServiceName`se o URLhttps://my-demo-service.search.windows.netcompleto for " ", o nome de serviço a fornecer é "my-demo-service".

1. Para, `AzureSqlConnectionString`o formato de cordas é semelhante a este:`"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Na cadeia de ligação, certifique-se de que a cadeia de ligação contém uma palavra-passe válida. Enquanto a base de dados e os nomes dos utilizadores forem impressos, a palavra-passe deve ser inserida manualmente.

## <a name="3---create-the-pipeline"></a>3 - Criar o gasoduto

Os indexadores requerem um objeto de origem de dados e um índice. O código relevante está em dois ficheiros:

  + **hotel.cs,** contendo um esquema que define o índice
  + **Program.cs,** contendo funções para a criação e gestão de estruturas no seu serviço

### <a name="in-hotelcs"></a>Em hotel.cs

O esquema do índice define a coleção de campos, incluindo atributos que especificam as operações permitidas, como, por exemplo, se um determinado campo é de pesquisa em texto completo, filtrável ou ordenável, conforme mostrado na definição de campo abaixo para HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Os esquemas também podem incluir outros elementos, como perfis de classificação para otimizar as classificações de pesquisas, analisadores personalizados e outros construtores. No entanto, para a nossa finalidade, o esquema é definido de forma escassa, consistindo apenas em campos encontrados nos conjuntos de dados de exemplo.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui lógica para criar um cliente, um índice, uma fonte de dados e um indexante. Verifica e elimina os recursos existentes com o mesmo nome, no pressuposto de que poderá executar este programa várias vezes.

O objeto de origem de dados é configurado com configurações específicas para os recursos da base de dados Azure SQL, incluindo [indexação parcial ou incremental](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) para alavancar as funcionalidades de deteção de [alterações incorporadas](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) do Azure SQL. A base de dados de hotéis de demonstração em Azure SQL tem uma coluna de "soft delete" chamada **IsDeleted**. Quando esta coluna é definida como verdadeira na base de dados, o indexante remove o documento correspondente do índice de pesquisa cognitiva Azure.

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

Um objeto indexante é agnóstico da plataforma, onde a configuração, o agendamento e a invocação são os mesmos independentemente da fonte. Este indexante de exemplo inclui um calendário, uma opção de reset que limpa o histórico do indexante, e chama um método para criar e executar o indexante imediatamente.

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

## <a name="4---build-the-solution"></a>4 - Construir a solução

Pressione F5 para construir e executar a solução. O programa é executado no modo de depuração. O estado de cada operação é reportado numa janela da consola.

   ![Saída da consola](./media/search-indexer-tutorial/console-output.png "Saída da consola")

O seu código funciona localmente no Estúdio Visual, conectando-se ao seu serviço de pesquisa no Azure, que por sua vez se conecta à Base de Dados Azure SQL e recupera o conjunto de dados. Com tantas operações, existem vários pontos potenciais de falha. Se tiver um erro, verifique primeiro as seguintes condições:

+ As informações de ligação do serviço de pesquisa que indicar estão limitadas ao nome do serviço deste tutorial. Se tiver introduzido o URL completo, as operações param na criação do índice, com um erro de falha ao ligar.

+ A informação de ligação da base de dados em **appsettings.json**. Deve ser a cadeia de ligação ADO.NET obtida no portal e modificada para incluir um nome de utilizador e uma palavra-passe válidas na sua base de dados. A conta de utilizador tem de ter permissão para obter dados. O endereço IP do seu cliente local deve ter acesso.

+ Limites dos recursos. Recorde-se que o free tier tem limites de 3 índices, indexadores e fontes de dados. Um serviço que esteja no limite máximo não pode criar objetos novos.

## <a name="5---search"></a>5 - Pesquisa

Utilize o portal Azure para verificar a criação de objetos e, em seguida, use o explorador de **pesquisa** para consultar o índice.

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, abra cada lista por sua vez para verificar se o objeto é criado. **Índices,** **Indexadores**e **Fontes** de Dados terão "hotéis", "indexador azul-sql", e "azure-sql", respectivamente.

   ![Mosaicos de indexador e origem de dados](./media/search-indexer-tutorial/tiles-portal.png)

1. Selecione o índice de hotéis. Na página dos hotéis, o explorador de **pesquisa** é o primeiro separador. 

1. Clique em **Procurar** para emitir uma consulta vazia. 

   As três entradas no índice são devolvidas como documentos JSON. O Explorador de Pesquisa devolve documentos em JSON, para que possa ver a estrutura inteira.

   ![Consulta de um índice](./media/search-indexer-tutorial/portal-search.png "Consulta de um índice")
   
1. Em seguida, introduza uma cadeia de pesquisa: `search=river&$count=true`. 

   Esta consulta invoca a pesquisa de texto completo para o termo `river` e o resultado inclui um contagem dos documentos correspondentes. Devolver a contagem de documentos correspondentes é útil em cenários de teste, em que tem um índice grande com milhares ou milhões de documentos. Neste caso, apenas um documento corresponde à consulta.

1. Por último, introduza uma cadeia de pesquisa que limite a saída JSON para campos de interesse: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A resposta da consulta é reduzida aos campos selecionados, resultando numa saída mais concisa.

## <a name="reset-and-rerun"></a>Repor e executar novamente

Nas fases experimentais iniciais de desenvolvimento, a abordagem mais prática para a iteração do design é apagar os objetos da Pesquisa Cognitiva Azure e permitir que o seu código os reconstrua. Os nomes dos recursos são exclusivos. Quando elimina um objeto, pode recriá-lo com o mesmo nome.

O código de amostra para este tutorial verifica os objetos existentes e elimina-os para que possa reexecutar o seu código.

Também pode usar o portal para eliminar índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação De Todos os recursos ou grupos de Recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Passos seguintes

Agora que está familiarizado com o básico da indexação da Base de Dados SQL, vamos ver mais de perto a configuração do indexante.

> [!div class="nextstepaction"]
> [Configure um indexador de base de dados Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)