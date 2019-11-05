---
title: Gerenciar Azure Cosmos DB recursos da API do SQL usando o SDK do .net v4
description: Início rápido para criar um aplicativo de console usando o SDK do .net v4 para gerenciar Azure Cosmos DB recursos da conta da API do SQL.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: c927ef071dec6e87e82a9634ccfc7097f2f187e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502066"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Início rápido: Crie um aplicativo de console usando o SDK do .net v4 para gerenciar Azure Cosmos DB recursos de conta da API do SQL.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Introdução à biblioteca de cliente da API do SQL Azure Cosmos DB para .NET. Siga as etapas neste documento para instalar o pacote do .NET V4 (Azure. Cosmos), criar um aplicativo e experimentar o código de exemplo para operações CRUD básicas nos dados armazenados no Azure Cosmos DB. 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Você pode usar Azure Cosmos DB para criar e consultar rapidamente bancos de dados de chave/valor, documento e gráfico. Use a biblioteca de cliente da API do SQL do Azure Cosmos DB para .NET para:

* Criar um banco de dados Cosmos do Azure e um contêiner
* Adicionar dados de exemplo ao contêiner
* Consultar os dados 
* Excluir o banco de dados

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie um gratuitamente](https://azure.microsoft.com/free/) ou você pode [tentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e com compromissos. 
* [SDK do NET Core 3](https://dotnet.microsoft.com/download/dotnet-core). Você pode verificar qual versão está disponível em seu ambiente executando `dotnet --version`.

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela criação de uma conta do Azure Cosmos e pela configuração de um projeto que usa Azure Cosmos DB biblioteca de cliente da API do SQL para .NET para gerenciar recursos. O código de exemplo descrito neste artigo cria um `FamilyDatabase` banco de dados e membros da família (cada membro da família é um item) dentro desse banco de dados. Cada membro da família tem propriedades como `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A propriedade `LastName` é usada como a chave de partição para o contêiner. 

### <a id="create-account"></a>Criar uma conta do Azure Cosmos

Se você usar a opção [tentar Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) para criar uma conta do Azure Cosmos, deverá criar uma conta de Azure Cosmos DB do tipo **API do SQL**. Uma conta de teste Azure Cosmos DB já foi criada para você. Você não precisa criar a conta explicitamente, portanto, você pode ignorar esta seção e ir para a próxima seção.

Se você tiver sua própria assinatura do Azure ou tiver criado uma assinatura gratuitamente, deverá criar uma conta do Azure Cosmos explicitamente. O código a seguir criará uma conta do Azure cosmos com consistência de sessão. A conta é replicada em `South Central US` e `North Central US`.  

Você pode usar Azure Cloud Shell para criar a conta do Azure Cosmos. Azure Cloud Shell é um shell interativo, autenticado e acessível por navegador para gerenciar recursos do Azure. Ele fornece a flexibilidade de escolher a experiência de shell que melhor se adapta à maneira como você trabalha, seja bash ou PowerShell. Para este guia de início rápido, escolha o modo **bash** . Azure Cloud Shell também requer uma conta de armazenamento, você pode criar uma quando solicitado.

Selecione o botão **experimentar** ao lado do código a seguir, escolha o modo **bash** selecione **criar uma conta de armazenamento** e faça logon para Cloud Shell. Em seguida, copie e cole o código a seguir no Azure cloud Shell e execute-o. O nome da conta do Azure Cosmos deve ser globalmente exclusivo, certifique-se de atualizar o valor de `mysqlapicosmosdb` antes de executar o comando.

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

A criação da conta do Azure Cosmos demora um pouco, uma vez que a operação é bem-sucedida, você pode ver a saída de confirmação. Depois que o comando for concluído com êxito, entre no [portal do Azure](https://portal.azure.com/) e verifique se a conta do Azure cosmos com o nome especificado existe. Você pode fechar a janela de Azure Cloud Shell após a criação do recurso. 

### <a id="create-dotnet-core-app"></a>Criar um novo aplicativo .NET

Crie um novo aplicativo .NET em seu editor preferido ou IDE. Abra o prompt de comando do Windows ou uma janela de terminal do computador local. Você executará todos os comandos nas próximas seções do prompt de comando ou do terminal.  Execute o seguinte comando dotnet novo para criar um novo aplicativo com o nome `todo`. O parâmetro--langVersion define a Propriedade LangVersion no arquivo de projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

   ```bash
   cd todo
   dotnet build
   ```

A saída esperada da compilação deve ser semelhante a esta:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Instalar o pacote de Azure Cosmos DB

Ainda no diretório do aplicativo, instale a biblioteca de cliente do Azure Cosmos DB para .NET Core usando o comando dotnet adicionar pacote.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie suas credenciais de conta do Azure Cosmos do portal do Azure

O aplicativo de exemplo precisa se autenticar na sua conta do Azure Cosmos. Para autenticar, você deve passar as credenciais da conta do Azure Cosmos para o aplicativo. Obtenha suas credenciais de conta do Azure Cosmos seguindo estas etapas:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue até sua conta do Azure Cosmos.

1. Abra o painel **chaves** e copie o **URI** e a **chave primária** da sua conta. Você adicionará os valores de URI e de chaves a uma variável de ambiente na próxima etapa.

## <a id="object-model"></a>Modelo de objeto

Antes de começar a criar o aplicativo, vamos examinar a hierarquia de recursos em Azure Cosmos DB e o modelo de objeto usado para criar e acessar esses recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta do Azure Cosmos 
* Bases de Dados 
* Contentores 
* Itens

Para saber mais sobre a hierarquia de entidades diferentes, consulte o artigo [trabalhando com bancos de dados, contêineres e itens no Azure Cosmos DB](databases-containers-items.md) . Você usará as seguintes classes .NET para interagir com estes recursos:

* CosmosClient-essa classe fornece uma representação lógica do lado do cliente para o serviço de Azure Cosmos DB. O objeto de cliente é usado para configurar e executar solicitações no serviço.
* CreateDatabaseIfNotExistsAsync-esse método cria (se não existir) ou obtém (se já existe) um recurso de banco de dados como uma operação assíncrona. 
* CreateContainerIfNotExistsAsync-esse método cria (se ele não existir) ou obtém (se já existir) um contêiner como uma operação assíncrona. Você pode verificar o código de status da resposta para determinar se o contêiner foi criado recentemente (201) ou se um contêiner existente foi retornado (200). 
* CreateItemAsync-esse método cria um item dentro do contêiner.
* UpsertItemAsync-esse método cria um item dentro do contêiner se ele ainda não existir ou substituirá o item se ele já existir. 
* GetItemQueryIterator-esse método cria uma consulta para itens em um contêiner em um banco de dados Cosmos do Azure usando uma instrução SQL com valores com parâmetros. 
* DeleteAsync-exclui o banco de dados especificado de sua conta do Azure Cosmos. `DeleteAsync` método apenas exclui o banco de dados.

 ## <a id="code-examples"></a>Exemplos de código

O código de exemplo descrito neste artigo cria um banco de dados de família em Azure Cosmos DB. O banco de dados da família contém detalhes da família, como nome, endereço, local, pais associados, filhos e animais de estimação. Antes de preencher os dados para sua conta do Azure Cosmos, defina as propriedades de um item da família. Crie uma nova classe chamada `Family.cs` no nível raiz do seu aplicativo de exemplo e adicione o seguinte código a ela:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adicionar as diretivas using & definir o objeto de cliente

No diretório do projeto, abra o arquivo `Program.cs` em seu editor e adicione as seguintes diretivas using na parte superior do seu aplicativo:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adicione as seguintes variáveis globais em sua classe `Program`. Elas incluirão o ponto de extremidade e as chaves de autorização, o nome do banco de dados e o contêiner que você criará. Certifique-se de substituir os valores de chaves de autorização e ponto de extremidade de acordo com seu ambiente. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Por fim, substitua o método `Main`:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Criar uma base de dados 

Defina o método `CreateDatabaseAsync` dentro da classe `program.cs`. Esse método criará o `FamilyDatabase` se ele ainda não existir.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Criar um contentor

Defina o método `CreateContainerAsync` dentro da classe `Program`. Esse método criará o `FamilyContainer` se ele ainda não existir. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Criar um item

Crie um item de família adicionando o método `AddItemsToContainerAsync` com o código a seguir. Você pode usar os métodos `CreateItemAsync` ou `UpsertItemAsync` para criar um item:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, você pode executar uma consulta para obter os detalhes da família "Andersen". O código a seguir mostra como executar a consulta usando a consulta SQL diretamente. A consulta SQL para obter os detalhes da família "Anderson" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Defina o método `QueryItemsAsync` dentro da classe `Program` e adicione o seguinte código a ele:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Substituir um item 

Leia um item da família e, em seguida, atualize-o adicionando o método `ReplaceFamilyItemAsync` com o código a seguir.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Excluir um item 

Exclua um item de família adicionando o método `DeleteFamilyItemAsync` com o código a seguir.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Excluir o banco de dados 

Por fim, você pode excluir o banco de dados adicionando o método `DeleteDatabaseAndCleanupAsync` com o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Depois de adicionar todos os métodos necessários, salve o arquivo `Program`. 

## <a name="run-the-code"></a>Executar o código

Em seguida, compile e execute o aplicativo para criar os recursos de Azure Cosmos DB.

   ```bash
   dotnet run
   ```

A saída a seguir é gerada quando você executa o aplicativo. Você também pode entrar no portal do Azure e validar que os recursos são criados:

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

Você pode validar que os dados são criados entrando no portal do Azure e ver os itens necessários em sua conta do Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o CLI do Azure ou Azure PowerShell para remover a conta do Azure Cosmos e o grupo de recursos correspondente. O comando a seguir mostra como excluir o grupo de recursos usando o CLI do Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar uma conta do Azure Cosmos, criar um banco de dados e um contêiner usando um aplicativo .NET Core. Agora você pode importar dados adicionais para sua conta do Azure cosmos com as instruções int no seguinte artigo. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
