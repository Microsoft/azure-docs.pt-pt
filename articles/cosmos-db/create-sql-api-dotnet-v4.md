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
ms.openlocfilehash: b69d67a5c4fc1d907f676cf4e400f9fa7df2653b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77585939"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Quickstart: Construa uma aplicação de consola utilizando o .Net V4 SDK para gerir os recursos da conta API Da Azure Cosmos DB SQL.

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Inicie-se com a biblioteca de clientes Azure Cosmos DB SQL API para .NET. Siga os passos neste doc para instalar o pacote .NET V4 (Azure.Cosmos), construir uma app e experimentar o código de exemplo para operações crud básicas nos dados armazenados em Azure Cosmos DB. 

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Pode utilizar o Azure Cosmos DB para criar e consultar rapidamente as bases de dados de chaves/valor, documentos e gráficos. Utilize a biblioteca de clientes Azure Cosmos DB SQL API para.NET para:

* Crie uma base de dados Azure Cosmos e um recipiente
* Adicione dados da amostra ao recipiente
* Consultar os dados 
* Apagar a base de dados

[Pacote](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | de código fonte da biblioteca[(NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/) ou pode [Experimentar O Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. 
* [Núcleo NET 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Pode verificar qual a versão disponível `dotnet --version`no seu ambiente executando .

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da criação de uma conta Azure Cosmos e da criação de um projeto que utiliza a biblioteca de clientes Azure Cosmos DB SQL API para a gestão de recursos. O código de exemplo descrito `FamilyDatabase` neste artigo cria uma base de dados e membros da família (cada membro da família é um item) dentro dessa base de dados. Cada membro da família `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`tem propriedades como. A `LastName` propriedade é usada como chave de partição para o recipiente. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Criar uma conta do Azure Cosmos

Se utilizar o [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) uma opção gratuita para criar uma conta Azure Cosmos, tem de criar uma conta Azure Cosmos DB do tipo **SQL API**. Já foi criada uma conta de teste Azure Cosmos DB para si. Não tem de criar a conta explicitamente, para que possa saltar esta secção e passar para a secção seguinte.

Se tiver a sua própria subscrição Azure ou tiver criado uma subscrição gratuitamente, deverá criar explicitamente uma conta Azure Cosmos. O código seguinte criará uma conta Azure Cosmos com consistência da sessão. A conta é `South Central US` replicada `North Central US`e.  

Você pode usar Azure Cloud Shell para criar a conta Azure Cosmos. O Azure Cloud Shell é uma shell interativa, autenticada e compatível com browsers para gerir recursos do Azure. Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho, quer seja baseada em Bash ou no PowerShell. Para este arranque rápido, escolha o modo **Bash.** A Azure Cloud Shell também requer uma conta de armazenamento, podecriar uma quando solicitada.

Selecione o botão **Try It** ao lado do código seguinte, escolha o modo **Bash** selecione criar uma conta de **armazenamento** e iniciar sessão na Cloud Shell. Próxima cópia e cole o seguinte código à casca de nuvem Azure e execute-o. O nome da conta Azure Cosmos deve ser `mysqlapicosmosdb` globalmente único, certifique-se de atualizar o valor antes de executar o comando.

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

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Criar uma nova aplicação .NET

Crie uma nova aplicação .NET no seu editor ou IDE preferido. Abra o pedido de comando do Windows ou uma janela terminal a partir do seu computador local. Executará todos os comandos nas próximas secções a partir do pedido de comando ou terminal.  Executar o novo comando do dotnet seguinte `todo`para criar uma nova app com o nome . O parâmetro --langVersion define a propriedade LangVersion no ficheiro de projeto criado.

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

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instale o pacote Azure Cosmos DB

Ainda no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando de pacote de adição de dotnet.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie as credenciais da sua conta Azure Cosmos do portal Azure

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deverá passar as credenciais da conta Azure Cosmos para a aplicação. Obtenha as credenciais da sua conta Azure Cosmos seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para a sua conta Azure Cosmos.

1. Abra o painel **de Teclas** e copie a CHAVE **URI** e **PRIMÁRIA** da sua conta. Você adicionará os valores URI e chaves a uma variável ambiental no próximo passo.

## <a name="object-model"></a><a id="object-model"></a>Modelo de objeto

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
* DeleteAsync - Elimina a base de dados especificada da sua conta Azure Cosmos. `DeleteAsync`método apenas elimina a base de dados.

 ## <a name="code-examples"></a><a id="code-examples"></a>Exemplos de código

O código de amostra descrito neste artigo cria uma base de dados familiar em Azure Cosmos DB. A base de dados familiar contém detalhes familiares como nome, endereço, localização, pais, crianças e animais de estimação associados. Antes de povoar os dados na sua conta Azure Cosmos, defina as propriedades de um item familiar. Crie uma `Family.cs` nova classe chamada ao nível raiz da sua aplicação de amostra e adicione-lhe o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adicione as diretivas de utilização & definir o objeto cliente

A partir do diretório `Program.cs` do projeto, abra o ficheiro no seu editor e adicione as seguintes diretivas utilizando as diretivas no topo da sua aplicação:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adicione as seguintes variáveis globais na sua `Program` classe. Estes incluirão as chaves de ponta e autorização, o nome da base de dados e o recipiente que irá criar. Certifique-se de que substitui os valores das chaves de ponta e de autorização de acordo com o seu ambiente. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Por fim, `Main` substitua o método:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Criar uma base de dados 

Defina `CreateDatabaseAsync` o `program.cs` método dentro da classe. Este método `FamilyDatabase` cria o se já não existir.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Criar um contentor

Defina `CreateContainerAsync` o `Program` método dentro da classe. Este método `FamilyContainer` cria o se já não existir. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Criar um item

Crie um item `AddItemsToContainerAsync` familiar adicionando o método com o seguinte código. Pode utilizar `CreateItemAsync` os `UpsertItemAsync` ou métodos para criar um item:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, pode fazer uma consulta para obter os detalhes da família "Andersen". O código seguinte mostra como executar a consulta utilizando a consulta SQL diretamente. A consulta da SQL para obter os `SELECT * FROM c WHERE c.LastName = 'Andersen'`detalhes da família "Anderson" é: . Defina `QueryItemsAsync` o `Program` método dentro da classe e adicione-lhe o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Substituir um item 

Leia um item familiar e, `ReplaceFamilyItemAsync` em seguida, atualize-o adicionando o método com o seguinte código.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Eliminar um item 

Elimine um item `DeleteFamilyItemAsync` familiar adicionando o método com o seguinte código.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Apagar a base de dados 

Por fim, pode eliminar `DeleteDatabaseAndCleanupAsync` a base de dados adicionando o método com o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Depois de adicionar todos os métodos necessários, guarde o `Program` ficheiro. 

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
