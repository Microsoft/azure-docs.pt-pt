---
title: 'Tutorial: Dados C# indexados das bases de dados Azure SQL'
titleSuffix: Azure Cognitive Search
description: Neste C# tutorial, ligue-se à base de dados Azure SQL, extrai dados pesquisáveis e carregue-os num índice de Pesquisa Cognitiva Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 978587b68e719b79db31ff25adaf2b38d2235095
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650097"
---
# <a name="tutorial-index-azure-sql-data-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: Dados Do Index Azure SQL na C# utilização de indexadores de pesquisa cognitiva Azure

Utilizando, C#configure um [indexante](search-indexer-overview.md) que extrai dados pesquisáveis da base de dados Azure SQL e envie-os para um índice de pesquisa. Este tutorial utiliza [bibliotecas de clientes Azure Cognitive Search .NET](https://aka.ms/search-sdk) e uma aplicação de consola .NET Core para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fonte de dados que se conecta à Base de Dados Azure SQL
> * Configure um indexante
> * Executar um indexante para carregar dados em um índice
> * Consulta de um índice como passo de verificação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Base de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) de pesquisa existente 

> [!Note]
> Pode utilizar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita-o a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, certifique-se de que tem espaço ao seu serviço para aceitar os novos recursos.

## <a name="download-source-code"></a>Descarregue código fonte

