---
title: Quickstart - Create Azure Cosmos DB recursos do portal Azure
description: Este quickstart mostra como criar uma base de dados, contentor e itens Azure Cosmos utilizando o portal Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 01/05/2020
ms.openlocfilehash: bc7e77cc498958b2f8f0c5b2d5ab2d59db97a235
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385419"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Quickstart: Criar uma conta Azure Cosmos, base de dados, contentores e itens do portal Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é um serviço de base de dados com vários modelos e de distribuição global da Microsoft. Você pode usar O Azure Cosmos DB para criar e consultar rapidamente bases de dados chave/valor, bases de dados de documentos e bases de dados de gráficos, que beneficiam da distribuição global e capacidades de escala horizontal no núcleo do Azure Cosmos DB. 

Este quickstart demonstra como usar o portal Azure para criar uma conta API Azure Cosmos DB [SQL,](sql-api-introduction.md) criar uma base de dados de documentos e um contentor, e adicionar dados ao recipiente. 

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição Azure ou conta experimental gratuita do Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adicione uma base de dados e um recipiente 

Pode utilizar o Data Explorer no portal Azure para criar uma base de dados e um contentor. 

1.  Selecione **Data Explorer** a partir da navegação esquerda na página da conta Azure Cosmos DB e, em seguida, selecione **Novo Recipiente**. 
    
    Pode ser necessário percorrer para ver a janela **Add Container.**
    
    ![O portal Azure Data Explorer, Adicionar painel de contentores](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  No painel do **recipiente Adicionar,** introduza as definições para o novo recipiente.
    
    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|ToDoList|Introduza o *ToDoList* como nome para a nova base de dados. Os nomes da base de dados devem conter de 1 a 255 caracteres, e não podem conter `/, \\, #, ?`, ou um espaço de fuga. Consulte a opção de entrada da base de **dados Provision,** permite-lhe partilhar a entrada disponibilizada na base de dados em todos os recipientes da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a entrada em 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID do contentor**|Itens|Introduza *itens* como o nome do seu novo recipiente. Os IDs de contentores têm os mesmos requisitos de caracteres que os nomes da base de dados.|
    |**Chave de partição**| /categoria| A amostra descrita neste artigo *utiliza/categoria* como chave de partição.|

    
    Não adicione **chaves Únicas** para este exemplo. As teclas únicas permitem-lhe adicionar uma camada de integridade de dados à base de dados, garantindo a singularidade de um ou mais valores por chave de partição. Para mais informações, consulte [as chaves Unique em Azure Cosmos DB](unique-keys.md).
    
1.  Selecione **OK**. O Data Explorer exibe a nova base de dados e o recipiente que criou.

## <a name="add-data-to-your-database"></a>Adicione dados à sua base de dados

Adicione dados à sua nova base de dados utilizando o Data Explorer.

1. No **Data Explorer,** expanda a base de dados **ToDoList** e expanda o recipiente **de itens.** Em seguida, selecione **Itens**, e, em seguida, selecione **New Item**. 
   
   ![Criar documentos novos no Data Explorer no portal do Azure](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
   
1. Adicione a seguinte estrutura ao documento do lado direito do painel de **documentos:**

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
   
   ![Copie em dados json e selecione Save in Data Explorer no portal Azure](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)
   
1. Selecione **novo Documento** novamente e crie e guarde outro documento com uma `id`única, e quaisquer outras propriedades e valores que desejar. Os seus documentos podem ter qualquer estrutura, porque a Azure Cosmos DB não impõe qualquer esquema aos seus dados.

## <a name="query-your-data"></a>Consultar os seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Se pretender eliminar apenas a base de dados e utilizar a conta Azure Cosmos no futuro, pode eliminar a base de dados com os seguintes passos:

* Cheguei à sua conta azure cosmos.
* Abra o **Data Explorer,** clique na base de dados que pretende eliminar e selecionar **eliminar base de dados**.
* Introduza o nome ID/base de dados da base de dados para confirmar o funcionamento da eliminação. 

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma conta Azure Cosmos DB, criar uma base de dados e um contentor usando o Data Explorer. Agora, pode importar dados adicionais para a sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB](import-data.md) (Importar dados para o Azure Cosmos DB).
