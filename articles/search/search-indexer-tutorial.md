---
title: 'Tutorial: indexar dados C# de bancos de dado SQL do Azure'
titleSuffix: Azure Cognitive Search
description: Neste C# tutorial, conecte-se ao banco de dados SQL do Azure, extraia o dado pesquisável e carregue-o em um índice de pesquisa cognitiva do Azure.
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
# <a name="tutorial-import-azure-sql-database-in-c-using-azure-cognitive-search-indexers"></a>Tutorial: importar o banco de dados C# SQL do Azure usando indexadores de pesquisa cognitiva do Azure

Saiba como configurar um indexador para extração de dados pesquisáveis de um banco de dados SQL do Azure de exemplo. Os [indexadores](search-indexer-overview.md) são um componente do Azure pesquisa cognitiva que rastreiam fontes de dados externas, preenchendo um [índice de pesquisa](search-what-is-an-index.md) com conteúdo. De todos os indexadores, o indexador do banco de dados SQL do Azure é o mais amplamente usado. 

É útil dominar a configuração de indexadores, porque simplifica a quantidade de código que tem de escrever e manter. Em vez de preparar e enviar um conjunto de dados JSON compatível com esquema, pode anexar um indexador a uma origem de dados, fazer com que este extraia dados e inseri-los num índice e, opcionalmente, executar esse índice numa agenda periódica para recolher as alterações na origem subjacente.

