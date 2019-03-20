---
title: Tutorial para indexação de bases de dados SQL do Azure no portal do Azure - Azure Search
description: Neste tutorial, ligar à base de dados SQL do Azure, em seguida, extrair dados pesquisáveis e carregá-los para um índice da Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4e94f4c1b5de47e36dd9a5be6b9e7f43d264de82
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201403"
---
# <a name="tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Tutorial: Pesquise a uma base de dados SQL do Azure com indexadores do Azure Search

Saiba como configurar um indexador para extrair dados pesquisáveis a partir de uma base de dados do SQL do Azure de exemplo. Os [indexadores](search-indexer-overview.md) são um componente do Azure Search que pesquisam origens de dados externas e preenchem um [índice de pesquisa](search-what-is-an-index.md) com conteúdos. De todos os indexadores, o indexador da base de dados do Azure SQL é o mais amplamente utilizadas. 

É útil dominar a configuração de indexadores, porque simplifica a quantidade de código que tem de escrever e manter. Em vez de preparar e enviar um conjunto de dados JSON compatível com esquema, pode anexar um indexador a uma origem de dados, fazer com que este extraia dados e inseri-los num índice e, opcionalmente, executar esse índice numa agenda periódica para recolher as alterações na origem subjacente.

Neste tutorial, utilize o [bibliotecas de cliente .NET do Azure Search](https://aka.ms/search-sdk) e uma aplicação de consola .NET Core para executar as seguintes tarefas:

> [!div class="checklist"]
> * Adicionar informações do serviço de pesquisa para as definições da aplicação
> * Preparar um conjunto de dados externo na base de dados SQL do Azure 
> * Rever o índice e as definições do indexador no código de exemplo
> * Executar o código do indexador para importar dados
> * Pesquisar o índice
> * Ver a configuração do indexador no portal

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Criar um serviço Azure Search](search-create-service-portal.md) ou [localizar um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na subscrição atual. Pode usar um serviço gratuito para este tutorial.

* Uma [base de dados do Azure SQL](https://azure.microsoft.com/services/sql-database/) fornecendo a origem de dados externa utilizada por um indexador. A solução de exemplo fornece um ficheiro de dados do SQL para criar a tabela.

* + [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. Código de exemplo e instruções foram testadas na edição de Comunidade gratuita.

> [!Note]
> Se estiver a utilizar o serviço do Azure Search gratuito, está limitado a três índices, três indexadores e três origens de dados. Este tutorial cria um de cada. Confirme que o seu serviço tem espaço para aceitar os recursos novos.

### <a name="download-the-solution"></a>Transferir a solução

A solução de indexador utilizada neste tutorial provém de uma coleção de exemplos do Azure Search fornecidos numa transferência principal. A solução utilizada para este tutorial é *DotNetHowToIndexers*.

1. Aceda a [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started), no repositório do GitHub de exemplos do Azure.

2. Clique em **Clone or Download**(Clonar ou Transferir) > **Download ZIP** (Transferir ZIP). Por predefinição, o ficheiro vai para a pasta Transferências.

3. No **Explorador de Ficheiros** > **Transferências**, clique com o botão direito do rato e escolha **Extrair tudo**.

4. Desative as permissões só de leitura. Faça duplo clique no nome da pasta > **Propriedades** > **Geral** e limpe o atributo **Só de leitura** para a pasta atual, as sub-pastas e os ficheiros.

5. No **Visual Studio 2017**, abra a solução *DotNetHowToIndexers.sln*.

6. No **Explorador de Soluções**, clique com o botão direito do rato na Solução principal do nó superior > **Restaurar Pacotes Nuget**.

### <a name="set-up-connections"></a>Configurar ligações
As informações de ligação dos serviços necessários estão especificadas no ficheiro **appsettings.json**, na solução. 

No Explorador de Soluções, abra **appsettings.json**, para poder preencher cada definição com as instruções deste tutorial.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

### <a name="get-the-search-service-name-and-admin-api-key"></a>Obter o nome do serviço de pesquisa e a api-chave de administrador

Pode encontrar o ponto final do serviço de pesquisa e a chave no portal. A chave dá acesso às operações do serviço. As chaves de administrador permitem acesso de escrita no serviço, que é necessário para criar e eliminar objetos, como índices e indexadores,

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e localize [serviços de pesquisa para a sua subscrição](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. Abra a página do serviço.

3. Na parte superior, localize o nome do serviço na página principal. Na captura de ecrã seguinte, é *azs-tutorial*.

   ![Nome do Serviço](./media/search-indexer-tutorial/service-name.png)

4. Copie e cole-o como a primeiro entrada em **appsettings.json** no Visual Studio.

   > [!Note]
   > Os nomes de serviço fazem parte do ponto final que inclui search.windows.net. Se tiver curiosidade, pode ver o URL completo em **Essentials**, na página Descrição Geral. O URL tem um aspeto semelhante a este exemplo: https://your-service-name.search.windows.net

5. À esquerda, em **Definições** > **Chaves**, copie uma das chaves de administrador e cole-a como a segunda entrada em **appsettings.json**. As chaves são cadeias alfanuméricas geradas para o seu serviço durante o aprovisionamento e são necessárias para acesso autorizado para operações do mesmo. 

   Depois de adicionar ambas as definições, o aspeto do seu ficheiro deverá ser parecido com:

   ```json
   {
    "SearchServiceName": "azs-tutorial",
    "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
    . . .
   }
   ```

## <a name="prepare-sample-data"></a>Preparar dados de exemplo

Neste passo, crie uma origem de dados externa que possa ser pesquisa por um indexador. O ficheiro de dados deste tutorial é *hotels.sql*, que está na pasta da solução \DotNetHowToIndexers. 

### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

Pode utilizar o portal do Azure e o ficheiro *hotels.sql* do exemplo para criar o conjunto de dados na Base de Dados SQL do Azure. O Azure Search consome conjuntos de linhas bidimensional, como os que são gerados a partir de vistas ou consultas. O ficheiro SQL na solução de exemplo cria e preenche uma única tabela.

O exercício seguinte pressupõe que não existe nenhum servidor o base de dados e diz-lhe para criá-los ambos no passo 2. Opcionalmente, se tiver um recurso já existente, pode adicionar a tabela “hotels”, começando no passo 4.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). 

2. Clique em **Criar um recurso** > **Base de Dados SQL** para criar uma base de dados, um servidor e um grupo de recursos. Pode utilizar as predefinições e o nível mais baixo do escalão de preço. Uma vantagem da criação de um servidor é que pode especificar um nome de utilizador e uma palavra-passe de administrador, que são necessários para criar e carregar tabelas num passo posterior.

   ![Página Nova base de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Clique em **Criar** para implementar o servidor e a base de dados novos. Aguarde que o servidor e a base de dados sejam implementados.

4. Abra a página Base de Dados SQL da base de dados nova, se ainda não estiver aberta. O nome do recurso deverá indicar *Base de dados SQL* e não *SQL Server*.

   ![Página Base de dados SQL](./media/search-indexer-tutorial/hotels-db.png)

4. Na barra de comandos, clique em **Ferramentas** > **Editor de consultas**.

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
      "SearchServiceName": "azs-tutorial",
      "SearchServiceAdminApiKey": "A1B2C3D4E5F6G7H8I9J10K11L12M13N14",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-index-and-indexer-code"></a>Compreender o código de índice e indexador

O seu código está agora pronto para ser criado e executado. Antes de o fazer, dispense uns minutos para estudar as definições do índice e do indexador deste exemplo. O código relevante está em dois ficheiros:

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

Neste tutorial, o indexador extrai dados de uma origem de dados. Na prática, pode anexar vários indexadores ao mesmo índice, o que cria um índice pesquisável consolidado a partir de várias origens de dados e indexadores. Pode utilizar o mesmo par indexador-índice, variando apenas as origens de dados, ou um índice com combinações de vários indexadores e origens de dados, dependendo da flexibilidade de que precisar.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui funções para as três origens de dados representativas. Olhando apenas para a Base de Dados SQL do Azure, destacam-se os objetos seguintes:

  ```csharp
  private const string IndexName = "hotels";
  private const string AzureSqlHighWaterMarkColumnName = "RowVersion";
  private const string AzureSqlDataSourceName = "azure-sql";
  private const string AzureSqlIndexerName = "azure-sql-indexer";
  ```

No Azure Search, os objetos que pode ver, configurar ou eliminar de forma independente incluem índices, indexadores e origens de dados (*hotels*, *azure-sql-indexer*, *azure-sql*, respetivamente). 

A coluna *AzureSqlHighWaterMarkColumnName* merece uma menção especial porque disponibiliza informações de deteção de alterações, que o indexador utiliza para determinar se uma determinada linha foi alterada desde a última carga de trabalho de indexação. As [políticas de deteção de alterações](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) só são suportadas nos indexadores e variam consoante a origem de dados. Na Base de Dados SQL do Azure, pode escolher de entre duas políticas, dependendo dos requisitos das bases de dados.

O código seguinte mostra os métodos em Program.cs utilizados para criar uma origem de dados e um indexador. Verifica e elimina os recursos existentes com o mesmo nome, no pressuposto de que poderá executar este programa várias vezes.

  ```csharp
  private static string SetupAzureSqlIndexer(SearchServiceClient serviceClient, IConfigurationRoot configuration)
  {
    Console.WriteLine("Deleting Azure SQL data source if it exists...");
    DeleteDataSourceIfExists(serviceClient, AzureSqlDataSourceName);

    Console.WriteLine("Creating Azure SQL data source...");
    DataSource azureSqlDataSource = CreateAzureSqlDataSource(serviceClient, configuration);

    Console.WriteLine("Deleting Azure SQL indexer if it exists...");
    DeleteIndexerIfExists(serviceClient, AzureSqlIndexerName);

    Console.WriteLine("Creating Azure SQL indexer...");
    Indexer azureSqlIndexer = CreateIndexer(serviceClient, AzureSqlDataSourceName, AzureSqlIndexerName);

    return azureSqlIndexer.Name;
  }
  ```

Tenha em atenção que as chamadas à API são agnósticas quanto à plataforma, exceto [DataSourceType](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet), que especifica o tipo de crawler a ser invocado.

## <a name="run-the-indexer"></a>Executar o indexador

Neste passo, compile e execute o programa. 

1. No Explorador de Soluções, faça duplo clique em **DotNetHowToIndexers** e selecione **Criar**.
2. Clique novamente em **DotNetHowToIndexers**, seguido de **Depurar** > **Iniciar instância nova**.

O programa é executado no modo de depuração. O estado de cada operação é reportado numa janela da consola.

  ![Script de SQL](./media/search-indexer-tutorial/console-output.png)

O código é executado localmente no Visual Studio e liga-se ao seu serviço de pesquisa no Azure, o qual, por sua vez, utiliza a cadeia de ligação para ligar à Base de Dados SQL do Azure e obter o conjunto de dados. Com tantas operações, existem diversos pontos de falha possíveis. Contudo, se receber um erro, veja as condições abaixo primeiro:

+ As informações de ligação do serviço de pesquisa que indicar estão limitadas ao nome do serviço deste tutorial. Se tiver introduzido o URL completo, as operações param na criação do índice, com um erro de falha ao ligar.

+ A informação de ligação da base de dados em **appsettings.json**. Deve ser a cadeia de ligação ADO.NET obtida no portal e modificada para incluir um nome de utilizador e uma palavra-passe válidas na sua base de dados. A conta de utilizador tem de ter permissão para obter dados.

+ Limites dos recursos. Recorde-se que o serviço partilhado (gratuito) tem como limite três índices, três indexadores e três origens de dados. Um serviço que esteja no limite máximo não pode criar objetos novos.

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

1. Abra a página Descrição Geral de serviço do seu serviço do Azure Search.
2. Desloque-se para baixo para localizar os mosaicos para **Indexadores** e **Origens de Dados**.
3. Clique num mosaico para abrir uma lista de cada recurso. Pode selecionar indexadores individuais ou origens de dados para ver ou modificar as definições de configuração.

   ![Mosaicos de indexador e origem de dados](./media/search-indexer-tutorial/tiles-portal.png)


## <a name="clean-up-resources"></a>Limpar recursos

A forma mais rápida de os limpar após o tutorial é eliminar o grupo de recursos que contém o serviço Azure Search. Pode eliminar o grupo de recursos agora para eliminar definitivamente tudo o que este contém. No portal, o nome do grupo de recursos está na página Descrição Geral do serviço Azure Search.

## <a name="next-steps"></a>Passos Seguintes

Pode anexar algoritmos com tecnologia de IA a um pipeline de indexador. Como próximo passo, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Indexar Documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)