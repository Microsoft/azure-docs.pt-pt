---
title: Azure Cosmos DB Table API usando .NET Standard SDK
description: Saiba como armazenar e consultar os dados estruturados na conta API da Tabela DB da Azure Cosmos
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: f4817e53cb36bc96add19d855750a73a151b4891
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772302"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Introdução ao armazenamento de API de Tabela do Azure Cosmos DB e Tabelas do Azure com o SDK .NET
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Pode utilizar o armazenamento API da Tabela AZure Cosmos ou Azure Table para armazenar dados NoSQL estruturados na nuvem, fornecendo uma loja chave/atributo com um design de esquemas a menos. Como o Azure Cosmos DB Table API e o armazenamento de mesa são menos esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. Pode utilizar a Azure Cosmos DB Table API ou o armazenamento da Tabela para armazenar conjuntos de dados flexíveis, tais como dados do utilizador para aplicações web, livros de endereços, informações de dispositivos ou outros tipos de metadados que o seu serviço necessita. 

Este tutorial descreve uma amostra que mostra como usar a [Microsoft Azure Cosmos DB Table Library para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) com Azure Cosmos DB Table API e Azure Table storage scenarios. Deve utilizar a ligação específica ao serviço Azure. Estes cenários são explorados usando exemplos C# que ilustram como criar tabelas, inserir/atualizar dados, consultar dados e eliminar as tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Esta biblioteca está atualmente disponível para o quadro .NET Standard e .NET. 