Neste tutorial, use as [bibliotecas de cliente .net pesquisa cognitiva do Azure](https://aka.ms/search-sdk) e um aplicativo de console .NET Core para executar as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar informações do serviço de pesquisa para as definições da aplicação
> * Preparar um conjunto de dados externo na base de dados SQL do Azure 
> * Rever o índice e as definições do indexador no código de exemplo
> * Executar o código do indexador para importar dados
> * Pesquisar o índice
> * Ver a configuração do indexador no portal

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste guia de início rápido. 

[Crie um serviço de pesquisa cognitiva do Azure](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este tutorial.

O banco de dados [SQL do Azure](https://azure.microsoft.com/services/sql-database/) armazena a fonte externa usada por um indexador. A solução de exemplo fornece um ficheiro de dados do SQL para criar a tabela. As etapas para criar o serviço e o banco de dados são fornecidas neste tutorial.

O [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição, pode ser usado para executar a solução de exemplo. Exemplos de código e instruções foram testados na edição gratuita da Comunidade.

[Azure-Samples/Search-dotnet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) fornece a solução de exemplo, localizada no repositório GitHub de exemplos do Azure. Baixe e extraia a solução. Por padrão, as soluções são somente leitura. Clique com o botão direito do mouse na solução e limpe o atributo somente leitura para que você possa modificar os arquivos.

> [!Note]
> Se você estiver usando o serviço de Pesquisa Cognitiva do Azure gratuito, será limitado a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Confirme que o seu serviço tem espaço para aceitar os recursos novos.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas à API precisam do URL de serviço e de uma chave de acesso em todos os pedidos. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Pesquisa Cognitiva à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto final de exemplo poderá ser parecido com `https://mydemo.search.windows.net`.

1. Em **configurações** > **chaves**, obtenha uma chave de administração para obter direitos totais sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso você precise fazer uma sobreposição. Você pode usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações exigem uma chave de API em cada solicitação enviada ao seu serviço. Ter uma chave válida estabelece fidedignidade, numa base por pedido, entre a aplicação a enviar o pedido e o serviço que o processa.

## <a name="set-up-connections"></a>Configurar ligações
As informações de ligação dos serviços necessários estão especificadas no ficheiro **appsettings.json**, na solução. 

1. No Visual Studio, abra o arquivo **DotNetHowToIndexers. sln** .

1. No Gerenciador de Soluções, abra **appSettings. JSON** para que você possa preencher cada configuração.  

As duas primeiras entradas que você pode preencher agora, usando a URL e as chaves de administração para o serviço de Pesquisa Cognitiva do Azure. Dado um ponto de extremidade de `https://mydemo.search.windows.net`, o nome do serviço a ser fornecido é `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

A última entrada requer um banco de dados existente. Você o criará na próxima etapa.

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

Neste passo, crie uma origem de dados externa que possa ser pesquisa por um indexador. Pode utilizar o portal do Azure e o ficheiro *hotels.sql* do exemplo para criar o conjunto de dados na Base de Dados SQL do Azure. O Azure Pesquisa Cognitiva consome conjuntos de linhas bidimensionais, como um gerado a partir de uma exibição ou consulta. O ficheiro SQL na solução de exemplo cria e preenche uma única tabela.

O exercício seguinte pressupõe que não existe nenhum servidor o base de dados e diz-lhe para criá-los ambos no passo 2. Opcionalmente, se tiver um recurso já existente, pode adicionar a tabela “hotels”, começando no passo 4.

1. [Entre no portal do Azure](https://portal.azure.com/). 

2. Localize ou crie um **banco de dados SQL do Azure** para criar um banco de dados, um servidor e um grupo de recursos. Pode utilizar as predefinições e o nível mais baixo do escalão de preço. Uma vantagem da criação de um servidor é que pode especificar um nome de utilizador e uma palavra-passe de administrador, que são necessários para criar e carregar tabelas num passo posterior.

   ![Página Nova base de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Clique em **Criar** para implementar o servidor e a base de dados novos. Aguarde que o servidor e a base de dados sejam implementados.

4. Abra a página Base de Dados SQL da base de dados nova, se ainda não estiver aberta. O nome do recurso deverá indicar *Base de dados SQL* e não *SQL Server*.

   ![Página Base de dados SQL](./media/search-indexer-tutorial/hotels-db.png)

4. No painel de navegação, clique em **Editor de consultas (visualização)** .

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

Depois que os dados e as definições de configuração estiverem em vigor, o programa de exemplo em **DotNetHowToIndexers. sln** estará pronto para compilar e executar. Antes de o fazer, dispense uns minutos para estudar as definições do índice e do indexador deste exemplo. O código relevante está em dois ficheiros:

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

Neste tutorial, o indexador extrai dados de uma origem de dados. Na prática, você pode anexar vários indexadores ao mesmo índice, criando um índice pesquisável consolidado de várias fontes de dados. Pode utilizar o mesmo par indexador-índice, variando apenas as origens de dados, ou um índice com combinações de vários indexadores e origens de dados, dependendo da flexibilidade de que precisar.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui a lógica para criar um cliente, um índice, uma fonte de dados e um indexador. Verifica e elimina os recursos existentes com o mesmo nome, no pressuposto de que poderá executar este programa várias vezes.

O objeto de fonte de dados é configurado com configurações que são específicas para recursos do Azure SQL Database, incluindo a [indexação incremental](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) para aproveitar os recursos internos de [detecção de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) do SQL do Azure. O banco de dados de hotéis de demonstração no SQL do Azure tem uma coluna de "exclusão reversível" chamada **IsDeleted**. Quando essa coluna é definida como true no banco de dados, o indexador remove o documento correspondente do índice de Pesquisa Cognitiva do Azure.

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

Um objeto do indexador é independente da plataforma, onde a configuração, o agendamento e a invocação são os mesmos, independentemente da origem. Este indexador de exemplo inclui uma agenda, uma opção de redefinição que limpa o histórico do indexador e chama um método para criar e executar o indexador imediatamente.

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

+ Limites dos recursos. Lembre-se de que a camada gratuita tem limites de 3 índices, indexadores e fontes de dados. Um serviço que esteja no limite máximo não pode criar objetos novos.

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

1. [Entre no portal do Azure](https://portal.azure.com/)e, na página **visão geral** do serviço de pesquisa, clique nos links para **índices**, **indexadores**e **fontes de dados**.
3. Selecione objetos individuais para exibir ou modificar as definições de configuração.

   ![Mosaicos de indexador e origem de dados](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de limpar após um tutorial é excluindo o grupo de recursos que contém o serviço de Pesquisa Cognitiva do Azure. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página Visão geral do serviço de Pesquisa Cognitiva do Azure.

## <a name="next-steps"></a>Passos seguintes

Você pode anexar algoritmos de enriquecimento de AI a um pipeline do indexador. Como próximo passo, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Indexar Documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)