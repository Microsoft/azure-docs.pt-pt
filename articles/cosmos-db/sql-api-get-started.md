---
title: Criar uma aplicação de consola .NET para gerir dados na conta do Azure Cosmos DB SQL API
description: Saiba como criar recursos do Azure Cosmos DB SQL API com um C# aplicação de consola.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: kirankk
ms.openlocfilehash: 6fd7efe38aeb1f1094d240cf1675d432f3766229
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985728"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Criar uma aplicação de consola .NET para gerir dados na conta do Azure Cosmos DB SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Tutorial de introdução de bem-vindo ao obter a API de SQL do Azure Cosmos DB. Depois de seguir este tutorial, terá de uma aplicação de consola que cria e consulta recursos do Cosmos DB. Este tutorial utiliza [versão 3.0 +](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do Azure Cosmos DB .NET SDK, que pode ser direcionado aos [.NET Framework](https://dotnet.microsoft.com/download) ou [.NET Core](https://dotnet.microsoft.com/download).

Este tutorial aborda:

> [!div class="checklist"]
> * Criar e ligar a uma conta do Cosmos do Azure
> * Configurar o seu projeto no Visual Studio
> * Criar uma base de dados e um contentor
> * Adicionar itens ao contentor
> * Consulta o contentor
> * Operações CRUD no item
> * Eliminar a base de dados