* [Conta Azure Cosmos DB Table API](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Criar um projeto de consola .NET

No Visual Studio, crie uma nova aplicação de consola .NET. Os passos seguintes mostram-lhe como criar uma aplicação de consola no Visual Studio 2019. Pode utilizar a Biblioteca de MesaS Azure Cosmos DB em qualquer tipo de aplicação .NET, incluindo um serviço de nuvem Azure ou aplicação web, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

1. Selecione **o**  >  **novo**  >  **projeto de arquivo**.

1. Escolha **a App consola (.NET Core)** e, em seguida, selecione **Seguinte**.

1. No campo nome do **Projeto,** insira um nome para a sua aplicação, como **CosmosTableSamples**. (Pode fornecer um nome diferente quando necessário.)

1. Selecione **Criar**.

Todos os exemplos de código desta amostra podem ser adicionados ao método Principal do ficheiro **.cs programa** da sua aplicação da consola.

## <a name="install-the-required-nuget-package"></a>Instale o pacote NuGet necessário

Para obter o pacote NuGet, siga estes passos:

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.

1. Procure online por [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , e [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) selecione **Instalar** para instalar a Microsoft Azure Cosmos DB Table Library.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

1. A partir do [portal Azure,](https://portal.azure.com/)navegue para a sua conta Azure Cosmos ou para a conta de Armazenamento de Mesa. 

1. Abra o painel **de chaves de ligação** ou **de acesso.** Utilize o botão de cópia à direita da janela, para copiar a **CADEIA DE LIGAÇÃO PRIMÁRIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Ver e copiar a CADEIA DE LIGAÇÃO PRIMÁRIA no painel Cadeia de Ligação":::
   
1. Para configurar a sua cadeia de conexão, a partir do estúdio visual clique no seu projeto **CosmosTableSamples**.

1. **Selecione Adicionar** e, em seguida, **Novo Item**. Crie um novo ficheiro **Settings.js** com o tipo de ficheiro como **Ficheiro de Configuração JSON do TypeScript.** 

1. Substitua o código no Settings.jsficheiro com o seguinte código e atribua a sua cadeia de ligação primária:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Clique à direita no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **AppSettings.cs**.

1. Adicione o seguinte código ao ficheiro .cs AppSettings. Este ficheiro lê a cadeia de ligação a partir de Settings.jsno ficheiro e atribui-a ao parâmetro de configuração:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Parse e validar os detalhes da ligação

1. Clique à direita no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **Common.cs**. Irá escrever código para validar os detalhes da ligação e criar uma tabela dentro desta classe.

1. Defina um método `CreateStorageAccountFromConnectionString` como mostrado abaixo. Este método analisará os detalhes da cadeia de ligação e validará que o nome da conta e os dados chave da conta fornecidos no ficheiro "Settings.json" são válidos.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Criar uma tabela 

A classe [CloudTableClient](/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) permite-lhe obter tabelas e entidades armazenadas no Table Storage. Como não temos tabelas na conta API da Tabela Cosmos, vamos adicionar o `CreateTableAsync` método à classe **Common.cs** para criar uma tabela:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Se obtém um erro de "exceção indisponíveis de serviço 503", é possível que as portas necessárias para o modo de conectividade sejam bloqueadas por uma firewall. Para corrigir este problema, abra as portas necessárias ou utilize a conectividade do modo gateway, como indicado no seguinte código:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definir a entidade 

As entidades mapeiam para objetos C# utilizando uma classe personalizada derivada da [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade.

Clique à direita no seu projeto **CosmosTableSamples**. **Selecione Adicionar**, **Nova Pasta** e nomeie-a como **Modelo**. Dentro da pasta Modelo adicione uma classe **denominada CustomerEntity.cs** e adicione-lhe o seguinte código.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Este código define uma classe de entidade que usa o primeiro nome do cliente como chave de linha e apelido como chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam-na de forma exclusiva na tabela. As entidades com a mesma chave de partição podem ser questionadas mais rapidamente do que as entidades com diferentes chaves de partição, mas a utilização de chaves de partição diversificadas permite uma maior escalabilidade de operações paralelas. As entidades a serem armazenadas em tabelas têm de ter um tipo suportado, por exemplo derivadas da classe [TableEntity](/dotnet/api/microsoft.azure.cosmos.table.tableentity). As propriedades de entidade que pretende armazenar numa tabela têm de ser propriedades públicas do tipo e suportar a introdução e a definição de valores. Além disso, o tipo de entidade tem de expor um construtor sem parâmetros.

## <a name="insert-or-merge-an-entity"></a>Inserir ou fundir uma entidade

O exemplo de código a seguir cria um objeto de entidade e adiciona-o à tabela. O método InsertOrMerge dentro da classe [TableOperation](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) é utilizado para inserir ou fundir uma entidade. O [ métodoCloudTable.Exebonito Async](/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync) é chamado para executar a operação. 

Clique à direita no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **SamplesUtils.cs**. Esta classe armazena todo o código necessário para a realização de operações CRUD nas entidades. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Obter uma entidade de uma partição

Pode obter a entidade a partir de uma partição utilizando o método Recuperar sob a classe [TableOperation.](/dotnet/api/microsoft.azure.cosmos.table.tableoperation) O exemplo de código a seguir obtém a chave de partição, o e-mail e o número de telefone de uma entidade cliente. Este exemplo também imprime as unidades de pedido consumidas para consulta da entidade. Para consultar uma entidade, apencha o seguinte código à **samplesUtils.cs** ficheiro:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente. Para eliminar uma entidade, apencha o seguinte código à **samplesUtils.cs** ficheiro: 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Executar as operações CRUD em dados de amostra

Depois de definir os métodos para criar entidades de mesa, inserção ou fusão, execute estes métodos nos dados da amostra. Para tal, clique em clique no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **BasicSamples.cs** e adicione-lhe o seguinte código. Este código cria uma tabela, acrescenta-lhe entidades.

Se não quiser eliminar a entidade e a mesa no final do projeto, comente os `await table.DeleteIfExistsAsync()` `SamplesUtils.DeleteEntityAsync(table, customer)` e métodos a partir do seguinte código. É melhor comentar estes métodos e validar os dados antes de apagar a tabela.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

O código anterior cria uma tabela que começa com "demo" e o GUID gerado é anexado ao nome da mesa. Em seguida, adiciona uma entidade cliente com o primeiro e último nome como "Harp Walter" e posteriormente atualiza o número de telefone deste utilizador. 

Neste tutorial, criou um código para realizar operações básicas da CRUD nos dados armazenados na conta API da tabela. Também pode efetuar operações avançadas, tais como – dados de inserção de lotes, consulta de todos os dados dentro de uma partição, consultar uma série de dados dentro de uma partição, Listar tabelas na conta cujos nomes começam com o prefixo especificado. Você pode baixar o formulário de amostra completo [azure-cosmos-table-dotnet-core-start-start-tory](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub. A classe [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/main/CosmosTableSamples/AdvancedSamples.cs) tem mais operações que pode realizar nos dados.  

## <a name="run-the-project"></a>Executar o projeto

Do seu projeto **CosmosTableSamples.** Abra a classe denominada **Programa.cs** e adicione-lhe o seguinte código para ligar para os Exemplos Básicos quando o projeto for executado.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Agora construa a solução e pressione F5 para executar o projeto. Quando o projeto for executado, verá a seguinte saída na solicitação de comando:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Saída do comando":::

Se receber um erro que diga Settings.jsno ficheiro não pode ser encontrado ao executar o projeto, pode resolvê-lo adicionando a seguinte entrada de XML nas definições do projeto. Clique no CosmosTableSamples, selecione CosmosTableSamples.csproj e adicione o seguinte itemGrupo: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Agora pode inscrever-se no portal Azure e verificar se os dados existem na tabela. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Resultados no portal":::

## <a name="next-steps"></a>Passos seguintes

Agora pode proceder ao próximo tutorial e aprender a migrar dados para a conta AZure Cosmos DB Table API. 

> [!div class="nextstepaction"]
>[Migrar dados para Azure Cosmos DB Table API](../cosmos-db/table-import.md)
