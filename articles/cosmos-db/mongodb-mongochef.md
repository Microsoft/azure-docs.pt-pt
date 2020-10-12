---
title: Use o Studio 3T para ligar à API da Azure Cosmos DB para a MongoDB
description: Aprenda a ligar-se à API de um API da Azure Cosmos para a MongoDB usando o Studio 3T.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 18a9f97d2cee9dd17345a1c8c0ae0efe442d79a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85799399"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>Ligue-se a uma conta Azure Cosmos usando o Studio 3T

Para ligar a API de um API da Azure Cosmos para MongoDB usando o Studio 3T, você deve:

* Descarregue e instale [o Studio 3T](https://studio3t.com/).
* Tenha a [informação](connect-mongodb-account.md) da sua conta Azure Cosmos.

## <a name="create-the-connection-in-studio-3t"></a>Criar a ligação no Studio 3T

Para adicionar a sua conta Azure Cosmos ao gestor de conexão Studio 3T, utilize os seguintes passos:

1. Recupere as informações de ligação para a sua conta Azure Cosmos DB para a conta MongoDB utilizando as instruções da [aplicação Connect a MongoDB para o artigo DB da Azure Cosmos.](connect-mongodb-account.md)

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="Screenshot da página de cadeia de ligação":::

2. Clique **em Ligar** para abrir o Gestor de Ligação e, em seguida, clique em Nova **Ligação**

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="Screenshot da página de cadeia de ligação":::
3. Na janela **New Connection,** no separador **Servidor,** introduza o HOST (FQDN) da conta Azure Cosmos e do PORT.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="Screenshot da página de cadeia de ligação":::
4. Na janela **Nova Ligação,** no separador **Autenticação,** escolha o Modo de Autenticação **Básico (MONGODB-CR ou SCARM-SHA-1)** e introduza o USERNAME e o PASSWORD.  Aceite a autenticação padrão db (administrador) ou forneça o seu próprio valor.

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="Screenshot da página de cadeia de ligação":::
5. Na janela **New Connection,** no separador **SSL,** verifique o **protocolo Use SSL para ligar a** caixa de verificação e o botão de rádio de **certificados SSL auto-assinado do servidor Aceitar.**

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="Screenshot da página de cadeia de ligação":::
6. Clique no botão **'Ligação de Teste'** para validar as informações de ligação, clique em **OK** para voltar à janela Nova Ligação e, em seguida, clique em **Guardar**.

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="Screenshot da página de cadeia de ligação":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Use o Studio 3T para criar uma base de dados, recolha e documentos
Para criar uma base de dados, recolha e documentos utilizando o Studio 3T, execute os seguintes passos:

1. Em **Connection Manager**, realce a ligação e clique em **Connect**.

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="Screenshot da página de cadeia de ligação":::
2. Clique com o botão direito no anfitrião e escolha **Add Database**.  Forneça um nome de base de dados e clique **em OK**.

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="Screenshot da página de cadeia de ligação":::
3. Clique com o botão direito na base de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar.**

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="Screenshot da página de cadeia de ligação":::
4. Clique no item do menu **Coleção** e, em seguida, clique em **Adicionar Documento.**

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="Screenshot da página de cadeia de ligação" },
        "isRegistered": false
    }
    ```

7. Execute uma consulta de amostra. Por exemplo, procure famílias com o apelido 'Andersen' e devolva os pais e os campos do Estado.

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="Screenshot da página de cadeia de ligação":::

## <a name="next-steps"></a>Passos seguintes

- Aprenda a [usar Robo 3T](mongodb-robomongo.md) com API da Azure Cosmos DB para a MongoDB.
- Explore [as amostras](mongodb-samples.md) do MongoDB com a API da Azure Cosmos para a MongoDB.
