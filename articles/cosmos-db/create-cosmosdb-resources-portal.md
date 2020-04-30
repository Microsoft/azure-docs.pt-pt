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
ms.openlocfilehash: 79deb2f33a11e8ccb6f059bde7590b7cc0fe20c0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80521084"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Quickstart: Criar uma conta Azure Cosmos, base de dados, contentores e itens do portal Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é um serviço de bases de dados com vários modelos e distribuído globalmente da Microsoft. Você pode usar O Azure Cosmos DB para criar e consultar rapidamente bases de dados chave/valor, bases de dados de documentos e bases de dados de gráficos, que beneficiam da distribuição global e capacidades de escala horizontal no núcleo do Azure Cosmos DB. 

Este quickstart demonstra como usar o portal Azure para criar uma conta API Azure Cosmos DB [SQL,](sql-api-introduction.md) criar uma base de dados de documentos e um contentor, e adicionar dados ao recipiente. 

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição Azure ou conta experimental gratuita do Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Aceda ao [portal do Azure](https://portal.azure.com/) para criar um conta do Azure Cosmos DB. Procure e selecione **Azure Cosmos DB**.

   ![O painel da base de dados do portal do Azure](./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png)

1. Selecione **Adicionar**.
1. Na página **Criar Conta do Azure Cosmos DB**, introduza as definições básicas da nova conta do Azure Cosmos DB. 

    |Definição|Valor|Descrição |
    |---|---|---|
    |Subscrição|Nome da subscrição|Selecione a subscrição do Azure que quer utilizar para esta conta do Azure Cosmos. |
    |Grupo de Recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo** e, em seguida, introduza um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Introduza um nome para identificar a conta do Azure Cosmos. Uma vez que *documents.azure.com* é anexado ao nome que indicar para criar o URI, utilize um nome exclusivo.<br><br>O nome só pode conter letras minúsculas, números e o caráter de hífen (-). Tem de ter entre 3 e 31 carateres.|
    |API|O tipo de conta a criar|Selecione **Núcleo (SQL)** para criar uma base de dados de documentos e consultar com a sintaxe SQL. <br><br>A API determina o tipo de conta a criar. A Azure Cosmos DB fornece cinco APIs: Core (SQL) e MongoDB para dados de documentos, Gremlin para dados de gráficos, Tabela Azure e Cassandra. De momento, deve criar uma conta separada para cada API. <br><br>[Saiba mais sobre a API SQL](introduction.md).|
    |Aplicar Desconto de Nível Livre|Aplicar ou não me candidatar|Com o nível gratuito Azure Cosmos DB, você receberá gratuitamente os primeiros 400 RU/s e 5 GB de armazenamento numa conta. Saiba mais sobre [o nível livre.](https://azure.microsoft.com/pricing/details/cosmos-db/)|
    |Localização|A região mais próxima dos seus utilizadores|Selecione a localização geográfica para alojar a sua conta do Azure Cosmos DB. Utilize a localização mais próxima dos utilizadores para lhes dar o acesso mais rápido aos dados.|
    |Tipo de conta|Produção ou Não Produção|Selecione **Produção** se a conta for utilizada para uma carga de trabalho de produção. Selecione **Não Produção** se a conta for utilizada para a não produção, por exemplo, desenvolvimento, teste, QA ou encenação. Trata-se de uma definição de etiqueta de recurso Azure que afina a experiência do Portal, mas não afeta a conta DB do Azure Cosmos subjacente. Pode alterar este valor a qualquer momento.|


> [!NOTE]
> Pode ter até um nível gratuito de conta Azure Cosmos DB por subscrição azure e deve optar por entrar na criação da conta. Se não vir a opção de aplicar o desconto de nível livre, isto significa que outra conta na subscrição já foi ativada com nível livre.
   
   ![A página da nova conta do Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail.png)

1. Selecione **Rever + criar**. Pode ignorar as secções **Rede** e **Etiquetas**.

1. Reveja as definições da conta e, em seguida, selecione **Criar**. A criação da conta demora alguns minutos. Aguarde até que a página do portal apresente **A implementação está concluída**. 

    ![O painel de Notificações de portal do Azure](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png)

1. Selecione **Ir para recurso** para aceder à página da conta do Azure Cosmos DB. 

    ![A página da conta do Azure Cosmos DB](./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png)

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adicione uma base de dados e um recipiente 

Pode utilizar o Data Explorer no portal Azure para criar uma base de dados e um contentor. 

1.  Selecione **Data Explorer** a partir da navegação esquerda na página da conta Azure Cosmos DB e, em seguida, selecione **Novo Recipiente**. 
    
    Pode ser necessário percorrer para ver a janela **Add Container.**
    
    ![O Data Explorer no portal do Azure, painel Adicionar Contentor](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)
    
1.  No painel do **recipiente Adicionar,** introduza as definições para o novo recipiente.
    
    |Definição|Valor sugerido|Descrição
    |---|---|---|
    |**ID da Base de Dados**|ToDoList|Introduza o *ToDoList* como nome para a nova base de dados. Os nomes da base de dados devem conter `/, \\, #, ?`de 1 a 255 caracteres, e não podem conter, ou um espaço de fuga. Consulte a opção de entrada da base de **dados Provision,** permite-lhe partilhar a entrada disponibilizada na base de dados em todos os recipientes da base de dados. Esta opção também ajuda na poupança de custos. |
    |**Débito**|400|Deixe a entrada em 400 unidades de pedido por segundo (RU/s). Se pretender reduzir a latência, pode aumentar o débito mais tarde.| 
    |**ID do contentor**|Itens|Introduza *itens* como o nome do seu novo recipiente. Os IDs dos contentores têm os mesmos requisitos em termos de carateres que os nomes das bases de dados.|
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
   
1. Selecione **novo Documento** novamente e crie e guarde outro documento com uma única, `id`e quaisquer outras propriedades e valores que desejar. Os seus documentos podem ter qualquer estrutura, porque a Azure Cosmos DB não impõe qualquer esquema aos seus dados.

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
