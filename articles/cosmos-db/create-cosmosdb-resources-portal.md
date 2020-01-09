---
title: Início rápido-criar Azure Cosmos DB recursos do portal do Azure
description: Este guia de início rápido mostra como criar um banco de dados, contêiner e itens do Azure Cosmos usando o portal do Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/02/2019
ms.openlocfilehash: 58fa891503342ac0ec9d41e9c458a71d18e58161
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445608"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Início rápido: criar uma conta do Azure Cosmos, um banco de dados, um contêiner e itens do portal do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente. Você pode usar Azure Cosmos DB para criar e consultar rapidamente bancos de dados de chave/valor, bancos de dados de documentos e bancos de dados de grafo, todos beneficiando-se dos recursos de escala horizontal e distribuição global no núcleo de Azure Cosmos DB. 

Este guia de início rápido demonstra como usar o portal do Azure para criar uma conta de [API do SQL](sql-api-introduction.md) do Azure Cosmos DB, criar um banco de dados de documentos e um contêiner e adicionar um dado ao contêiner. 

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure ou uma conta de avaliação gratuita Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adicionar um banco de dados e um contêiner 

Você pode usar o Data Explorer no portal do Azure para criar um banco de dados e um contêiner. 

1.  Selecione **Data Explorer** na barra de navegação à esquerda na página da sua conta do Azure Cosmos DB e, em seguida, selecione **novo contêiner**. 
    
    Talvez seja necessário rolar para a direita para ver a janela **Adicionar contêiner** .
    
    ![O Data Explorer no portal do Azure, painel Adicionar Contentor](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  No painel **Adicionar contêiner** , insira as configurações para o novo contêiner.
    
    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|ToDoList|Insira *ToDoList* como o nome do novo banco de dados. Nomes de base de dados tem de conter entre 1 e 255 carateres e não podem conter `/, \\, #, ?`, ou um espaço à direita. Marque a opção **provisionar taxa de transferência do banco de dados** , que permite que você compartilhe a taxa de transferência provisionada para o banco de dados em todos os contêineres no banco de dados. Essa opção também ajuda na economia de custos. |
    |**Débito**|400|Deixe a taxa de transferência em 400 unidades de solicitação por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID do Contentor**|Itens|Insira os *itens* como o nome do novo contêiner. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
    |**Chave de partição**| /categoria| O exemplo descrito neste artigo usa */Category* como a chave de partição.|

    
    Não adicione **chaves exclusivas** para este exemplo. Chaves exclusivas permitem que você adicione uma camada de integridade de dados ao banco de dado garantindo a exclusividade de um ou mais valores por chave de partição. Para obter mais informações, consulte [chaves exclusivas em Azure Cosmos DB](unique-keys.md).
    
1.  Selecione **OK**. O Data Explorer exibe o novo banco de dados e o contêiner que você criou.

## <a name="add-data-to-your-database"></a>Adicionar dados ao banco de dado

Adicione dados ao novo banco de dado usando Data Explorer.

1. Em **Data Explorer**, expanda o banco de dados **ToDoList** e expanda o contêiner **itens** . Em seguida, selecione **itens**e, em seguida, selecione **novo item**. 
   
   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Adicione a seguinte estrutura ao documento no lado direito do painel **documentos** :

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Selecione **Guardar**.
   
   ![Copie em dados JSON e selecione salvar em Data Explorer no portal do Azure](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Selecione **novo documento** novamente e crie e salve outro documento com um `id`exclusivo e quaisquer outras propriedades e valores desejados. Seus documentos podem ter qualquer estrutura, porque Azure Cosmos DB não impõe nenhum esquema em seus dados.

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a criar uma conta de Azure Cosmos DB, criar um banco de dados e um contêiner usando o Data Explorer. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).