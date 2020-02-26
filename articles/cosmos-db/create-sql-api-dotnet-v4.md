---
title: Gerir os recursos da API Da Azure Cosmos DB SQL utilizando .Net V4 SDK
description: Quickstart para construir uma aplicação de consola usando .Net V4 SDK para gerir os recursos da conta API Da Azure Cosmos DB SQL.
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/10/2020
ms.openlocfilehash: 6587b6d5ceb18bcc2374594aef91da91f792bb84
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585939"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Quickstart: Construa uma aplicação de consola utilizando o .Net V4 SDK para gerir os recursos da conta API Da Azure Cosmos DB SQL.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Inicie-se com a biblioteca de clientes Azure Cosmos DB SQL API para .NET. Siga os passos neste doc para instalar o pacote .NET V4 (Azure.Cosmos), construir uma app e experimentar o código de exemplo para operações crud básicas nos dados armazenados em Azure Cosmos DB. 

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Pode utilizar o Azure Cosmos DB para criar e consultar rapidamente as bases de dados de chaves/valor, documentos e gráficos. Utilize a biblioteca de clientes Azure Cosmos DB SQL API para.NET para:

* Crie uma base de dados Azure Cosmos e um recipiente
* Adicione dados da amostra ao recipiente
* Consultar os dados 
* Apagar a base de dados

