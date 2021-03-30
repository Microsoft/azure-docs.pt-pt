---
title: 'Quickstart: Tabela API com .NET - Azure Cosmos DB'
description: Este guia de introdução mostra como utilizar a API de Tabela do Azure Cosmos DB para criar uma aplicação com o portal do Azure e .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096942"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Quickstart: Construa uma aplicação API de tabela com .NET SDK e Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Este guia de introdução mostra como utilizar o .NET e a [API de Tabela](table-introduction.md) do Azure Cosmos DB para criar uma aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução mostra também como criar uma conta do Azure Cosmos DB e como utilizar o Data Explorer para criar tabelas e entidades no portal do Azure baseado na Web.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2019 instalado,  pode baixar e utilizar a [Edição Comunitária visual 2019 gratuita do Visual Studio 2019.](https://www.visualstudio.com/downloads/) Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de base de dados

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Adicionar uma tabela

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Clonar a aplicação de exemplo

Agora, vamos clonar uma aplicação de Tabela a partir do GitHub, definir a cadeia de ligação e executá-la. Vai ver como é fácil trabalhar com dados programaticamente. 

1. Abra uma linha de comandos, crie uma nova pasta designada git-samples e, em seguida, feche a linha de comandos.

   ```bash
   md "C:\git-samples"
   ```

2. Abra uma janela de terminal do git, como o git bash e utilize o comando `cd` para alterar para uma nova pasta e instalar a aplicação de exemplo.

   ```bash
   cd "C:\git-samples"
   ```

3. Execute o seguinte comando para clonar o repositório de exemplo. Este comando cria uma cópia da aplicação de exemplo no seu computador.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> Para obter uma passagem mais detalhada de código semelhante, consulte o artigo de amostra da [Tabela Cosmos DB API.](./tutorial-develop-table-dotnet.md)

## <a name="open-the-sample-application-in-visual-studio"></a>Abra a aplicação de exemplo no Visual Studio

1. No Visual Studio, a partir do menu **ficheiro**, escolha **Abrir** e, em seguida, escolha **Projeto/Solução**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="Abra a solução"::: 

2. Navegue para a pasta onde clonou o pedido de amostra e abra o ficheiro tableStorage.sln.

## <a name="review-the-code"></a>Rever o código

Este passo é opcional. Se estiver interessado em aprender de que forma os recursos da base de dados são criados no código, pode consultar os seguintes fragmentos. Caso contrário, pode adiantar-se para atualizar a secção de cadeias de [ligação](#update-your-connection-string) deste doc.

* O seguinte código mostra como criar uma tabela dentro do Azure Storage:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* O seguinte código mostra como inserir dados na tabela:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* O seguinte código mostra como consultar os dados da tabela:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* O seguinte código mostra como eliminar dados da tabela:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite à aplicação comunicar com a base de dados alojada. 

1. No [portal do Azure](https://portal.azure.com/), clique em **Cadeia de ligação**. Utilize o botão de cópia à direita da janela, para copiar a **CADEIA DE LIGAÇÃO PRIMÁRIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Ver e copiar a CADEIA DE LIGAÇÃO PRIMÁRIA no painel Cadeia de Ligação":::

2. No Visual Studio, abra a **Settings.jsem** arquivo. 

3. Cole a **CADEIA DE LIGAÇÃO PRIMÁRIA** do portal para o valor StorageConnectionString. Cole a cadeia dentro de aspas.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Prima CTRL+S para guardar o **Settings.jsno** ficheiro.

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação

1. No Visual Studio, clique à direita no projeto **CosmosTableSamples** no **Solution Explorer** e, em seguida, clique em **Gerir Pacotes NuGet**. 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="Gerir pacotes nuget":::

2. Na caixa NuGet **Browse, digite** Microsoft.Azure.Cosmos.Table. Esta ação encontrará a biblioteca de cliente da API da Tabela do Cosmos DB. Note que esta biblioteca está atualmente disponível para .NET Framework e .NET Standard. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="Separador NuGet Browse":::

3. Clique **em Instalar** para instalar a biblioteca **Microsoft.Azure.Cosmos.Table.** Esta ação instala o pacote da API de Tabela do Azure Cosmos DB e todas as dependências do mesmo.

4. Quando executam toda a aplicação, os dados da amostra são inseridos na entidade da tabela e eliminados no final para que não veja nenhum dado inserido se executar toda a amostra. No entanto, pode inserir alguns pontos de rutura para visualizar os dados. Abra basicSamples.cs ficheiro e clique com o botão direito na linha 52, selecione **Breakpoint** e, em seguida, **selecione Insira Breakpoint**. Insira outro ponto de interrupção na linha 55.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Adicionar um ponto de interrupção"::: 

5. Prima F5 para executar a aplicação. A janela da consola apresenta o nome da nova base de dados de tabelas (neste caso, demoa13b1) em Azure Cosmos DB. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Saída da consola":::

   Quando atingir o primeiro ponto de interrupção, volte para Data Explorer no portal do Azure. Clique no botão **Atualizar**, expanda a tabela de demonstração* e clique em **Entidades**. O separador **Entidades** no lado direito apresenta a nova entidade que foi adicionada para Walter Harp. Tenha em atenção que o número de telefone para a nova entidade 425-555-0101.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Nova entidade":::
    
   Se receber um erro que diga Settings.jsno ficheiro não pode ser encontrado ao executar o projeto, pode resolvê-lo adicionando a seguinte entrada de XML nas definições do projeto. Clique no CosmosTableSamples, selecione CosmosTableSamples.csproj e adicione o seguinte itemGrupo: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Feche o separador **Entidades** no Data Explorer.
    
7. Prima F5 para executar a aplicação para o ponto de interrupção seguinte. 

    Quando atingir o ponto de interrupção, mude novamente para o portal do Azure, clique em **Entidades** novamente para abrir o separador **Entidades** e tenha em atenção que o número de telefone foi atualizado para 425-555-0105.

8. Prima F5 para executar a aplicação. 
 
   A aplicação adiciona entidades para utilização numa aplicação de exemplo avançado que a API de tabela não suporta atualmente. A aplicação, em seguida, elimina a tabela criada pela aplicação de exemplo.

9. Na janela da consola, prima Enter para terminar a execução da aplicação. 
  

## <a name="review-slas-in-the-azure-portal"></a>Rever os SLAs no portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma tabela com o Data Explorer e a executar uma aplicação.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados da tabela para a API de Tabela](table-import.md)