Não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Avance para o [obter a seção de solução completa do tutorial](#GetSolution) para instruções rápidas.

Agora comecemos!

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passo 1: Criar uma conta do Azure Cosmos DB
Vamos criar uma conta do Azure Cosmos DB. Se já tiver uma conta que pretende utilizar, pode avançar diretamente para [Configurar a sua solução do Visual Studio](#SetupVS). Se estiver a utilizar o emulador do Azure Cosmos DB, siga os passos indicados em [emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e avance para [configurar seu projeto do Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Passo 2: Configurar o seu projeto do Visual Studio
1. Abra o **Visual Studio 2017** no seu computador.
1. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.
1. Na **novo projeto** caixa de diálogo, selecione **Visual C#**   /  **aplicação de consola (.NET Framework)** , nomeie o projeto e, em seguida, clique em **OK** .
    ![Captura de ecrã da janela novo projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Para .NET core destino, na **novo projeto** caixa de diálogo, selecione **Visual C#**   /  **aplicação de consola (.NET Core)** , nomeie o projeto e, em seguida, clique em  **OK**

1. No **Explorador de Soluções**, clique com o botão direito do rato na sua nova aplicação de consola, que está sob a sua solução Visual Studio e, em seguida, clique em **Gerir Pacotes NuGet...**

    ![Captura de ecrã do Menu com Botão do Lado Direito para o Projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Na **NuGet** separador, clique em **procurar**e o tipo **Microsoft.Azure.Cosmos** na caixa de pesquisa.
1. Nos resultados, localize **Microsoft.Azure.Cosmos** e clique em **instalar**.
   O ID do pacote para a Biblioteca de Cliente da API SQL do Azure Cosmos DB é [Biblioteca de Cliente do Microsoft Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Captura de ecrã do NuGet Menu para encontrar o SDK de cliente do Azure Cosmos DB](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Se obtiver uma mensagem sobre a revisão das alterações para a solução, clique em **OK**. Se obtiver uma mensagem sobre a aceitação de licença, clique em **Aceito**.

Ótimo! Agora que concluímos a configuração, comecemos a escrever certos códigos. Pode encontrar um projeto de código completo deste tutorial em [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Passo 3: Ligar a uma conta do Azure Cosmos DB
1. Em primeiro lugar, substitua as referências no início do seu C# aplicativo, o **Program.cs** ficheiro com essas referências:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Agora, adicione essas constantes e variáveis em sua classe pública ``Program``.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

    Tenha em atenção de que se estiver familiarizado com a versão anterior do SDK do .NET, poderá ser utilizado para ver os termos "coleção' e 'document'. Como o Azure Cosmos DB suporta vários modelos de API, versão 3.0 + do SDK do .NET usa os termos genéricos "contentor" e "item". Os contentores podem ser uma coleção, um grafo ou uma tabela. Os itens podem ser um documento, um vértice/aresta ou uma linha e são o conteúdo dentro dos contentores. [Saiba mais sobre as bases de dados, contentores e itens.](databases-containers-items.md)

1. Obter o URL de ponto final e a chave primária a partir da [portal do Azure](https://portal.azure.com).

    No portal do Azure, navegue até à sua conta do Azure Cosmos DB e, em seguida, clique em **Chaves**.

    Copie o URI a partir do portal e cole-a na `<your endpoint URL>` no ```Program.cs``` ficheiro. Copie a chave primária do portal e cole-o em `<your primary key>`.

   ![Captura de ecrã para obter as chaves do Azure Cosmos DB a partir do portal do Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. Em seguida, vamos criar uma nova instância do ```CosmosClient``` e configurar alguns scaffolding para nosso programa.

    Abaixo da **Main** método, adicione uma nova tarefa assíncrona designada **GetStartedDemoAsync**, que irá criar uma instância de nossa nova ```CosmosClient```. Nós usaremos **GetStartedDemoAsync** como ponto de entrada que chama os métodos que operam em recursos do Azure Cosmos DB.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

1. Adicione o seguinte código para executar o **GetStartedDemoAsync** tarefa assíncrona a partir do **Main** método. O método **Principal** irá capturar as exceções e escrevê-las na consola.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Selecione **F5** para executar a sua aplicação. O resultado da janela de consola apresenta a mensagem `End of demo, press any key to exit.` que confirma que foi efetuada uma ligação ao Azure Cosmos DB. Em seguida, pode fechar a janela da consola.

Parabéns! Ligou com êxito a uma conta do Azure Cosmos DB. 

## <a name="step-4-create-a-database"></a>Passo 4: Criar uma base de dados
Uma base de dados pode ser criado utilizando o [ **CreateDatabaseIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) ou [ **CreateDatabaseAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) função do ``CosmosClient`` classe. As bases de dados são os contentores lógicos dos itens particionados em contentores.

1. Copie e cole o **CreateDatabaseAsync** método abaixo sua **GetStartedDemoAsync** método. **CreateDatabaseAsync** irá criar uma nova base de dados com o ID ``FamilyDatabase`` se ainda não exista, com o ID especificado a partir do ``databaseId`` campo. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

1. Copie e cole o código a seguir, onde instanciado CosmosClient para chamar o **CreateDatabaseAsync** método que acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Neste momento, seu código deverá agora ser semelhante a esta, com o seu ponto final e a chave primária preenchidos.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Criou uma base de dados do Azure Cosmos DB com êxito.  

## <a id="CreateColl"></a>Passo 5: Criar um contentor
> [!WARNING]
> Chamando o método **CreateContainerIfNotExistsAsync** irá criar um novo contentor, que tem sobre os preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Um contentor pode ser criado utilizando o [ **CreateContainerIfNotExistsAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) ou [ **CreateContainerAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosdatabase) funcionem no **CosmosDatabase** classe. Consiste num contentor de itens (documentos JSON se a API de SQL) e associadas a lógica de aplicação do lado do servidor em JavaScript, por exemplo, procedimentos armazenados, funções definidas pelo utilizador e acionadores.

1. Copie e cole o **CreateContainerAsync** método abaixo sua **CreateDatabaseAsync** método. **CreateContainerAsync** irá criar um novo contentor com o ID ``FamilyContainer`` se ainda não exista, com o ID especificado a partir do ``containerId`` campo particionados por ``LastName`` propriedade.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copie e cole o código a seguir, onde instanciado CosmosClient para chamar o **CreateContainer** método que acabou de adicionar.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

   Selecione **F5** para executar a sua aplicação.

Parabéns! Criou um contentor do Azure Cosmos DB com êxito.  

## <a id="CreateDoc"></a>Passo 6: Adicionar itens ao contentor
Um item pode ser criado utilizando o [ **CreateItemAsync** ](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmoscontainer) função dos **CosmosContainer** classe. Quando é utilizada a API SQL, os itens são projetados como documentos, que são conteúdos JSON definidos pelo utilizador (arbitrários). Pode agora inserir um item no seu contentor do Azure Cosmos DB.

Em primeiro lugar, vamos criar um **família** classe que irá representar objetos armazenados no Azure Cosmos DB neste exemplo. Também iremos criar subclasses **Principal**, **Subordinado**, **Animal de estimação** e **Endereço** utilizadas dentro da **Família**. Item de observação tem de ter uma **Id** propriedade serializado como **id** em JSON.

1. Selecione **Ctrl + Shift + A** para abrir o **Add New Item** caixa de diálogo. Adicionar uma nova classe **Family.cs** ao seu projeto.

    ![Captura de ecrã da adição de uma nova classe de Family.cs no projeto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copie e cole o **família**, **principal**, **filho**, **animal de estimação**, e **endereço** classe **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Navegue de volta para **Program.cs** e adicione o **AddItemsToContainerAsync** método em seu **CreateContainerAsync** método.
O código verifica para se certificar de que um item com o mesmo ID ainda não existir antes de criá-lo. Podemos irá inserir dois itens, um para a família Andersen e a família Wakefield.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Adicione uma chamada para ``AddItemsToContainerAsync`` no ``GetStartedDemoAsync`` método.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Criou dois itens de Azure Cosmos DB com êxito.  

## <a id="Query"></a>Passo 7: Consultar recursos do Azure Cosmos DB
O Azure Cosmos DB suporta [consultas](sql-api-sql-query.md) extensas de documentos JSON armazenados em cada coleção. O código de exemplo seguinte mostra como executar uma consulta os itens que inserimos no passo anterior.

1. Copie e cole o **QueryItemsAsync** método abaixo sua **AddItemsToContainerAsync** método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Adicione uma chamada para ``QueryItemsAsync`` no ``GetStartedDemoAsync`` método.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Consultou com êxito em relação a um contentor do Azure Cosmos DB.

## <a id="ReplaceItem"></a>Passo 8: Substituir um item JSON
Agora, vamos atualizar um item no Azure Cosmos DB.

1. Copie e cole o **ReplaceFamilyItemAsync** método abaixo sua **QueryItemsAsync** método. Tenha em atenção de que estamos a alterar o ``IsRegistered`` propriedade da família e o ``Grade`` de um dos filhos.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Adicione uma chamada para ``ReplaceFamilyItemAsync`` no ``GetStartedDemoAsync`` método.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

   Selecione **F5** para executar a sua aplicação.

Parabéns! Substituiu um item do Azure Cosmos DB com êxito.

## <a id="DeleteDocument"></a>Passo 9: Eliminar item
Agora, iremos eliminar um item no Azure Cosmos DB.

1. Copie e cole o **DeleteFamilyItemAsync** método abaixo sua **ReplaceFamilyItemAsync** método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Adicione uma chamada para ``DeleteFamilyItemAsync`` no ``GetStartedDemoAsync`` método.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

Selecione **F5** para executar a sua aplicação.

Parabéns! Eliminou um item do Azure Cosmos DB com êxito.

## <a id="DeleteDatabase"></a>Passo 10: Eliminar a base de dados
Agora, iremos eliminar nosso banco de dados. A eliminar a base de dados criada remove a base de dados e todos os recursos subordinados (contentores, itens e qualquer procedimentos armazenados, funções definidas pelo utilizador e acionadores). Podemos também será descartar a **CosmosClient** instância.

1. Copie e cole o **DeleteDatabaseAndCleanupAsync** método abaixo sua **DeleteFamilyItemAsync** método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Adicione uma chamada para ``DeleteDatabaseAndCleanupAsync`` no ``GetStartedDemoAsync`` método.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Selecione **F5** para executar a sua aplicação.

Parabéns! Eliminou bases de dados do Azure Cosmos DB com êxito.

## <a id="Run"></a>Passo 11: Executar o C# aplicação de consola tudo!
Selecione F5 no Visual Studio para compilar e executar a aplicação no modo de depuração.

Deverá ver a saída de todo o seu aplicativo numa janela de consola. O resultado apresentará os resultados das consultas que adicionámos e deverá corresponder ao texto de exemplo abaixo.

```
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Parabéns! Concluiu este tutorial e a sua aplicação de consola C# está a funcionar!

## <a id="GetSolution"></a>Obter a solução completa do tutorial
Se não tiver tempo de completar os passos deste tutorial, ou apenas pretender transferir os exemplos de código, pode obtê-los a partir do [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Para criar a solução GetStarted, irá precisar do seguinte:

* Uma conta ativa do Azure. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma [conta do Azure Cosmos DB][cosmos-db-create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) está disponível no GitHub.

Para restaurar as referências para o SDK de .NET do Azure Cosmos DB no Visual Studio, clique com botão direito a **GetStarted** solução no Explorador de soluções e clique em **restaurar pacotes NuGet**. Em seguida, no ficheiro App. config, atualize os valores de EndPointUri e PrimaryKey conforme descrito em [ligar a uma conta do Azure Cosmos DB](#Connect).

É isso, compilação e está no caminho certo!

## <a name="next-steps"></a>Passos Seguintes
* Quer um tutorial ASP.NET MVC mais complexo? Consulte [Tutorial do ASP.NET MVC: Desenvolvimento de aplicação com o Azure Cosmos DB Web](sql-api-dotnet-application-preview.md).
* Pretende testar o dimensionamento e desempenho com o Azure Cosmos DB? Veja [Testar o Desempenho e o Dimensionamento com o Azure Cosmos DB](performance-testing.md)
* Saiba como [monitorizar pedidos, utilização e armazenamento do Azure Cosmos DB](monitor-accounts.md).
* Execute consultas no nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Para saber mais sobre o Azure Cosmos DB, veja [Bem-vindo ao Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