O código fonte deste tutorial está na pasta [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) no repositório GitHub [azure-samples/search-dotnet-getting-start.](https://github.com/Azure-Samples/search-dotnet-getting-started)

## <a name="get-a-key-and-url"></a>Obtenha uma chave e URL

As chamadas API requerem o URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, por isso, se você adicionar Pesquisa Cognitiva Azure à sua subscrição, siga estes passos para obter as informações necessárias:

1. [Inscreva-se no portal Azure](https://portal.azure.com/), e na página de **visão geral** do seu serviço de pesquisa, obtenha o URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **Definições** > **Teclas,** obtenha uma chave de administração para todos os direitos sobre o serviço. Existem duas chaves de administração intercambiáveis, previstas para a continuidade do negócio no caso de precisar de rolar uma. Pode utilizar a chave primária ou secundária nos pedidos de adição, modificação e aparas de objetos.

   ![Obtenha um ponto final http e chave de acesso](media/search-get-started-postman/get-url-key.png "Obtenha um ponto final http e chave de acesso")

## <a name="set-up-connections"></a>Configurar ligações

1. Inicie o Estúdio Visual e abra **o DotNetHowToIndexers.sln**.

1. No Solution Explorer, abra **as definições.json** e substitua os valores do espaço reservado por informações de ligação ao seu serviço de pesquisa. Se o URL completo for "https://my-demo-service.search.windows.net", o nome de serviço a fornecer é "my-demo-service".

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
    }
    ```

A última entrada requer uma base de dados existente. Vais criá-lo no próximo passo.

## <a name="prepare-sample-data"></a>Preparar dados da amostra

Neste passo, crie uma fonte de dados externa na Base de Dados Azure SQL que um indexante possa rastejar. Pode utilizar o portal do Azure e o ficheiro *hotels.sql* do exemplo para criar o conjunto de dados na Base de Dados SQL do Azure. A Pesquisa Cognitiva Azure consome rowsets achatados, como um gerado a partir de uma vista ou consulta. O ficheiro SQL na solução de exemplo cria e preenche uma única tabela.

Se você tem um recurso de base de dados Azure SQL existente, você pode adicionar a tabela de hotéis a ele, começando no passo 4.

1. [Inscreva-se no portal Azure.](https://portal.azure.com/)

1. Encontre ou crie uma Base de **Dados SQL**. Pode utilizar as predefinições e o nível mais baixo do escalão de preço. Uma vantagem da criação de um servidor é que pode especificar um nome de utilizador e uma palavra-passe de administrador, que são necessários para criar e carregar tabelas num passo posterior.

   ![Nova página de base de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png "Página Nova base de dados")

1. Clique em **Rever + criar** para implementar o novo servidor e base de dados. Aguarde que o servidor e a base de dados sejam implementados.

1. No painel de navegação, clique no editor da **Consulta (pré-visualização)** e introduza o nome de utilizador e a palavra-passe do administrador do servidor. 

   Se o acesso for negado, copie o endereço IP do cliente a partir da mensagem de erro e, em seguida, clique no link de firewall do **servidor set** para adicionar uma regra que permite o acesso a partir do seu computador cliente, utilizando o IP do seu cliente para o intervalo. Pode levar vários minutos para a regra fazer efeito.

1. No editor da Consulta, clique em **Abrir a consulta** e navegue até à localização do ficheiro *hotels.sql* no seu computador local. 

1. Selecione o ficheiro e clique em **Abrir**. O script deverá ser parecido com o da captura de ecrã seguinte:

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png "Script de SQL")

1. Clique em **Executar** para executar a consulta. No painel Resultados, deverá ver uma mensagem de consulta bem-sucedida para três linhas.

1. Para devolver um conjunto de linhas a partir desta tabela, pode executar a consulta seguinte como passo de verificação:

    ```sql
    SELECT * FROM Hotels
    ```

1. Copie a cadeia de ligação ADO.NET para a base de dados. Em **Definições** > **Cordas de Ligação,** copie a cadeia de ligação ADO.NET, semelhante ao exemplo abaixo.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

1. Cole a cadeia de ligação em "AzureSqlConnectionString" como a terceira entrada no ficheiro **appsettings.json**, no Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

1. Introduza a sua palavra-passe na cadeia de ligação no ficheiro **appsettings.json.** A base de dados e os nomes dos utilizadores serão impressos na sua cadeia de ligação, mas a palavra-passe deve ser inserida manualmente.

## <a name="build-the-solution"></a>Compilar a solução

Pressione F5 para construir a solução. O programa é executado no modo de depuração. O estado de cada operação é reportado numa janela da consola.

   ![Saída da consola](./media/search-indexer-tutorial/console-output.png "Saída da consola")

O seu código funciona localmente no Estúdio Visual, conectando-se ao seu serviço de pesquisa no Azure, que por sua vez se conecta à Base de Dados Azure SQL e recupera o conjunto de dados. Com tantas operações, existem vários pontos potenciais de falha. Se tiver um erro, verifique primeiro as seguintes condições:

+ As informações de ligação do serviço de pesquisa que indicar estão limitadas ao nome do serviço deste tutorial. Se tiver introduzido o URL completo, as operações param na criação do índice, com um erro de falha ao ligar.

+ A informação de ligação da base de dados em **appsettings.json**. Deve ser a cadeia de ligação ADO.NET obtida no portal e modificada para incluir um nome de utilizador e uma palavra-passe válidas na sua base de dados. A conta de utilizador tem de ter permissão para obter dados. O endereço IP do seu cliente local deve ter acesso.

+ Limites dos recursos. Recorde-se que o free tier tem limites de 3 índices, indexadores e fontes de dados. Um serviço que esteja no limite máximo não pode criar objetos novos.

## <a name="check-results"></a>Verificar resultados

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

## <a name="explore-the-code"></a>Explore o código

Agora que compreende o que o código de amostra cria, voltemos à solução para rever o código. O código relevante está em dois ficheiros:

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando se trabalha na sua própria subscrição, no final de um projeto, é uma boa ideia remover os recursos de que já não precisa. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação De Todos os recursos ou grupos de Recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se de que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para se manter abaixo do limite.

## <a name="next-steps"></a>Passos seguintes

Na Pesquisa Cognitiva Azure, os indexadores estão disponíveis para várias fontes de dados do Azure. Como próximo passo, explore o indexador para armazenamento De Blob Azure.

> [!div class="nextstepaction"]
> [Indexar Documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)