Pacote de | de [código-fonte da biblioteca](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) [(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/) ou pode [Experimentar O Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. 
* [Núcleo NET 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Pode verificar qual a versão disponível no seu ambiente, executando `dotnet --version`.

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da criação de uma conta Azure Cosmos e da criação de um projeto que utiliza a biblioteca de clientes Azure Cosmos DB SQL API para a gestão de recursos. O código de exemplo descrito neste artigo cria uma base de dados `FamilyDatabase` e membros da família (cada membro da família é um item) dentro dessa base de dados. Cada membro da família tem propriedades como `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. A propriedade `LastName` é usada como chave de partição para o recipiente. 

### <a id="create-account"></a>Criar uma conta Azure Cosmos

Se utilizar o [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) uma opção gratuita para criar uma conta Azure Cosmos, tem de criar uma conta Azure Cosmos DB do tipo **SQL API**. Já foi criada uma conta de teste Azure Cosmos DB para si. Não tem de criar a conta explicitamente, para que possa saltar esta secção e passar para a secção seguinte.

Se tiver a sua própria subscrição Azure ou tiver criado uma subscrição gratuitamente, deverá criar explicitamente uma conta Azure Cosmos. O código seguinte criará uma conta Azure Cosmos com consistência da sessão. A conta é replicada em `South Central US` e `North Central US`.  

Você pode usar Azure Cloud Shell para criar a conta Azure Cosmos. A Azure Cloud Shell é uma concha interativa, autenticada e acessível ao navegador para gerir os recursos do Azure. Proporciona a flexibilidade de escolher a experiência da concha que melhor se adequa à forma como trabalha, seja a Bash ou a PowerShell. Para este arranque rápido, escolha o modo **Bash.** A Azure Cloud Shell também requer uma conta de armazenamento, podecriar uma quando solicitada.

Selecione o botão **Try It** ao lado do código seguinte, escolha o modo **Bash** selecione criar uma conta de **armazenamento** e iniciar sessão na Cloud Shell. Próxima cópia e cole o seguinte código à casca de nuvem Azure e execute-o. O nome da conta Azure Cosmos deve ser globalmente único, certifique-se de atualizar o valor `mysqlapicosmosdb` antes de executar o comando.

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

A criação da conta Azure Cosmos demora algum tempo, uma vez que a operação é bem sucedida, pode ver a saída de confirmação. Depois do comando terminar com sucesso, assine no [portal Azure](https://portal.azure.com/) e verifique se a conta Azure Cosmos com o nome especificado existe. Pode fechar a janela Azure Cloud Shell depois de o recurso ser criado. 

### <a id="create-dotnet-core-app"></a>Criar uma nova aplicação .NET

Crie uma nova aplicação .NET no seu editor ou IDE preferido. Abra o pedido de comando do Windows ou uma janela terminal a partir do seu computador local. Executará todos os comandos nas próximas secções a partir do pedido de comando ou terminal.  Execute o novo comando do dotnet seguinte para criar uma nova app com o nome `todo`. O parâmetro --langVersion define a propriedade LangVersion no ficheiro de projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

   ```bash
   cd todo
   dotnet build
   ```

A saída esperada da construção deve ser algo assim:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Instale o pacote Azure Cosmos DB

Ainda no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando de pacote de adição de dotnet.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie as credenciais da sua conta Azure Cosmos do portal Azure

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deverá passar as credenciais da conta Azure Cosmos para a aplicação. Obtenha as credenciais da sua conta Azure Cosmos seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para a sua conta Azure Cosmos.

1. Abra o painel **de Teclas** e copie a CHAVE **URI** e **PRIMÁRIA** da sua conta. Você adicionará os valores URI e chaves a uma variável ambiental no próximo passo.

## <a id="object-model"></a>Modelo de objeto

Antes de começar a construir a aplicação, vamos olhar para a hierarquia de recursos em Azure Cosmos DB e o modelo de objeto usado para criar e aceder a esses recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta Azure Cosmos 
* Bases de Dados 
* Contentores 
* Itens

Para saber mais sobre a hierarquia de diferentes entidades, consulte o trabalho com bases de [dados, contentores e itens no artigo da Azure Cosmos DB.](databases-containers-items.md) Utilizará as seguintes classes .NET para interagir com estes recursos:

* CosmosClient - Esta classe fornece uma representação lógica do lado do cliente para o serviço Azure Cosmos DB. O objeto do cliente é usado para configurar e executar pedidos contra o serviço.
* CreateDatabaseIfNotExistsAsync - Este método cria (se não existe) ou obtém (se já existe) um recurso de base de dados como uma operação assíncrona. 
* CreateContainerIfNotExistsAsync - Este método cria (se não existe) ou obtém (se já existe) um recipiente como uma operação assíncrona. Pode verificar o código de estado a partir da resposta para determinar se o recipiente foi recém-criado (201) ou se um contentor existente foi devolvido (200). 
* CreateItemAsync - Este método cria um item dentro do recipiente.
* UpsertItemAsync - Este método cria um item dentro do recipiente se já não existir ou substituir o item se já existir. 
* GetItemQueryIterator - Este método cria uma consulta para itens sob um recipiente numa base de dados Azure Cosmos usando uma declaração SQL com valores parametrizados. 
* DeleteAsync - Elimina a base de dados especificada da sua conta Azure Cosmos. `DeleteAsync` método apenas elimina a base de dados.

 ## <a id="code-examples"></a>Exemplos de código

O código de amostra descrito neste artigo cria uma base de dados familiar em Azure Cosmos DB. A base de dados familiar contém detalhes familiares como nome, endereço, localização, pais, crianças e animais de estimação associados. Antes de povoar os dados na sua conta Azure Cosmos, defina as propriedades de um item familiar. Crie uma nova classe chamada `Family.cs` ao nível raiz da sua aplicação de amostra e adicione-lhe o seguinte código:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Family.cs":::

### <a name="add-the-using-directives--define-the-client-object"></a>Adicione as diretivas de utilização e defina o objeto cliente

A partir do diretório do projeto, abra o ficheiro `Program.cs` no seu editor e adicione as seguintes diretivas utilizando as seguintes diretivas no topo da sua aplicação:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="Usings":::


Adicione as seguintes variáveis globais na sua classe `Program`. Estes incluirão as chaves de ponta e autorização, o nome da base de dados e o recipiente que irá criar. Certifique-se de que substitui os valores das chaves de ponta e de autorização de acordo com o seu ambiente. 

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="Constants":::

Por último, substitua o método `Main`:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="Main":::

### <a name="create-a-database"></a>Criar uma base de dados 

Defina o método `CreateDatabaseAsync` dentro da classe `program.cs`. Este método cria a `FamilyDatabase` se já não existir.

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="CreateDatabaseAsync":::

### <a name="create-a-container"></a>Criar um contentor

Defina o método `CreateContainerAsync` dentro da classe `Program`. Este método cria a `FamilyContainer` se já não existir. 

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="CreateContainerAsync":::

### <a name="create-an-item"></a>Criar um item

Crie um item familiar adicionando o método `AddItemsToContainerAsync` com o seguinte código. Pode utilizar os métodos `CreateItemAsync` ou `UpsertItemAsync` para criar um item:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="AddItemsToContainerAsync":::

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, pode fazer uma consulta para obter os detalhes da família "Andersen". O código seguinte mostra como executar a consulta utilizando a consulta SQL diretamente. A consulta da SQL para obter os detalhes da família "Anderson" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Defina o método `QueryItemsAsync` dentro da classe `Program` e adicione-lhe o seguinte código:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="QueryItemsAsync":::

### <a name="replace-an-item"></a>Substitua um item 

Leia um item familiar e, em seguida, atualize-o adicionando o método `ReplaceFamilyItemAsync` com o seguinte código.

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="ReplaceFamilyItemAsync":::

### <a name="delete-an-item"></a>Apagar um item 

Elimine um item familiar adicionando o método `DeleteFamilyItemAsync` com o seguinte código.

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="DeleteFamilyItemAsync":::

### <a name="delete-the-database"></a>Apagar a base de dados 

Por fim, pode eliminar a base de dados adicionando o método `DeleteDatabaseAndCleanupAsync` com o seguinte código:

:::code language="csharp" source="~/cosmos-dotnet-v4-getting-started/src/Program.cs" id="DeleteDatabaseAndCleanupAsync":::

Depois de adicionar todos os métodos necessários, guarde o ficheiro `Program`. 

## <a name="run-the-code"></a>Executar o código

Em seguida, construa e execute a aplicação para criar os recursos Do BD Azure Cosmos.

   ```bash
   dotnet run
   ```

A seguinte saída é gerada quando executa a aplicação. Também pode entrar no portal Azure e validar que os recursos são criados:

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

Pode validar que os dados são criados através da assinatura no portal Azure e ver os itens necessários na sua conta Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover a conta Azure Cosmos e o grupo de recursos correspondente. O seguinte comando mostra como eliminar o grupo de recursos utilizando o ClI Azure:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos, criar uma base de dados e um recipiente usando uma aplicação .NET Core. Pode agora importar dados adicionais para a sua conta Azure Cosmos com as instruções int int no seguinte artigo. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
