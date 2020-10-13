---
title: Use comandos e funcionalidades de cadernos incorporados em cadernos Azure Cosmos DB C# (pré-visualização)
description: Aprenda a usar comandos e funcionalidades incorporados para fazer operações comuns utilizando os cadernos C# incorporados da Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 47ac6b4caf97b264df58732f8d031fc63091171e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89019271"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Use comandos e funcionalidades de cadernos incorporados em cadernos Azure Cosmos DB C# (pré-visualização)

Os cadernos Jupyter incorporados em Azure Cosmos DB permitem-lhe analisar e visualizar os seus dados a partir do portal Azure. Este artigo descreve como usar comandos e funcionalidades de cadernos incorporados para fazer operações comuns em cadernos C#.

## <a name="install-a-new-nuget-package"></a>Instale um novo pacote NuGet
Depois de ativar o suporte de um portátil para as suas contas Azure Cosmos, pode abrir um novo caderno e instalar um pacote.

Numa nova célula de código, insira e execute o seguinte código, ``PackageToBeInstalled`` substituindo-o pelo pacote NuGet pretendido, e ``optionalVersion`` por uma versão específica da embalagem, se desejar. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Pode instalar vários pacotes NuGet na mesma célula. Os pacotes estarão disponíveis para usar em qualquer caderno no espaço de trabalho da conta Azure Cosmos. 

Atualmente, o espaço de trabalho dos cadernos C# não suporta a resolução recursiva dos pacotes NuGet. Se um pacote NuGet tiver dependências de outros pacotes NuGet que não estão atualmente instalados, tem de os referenciar explicitamente juntamente com o pacote-mãe.

> [!TIP]
> Se o seu caderno necessitar de um pacote personalizado, recomendamos que adicione uma célula ao seu caderno para instalar a embalagem e torná-la a primeira célula. Isto reduz a probabilidade de conflitos com outros pacotes que a Azure Cosmos DB carrega por padrão. Também é fácil reinstalar as embalagens se [repor o espaço de trabalho](#reset-notebooks-workspace), que remove todas as embalagens. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Utilize o Azure Cosmos DB .NET SDK incorporado
A versão 3 do [Azure Cosmos DB .NET SDK para a SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) está instalada e incluída no ambiente de cadernos para a conta Azure Cosmos.

Criar uma instância ``CosmosClient`` para executar qualquer operação SDK. 

Por exemplo:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Para saber mais, consulte as [amostras .NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> O Azure Cosmos DB .NET SDK incorporado é suportado apenas para contas API SQL (Core). Para outras APIs, terá de [instalar o controlador .NET relevante](#install-a-new-nuget-package) que corresponda à API. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Definir opções personalizadas usando ``CosmosClientOptions``
Para maior flexibilidade, pode definir a propriedade personalizada ``CosmosClientOptions`` e passá-la no seu ``CosmosClient`` caso. Você pode usar esta propriedade para:

- Desa estaladie um nome de aplicação no sufixo do agente de utilizador para incluí-lo em cada pedido.
- Desacorda a região preferida a utilizar quando estiver a executar operações contra o serviço.
- Desaça uma política de relemisão personalizada para lidar com pedidos limitados de tarifas.

Consulte o artigo [de referência da API da CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) para todas as definições suportadas. Segue-se um exemplo que mostra como definir `cosmosClientOptions` a propriedade:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Aceda ao ponto final da conta e às principais variáveis-chave
Pode aceder ao ponto final incorporado e à chave da conta Azure Cosmos onde existe o seu caderno.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> As ``Cosmos.Key`` ``Cosmos.Endpoint`` variáveis e variáveis só são aplicáveis à API SQL. Para outras APIs, encontre o ponto final e a chave na lâmina **de Ligação ou** **Teclas** na sua conta Azure Cosmos.  

## <a name="print-console-output-in-c-code"></a>Imprimir saída da consola em código C#
No seu código C#, pode utilizar a sintaxe Display.AsMarkdown() com [interpolação de cordas](/dotnet/csharp/language-reference/tokens/interpolated) para imprimir a saída da consola que aparecerá quando executar a célula. 

Por exemplo: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> A sintaxe Console.WriteLine não é suportada atualmente em cadernos C#. Use o Display.AsMarkdown para imprimir a saída da consola a partir do seu programa. 

## <a name="use-built-in-nteract-data-explorer"></a>Utilize o explorador de dados nteract incorporado
Pode utilizar o explorador de [dados nteract](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) incorporado para filtrar e visualizar uma coleção de itens. Numa célula, coloque a variável que pretende visualizar na última linha, que é exibida automaticamente no nteract quando corre a célula.

Por exemplo, no exemplo *GetingStarted_Csharp.ipynb,* podemos imprimir a variável com o nosso resultado, o ``telemetryEvents`` . Consulte o [caderno GettingStarted_Csharp.ipynb](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) para toda a amostra. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Célula de consulta de Csharp":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="Célula de consulta de Csharp":::

## <a name="use-built-in-dictionary-viewer"></a>Use o espectador de dicionário incorporado
Você pode usar o espectador de dicionário incorporado para ver uma variável. Numa célula, coloque a variável que pretende visualizar na última linha, que será exibida automaticamente quando a célula é executada.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Célula de consulta de Csharp":::

## <a name="upload-json-items-to-a-container"></a>Faça o upload de itens JSON para um recipiente
Pode utilizar o ``%%upload`` comando mágico para enviar dados de um ficheiro JSON para um recipiente Azure Cosmos especificado. Utilize o seguinte comando para carregar os itens:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Substitua ``{database_id}`` e pelo nome da base de ``{container_id}`` dados e do recipiente na sua conta Azure Cosmos. 
- ``{url_location_of_file}``Substitua-o pela localização do seu ficheiro JSON. O ficheiro deve ser um conjunto de objetos JSON válidos e deve ser acessível através da Internet pública.

Por exemplo:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Com as estatísticas de saída, pode calcular os RU/s eficazes utilizados para carregar os itens. Por exemplo, se 25.000 RUs foram consumidos ao longo de 38 segundos, o RU/s eficaz é de 25.000 RUs / 38 segundos = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Executar outro caderno no caderno atual 
Pode utilizar o ``%%run`` comando mágico para executar outro caderno no seu espaço de trabalho a partir do seu caderno atual. Utilize a sintaxe:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
``{notebookName}``Substitua-o pelo nome do caderno que pretende executar. O caderno deve estar no seu atual espaço de trabalho 'Os Meus Cadernos'. 

## <a name="reset-notebooks-workspace"></a>Redefinir espaço de trabalho de cadernos
Para redefinir o espaço de trabalho dos blocos de notas para as definições predefinidos, selecione **Reset Workspace** na barra de comando. Isto removerá quaisquer pacotes instalados personalizados e reiniciará o servidor Jupyter. Os seus cadernos, ficheiros e recursos da Azure Cosmos não serão afetados.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Célula de consulta de Csharp":::

## <a name="next-steps"></a>Passos seguintes

- Conheça os benefícios dos [cadernos Azure Cosmos DB Jupyter](cosmosdb-jupyter-notebooks.md)
- Conheça o [Azure Cosmos DB .NET SDK para API SQL](https://github.com/Azure/azure-cosmos-dotnet-v3)
