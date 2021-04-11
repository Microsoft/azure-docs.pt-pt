---
title: Gerir recursos Azure Cosmos DB SQL API utilizando .NET V4 SDK
description: Quickstart para construir uma aplicação de consola usando .NET V4 SDK para gerir os recursos da conta Azure Cosmos DB SQL API.
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/07/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: 8afa0ec51f86bd77e5539840f3419eaff89171f5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107198"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-preview-to-manage-azure-cosmos-db-sql-api-account-resources"></a>Quickstart: Construa uma aplicação de consola utilizando o .NET V4 SDK (Preview) para gerir os recursos da conta API API Azure Cosmos DB SQL.
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [SDK v4 de Java](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Começa com a biblioteca de clientes Azure Cosmos DB SQL API para .NET. Siga os passos neste doc para instalar o pacote .NET V4 (Azure.Cosmos), construir uma app e experimentar o código de exemplo para operações básicas da CRUD nos dados armazenados no Azure Cosmos DB.

> [!IMPORTANT]
> O .NET V4 SDK para Azure Cosmos DB está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB é a base de dados NoSQL rápida da Microsoft com APIs abertos para qualquer escala. Pode utilizar a Azure Cosmos DB para criar e consultar rapidamente bases de dados de chaves/valor, documentos e gráficos. Utilize a biblioteca de clientes Azure Cosmos DB SQL API para .NET para:

* Criar uma base de dados Azure Cosmos e um recipiente
* Adicione dados de amostra ao recipiente
* Consultar os dados 
* Eliminar a base de dados

[Código fonte da biblioteca](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscription - [crie um gratuitamente](https://azure.microsoft.com/free/) ou pode [experimentar gratuitamente a Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) sem uma subscrição Azure, gratuitamente e compromissos. 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core). Pode verificar qual a versão disponível no seu ambiente executando `dotnet --version` .

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da criação de uma conta Azure Cosmos e da criação de um projeto que utiliza a biblioteca de clientes API API AZure Cosmos DB SQL para .NET para gerir recursos. O código de exemplo descrito neste artigo cria uma `FamilyDatabase` base de dados e membros da família (cada membro da família é um item) dentro dessa base de dados. Cada membro da família tem propriedades `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` como. A `LastName` propriedade é usada como chave de partição para o recipiente. 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Criar uma conta do Azure Cosmos

Se utilizar o [Try Azure Cosmos DB para](https://azure.microsoft.com/try/cosmosdb/) opção gratuita para criar uma conta Azure Cosmos, deve criar uma conta DB Azure Cosmos do tipo **SQL API**. Uma conta de teste DB da Azure Cosmos já foi criada para si. Não precisa de criar a conta explicitamente, para que possa saltar esta secção e passar para a secção seguinte.

Se tiver a sua própria subscrição Azure ou tiver uma subscrição gratuita, deverá criar uma conta Azure Cosmos explicitamente. O seguinte código criará uma conta Azure Cosmos com consistência da sessão. A conta é replicada em `South Central US` e `North Central US` .  

Pode utilizar a Azure Cloud Shell para criar a conta Azure Cosmos. O Azure Cloud Shell é uma shell interativa, autenticada e compatível com browsers para gerir recursos do Azure. Dá a flexibilidade de escolher a experiência de shell mais adequada ao seu método de trabalho, quer seja baseada em Bash ou no PowerShell. Para este arranque rápido, escolha o modo **Bash.** AZure Cloud Shell também requer uma conta de armazenamento, pode criar uma quando solicitada.

Selecione o botão **Try It** ao lado do seguinte código, escolha o modo **Bash** **selecione criar uma conta de armazenamento** e iniciar sessão na Cloud Shell. Em seguida, copie e cole o seguinte código para Azure Cloud Shell e execute-o. O nome da conta Azure Cosmos deve ser globalmente único, certifique-se de atualizar o `mysqlapicosmosdb` valor antes de executar o comando.

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

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

A criação da conta Azure Cosmos demora algum tempo, uma vez que a operação é bem sucedida, você pode ver a saída de confirmação. Após o comando ter concluído com sucesso, inscreva-se no [portal Azure](https://portal.azure.com/) e verifique se existe a conta Azure Cosmos com o nome especificado. Pode fechar a janela Azure Cloud Shell após a criação do recurso. 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>Criar uma nova aplicação .NET

Crie uma nova aplicação .NET no seu editor preferido ou IDE. Abra o pedido de comando do Windows ou uma janela do terminal a partir do computador local. Executará todos os comandos nas próximas secções a partir do comando ou terminal.  Executar o seguinte comando dotnet novo para criar uma nova app com o nome `todo` . O parâmetro langVersion define a propriedade LangVersion no ficheiro do projeto criado.

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

   ```bash
   cd todo
   dotnet build
   ```

A produção esperada da construção deve ser algo assim:

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Instale o pacote DB Azure Cosmos

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes Azure Cosmos DB para .NET Core utilizando o comando do pacote de adicionar dotnet.

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copie as suas credenciais de conta Azure Cosmos do portal Azure

A aplicação da amostra precisa de autenticar na sua conta Azure Cosmos. Para autenticar, deve passar as credenciais de conta Azure Cosmos para a aplicação. Obtenha as suas credenciais de conta Azure Cosmos seguindo estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Navegue para a sua conta Azure Cosmos.

1. Abra o painel **de chaves** e copie o **URI** e **a CHAVE PRINCIPAL** da sua conta. Irá adicionar o URI e os valores das teclas a uma variável ambiental no passo seguinte.

## <a name="object-model"></a><a id="object-model"></a>Modelo de objeto

Antes de começar a construir a aplicação, vamos olhar para a hierarquia de recursos em Azure Cosmos DB e o modelo de objeto usado para criar e aceder a estes recursos. O Azure Cosmos DB cria recursos na seguinte ordem:

* Conta Azure Cosmos 
* Bases de Dados 
* Contentores 
* Itens

Para saber mais sobre a hierarquia de diferentes entidades, consulte o [trabalho com bases de dados, contentores e itens no artigo da Azure Cosmos DB.](account-databases-containers-items.md) Utilizará as seguintes classes .NET para interagir com estes recursos:

* CosmosClient - Esta classe proporciona uma representação lógica do lado do cliente para o serviço DB Azure Cosmos. O objeto cliente é usado para configurar e executar pedidos contra o serviço.
* CreateDatabaseIfNotExistsAsync - Este método cria (se não existir) ou obtém (se já existe) um recurso de base de dados como uma operação assíncrono. 
* CreateContainerIfNotExistsAsync - Este método cria (se não existir) ou obtém (se já existe) um recipiente como uma operação assíncrona. Pode verificar o código de estado da resposta para determinar se o recipiente foi recém-criado (201) ou se foi devolvido um contentor existente (200). 
* CreateItemAsync - Este método cria um item dentro do recipiente.
* UpsertItemAsync - Este método cria um item dentro do recipiente se já não existir ou substituir o item se já existir. 
* GetItemQueryIterator - Este método cria uma consulta para itens debaixo de um recipiente numa base de dados do Azure Cosmos utilizando uma declaração SQL com valores parametrizados. 
* DeleteAsync - Elimina a base de dados especificada da sua conta Azure Cosmos. `DeleteAsync` método apenas elimina a base de dados.

 ## <a name="code-examples"></a><a id="code-examples"></a>Exemplos de código

O código de amostra descrito neste artigo cria uma base de dados familiar em Azure Cosmos DB. A base de dados da família contém detalhes familiares como nome, endereço, localização, pais, crianças e animais de estimação associados. Antes de povoar os dados para a sua conta Azure Cosmos, defina as propriedades de um item familiar. Crie uma nova classe nomeada `Family.cs` ao nível da raiz da sua aplicação de amostra e adicione-lhe o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>Adicione as diretivas de utilização & definir o objeto cliente

A partir do diretório do projeto, abra o `Program.cs` ficheiro no seu editor e adicione as seguintes diretivas no topo da sua aplicação:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


Adicione as seguintes variáveis globais na sua `Program` classe. Estes incluirão as chaves de ponto final e autorização, o nome da base de dados e o recipiente que irá criar. Certifique-se de que substitui os valores das teclas de ponta e de autorização de acordo com o ambiente. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

Por último, substitua o `Main` método:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>Criar uma base de dados 

Defina o `CreateDatabaseAsync` método dentro da `program.cs` classe. Este método cria o `FamilyDatabase` se já não existe.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>Criar um contentor

Defina o `CreateContainerAsync` método dentro da `Program` classe. Este método cria o `FamilyContainer` se já não existe. 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>Criar um item

Crie um item familiar adicionando o `AddItemsToContainerAsync` método com o seguinte código. Pode utilizar os `CreateItemAsync` métodos ou `UpsertItemAsync` métodos para criar um item:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>Consultar os itens

Depois de inserir um item, você pode executar uma consulta para obter os detalhes da família "Andersen". O código que se segue mostra como executar a consulta utilizando a consulta SQL diretamente. A consulta do SQL para obter os detalhes da família "Anderson" é: `SELECT * FROM c WHERE c.LastName = 'Andersen'` . Defina o `QueryItemsAsync` método dentro da classe e `Program` adicione-lhe o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>Substituir um item 

Leia um item familiar e, em seguida, atualize-o adicionando o `ReplaceFamilyItemAsync` método com o seguinte código.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>Eliminar um item 

Elimine um item familiar adicionando o `DeleteFamilyItemAsync` método com o seguinte código.

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>Eliminar a base de dados 

Finalmente pode eliminar a base de dados adicionando o `DeleteDatabaseAndCleanupAsync` método com o seguinte código:

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

Depois de adicionar todos os métodos necessários, guarde o `Program` ficheiro. 

## <a name="run-the-code"></a>Executar o código

Em seguida, construa e execute a aplicação para criar os recursos DB Azure Cosmos.

   ```bash
   dotnet run
   ```

A seguinte saída é gerada quando executar a aplicação. Também pode entrar no portal Azure e validar que os recursos são criados:

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

Pode validar que os dados são criados assinando no portal Azure e ver os itens necessários na sua conta Azure Cosmos. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover a conta Azure Cosmos e o grupo de recursos correspondente. O seguinte comando mostra como eliminar o grupo de recursos utilizando o Azure CLI:

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a criar uma conta Azure Cosmos, criar uma base de dados e um contentor utilizando uma aplicação .NET Core. Pode agora importar dados adicionais para a sua conta Azure Cosmos com as instruções int o seguinte artigo. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
