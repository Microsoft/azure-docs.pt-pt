---
title: 'Quickstart: Table API com .NET - Azure Cosmos DB'
description: Este guia de introdução mostra como utilizar a API de Tabela do Azure Cosmos DB para criar uma aplicação com o portal do Azure e .NET
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 629adfe558aec71e156e50c75aa0891eac5a8bcf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240171"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Quickstart: Construa uma app Table API com .NET SDK e Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Nó.js](create-table-nodejs.md)
> * [Pitão](create-table-python.md)
>  

Este guia de introdução mostra como utilizar o .NET e a [API de Tabela](table-introduction.md) do Azure Cosmos DB para criar uma aplicação através da clonagem de um exemplo do GitHub. Este guia de introdução mostra também como criar uma conta do Azure Cosmos DB e como utilizar o Data Explorer para criar tabelas e entidades no portal do Azure baseado na Web.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não tiver o Visual Studio 2019 instalado, pode descarregar e utilizar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) **gratuito.** Confirme que ativa o **desenvolvimento do Azure** durante a configuração do Visual Studio.

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

## <a name="open-the-sample-application-in-visual-studio"></a>Abra a aplicação de exemplo no Visual Studio

1. No Visual Studio, a partir do menu **ficheiro**, escolha **Abrir** e, em seguida, escolha **Projeto/Solução**. 

   ![Abra a solução](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Navegue para a pasta onde clonou a aplicação da amostra e abra o ficheiro TableStorage.sLN.

## <a name="update-your-connection-string"></a>Atualizar a cadeia de ligação

Agora, regresse ao portal do Azure para obter as informações da cadeia de ligação e copie-as para a aplicação. Isto permite à aplicação comunicar com a base de dados alojada. 

1. No [portal do Azure](https://portal.azure.com/), clique em **Cadeia de ligação**. Utilize o botão de cópia à direita da janela, para copiar a **CADEIA DE LIGAÇÃO PRIMÁRIA**.

   ![Ver e copiar a CADEIA DE LIGAÇÃO PRIMÁRIA no painel Cadeia de Ligação](./media/create-table-dotnet/connection-string.png)

2. No Estúdio Visual, abra o ficheiro **Definições.json.** 

3. Colar a CADEIA DE **LIGAÇÃO PRIMÁRIA** DO portal para o valor De StorageConnectionString. Cole a cadeia dentro de aspas.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Prima CTRL+S para guardar o ficheiro **Definições.json.**

Atualizou agora a sua aplicação com todas as informações necessárias para comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação

1. No Estúdio Visual, clique à direita no projeto **CosmosTableSamples** no **Solution Explorer** e, em seguida, clique em **Gerir pacotes NuGet**. 

   ![Gerir Pacotes NuGet](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. Na caixa NuGet **Browse,** escreva Microsoft.Azure.Cosmos.Table. Esta ação encontrará a biblioteca de cliente da API da Tabela do Cosmos DB. Note que esta biblioteca está atualmente disponível para .NET Framework e .NET Standard. 
   
   ![Separador NuGet Browse](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Clique **em Instalar** para instalar a biblioteca **Microsoft.Azure.Cosmos.Table.** Esta ação instala o pacote da API de Tabela do Azure Cosmos DB e todas as dependências do mesmo.

4. Quando executa toda a aplicação, os dados da amostra são inseridos na entidade da tabela e eliminados no final para que não veja nenhum dado inserido se executar toda a amostra. No entanto, pode inserir alguns pontos de rutura para visualizar os dados. Abra BasicSamples.cs ficheiro e clique à direita na linha 52, selecione **Breakpoint**e, em seguida, **selecione Insert Breakpoint**. Insira outro ponto de interrupção na linha 55.

   ![Adicionar um ponto de interrupção](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Prima F5 para executar a aplicação. A janela da consola exibe o nome da nova base de dados de tabelas (neste caso, demoa13b1) em Azure Cosmos DB. 
    
   ![Saída da consola](media/create-table-dotnet/azure-cosmosdb-console.png)

   Quando atingir o primeiro ponto de interrupção, volte para Data Explorer no portal do Azure. Clique no botão **Atualizar**, expanda a tabela de demonstração* e clique em **Entidades**. O separador **Entidades** no lado direito apresenta a nova entidade que foi adicionada para Walter Harp. Tenha em atenção que o número de telefone para a nova entidade 425-555-0101.

   ![Nova entidade](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Se receber um erro que diga que o ficheiro Definições.json não pode ser encontrado ao executar o projeto, pode resolvê-lo adicionando a seguinte entrada XML nas definições do projeto. Clique à direita no CosmosTableSamples, selecione Editar CosmosTableSamples.csproj e adicione o seguinte itemGroup: 

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

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, aprendeu a criar uma conta do Azure Cosmos DB, a criar uma tabela com o Data Explorer e a executar uma aplicação.  Agora, pode consultar os dados com a API de Tabela.  

> [!div class="nextstepaction"]
> [Importar dados da tabela para a API de Tabela](table-import.md)

