---
title: Criar uma função acionada pelo Azure Cosmos DB
description: Utilize as Funções do Azure para criar uma função sem servidores que é invocada quando os dados são adicionados a uma base de dados no Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: c16bd728fe81796d671762615ec8dc4ad6e1d87d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123774"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Criar uma função acionada pelo Azure Cosmos DB

Saiba como criar uma função acionada quando os dados são adicionados ou alterados no Azure Cosmos DB. Para saber mais sobre o Azure Cosmos DB, veja [Azure Cosmos DB: Serverless database computing using Azure Functions (Azure Cosmos DB: computação de base de dados sem servidor com as Funções do Azure)](../cosmos-db/serverless-computing-database.md).

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Código DB Azure Cosmos":::

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta Azure.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Tem de ter uma conta do Azure Cosmos DB que utilize a API SQL para poder criar o acionador.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Criar acionador do Azure Cosmos DB

1. Na sua aplicação de funções, selecione **Funções** do menu esquerdo e, em seguida, selecione **Adicionar** a partir do menu superior. 

1. Na página **New Function,** introduza no campo de `cosmos` pesquisa e, em seguida, escolha o modelo de gatilho **Azure Cosmos DB.**

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Página de funções no portal Azure":::


1. Configure o novo gatilho com as definições especificadas no quadro seguinte:

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nova Função** | Aceitar o nome padrão | O nome da função. |
    | **Ligação à conta do Cosmos DB** | Aceite o novo nome padrão | Selecione **New**, a **Conta base** de dados que criou anteriormente e, em seguida, **OK**. Esta ação cria uma definição de aplicação para a ligação da sua conta. Esta definição é utilizada pelo enlace para a ligação à base de dados. |
    | **Nome da base de dados** | Tarefas | Nome da base de dados que inclui a recolha a ser monitorizada. |
    | **Nome da coleção** | Itens | Nome da coleção a ser monitorizada. |
    | **Nome da coleção para concessões** | leases | Nome da coleção para armazenar os arrendamentos. |
    | **Criar coleção de arrendamento se não existir** | Sim | Verifica a existência da coleção de arrendamento e cria-a automaticamente. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Criar a função acionada do Azure Cosmos DB":::

1. Selecione **Criar Função**. 

    Azure cria a função de gatilho Cosmos DB.

1. Para visualizar o código de função baseado no modelo, selecione **Código + Teste**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="Modelo de função do Cosmos DB em C#":::

    Este modelo de função escreve o número de documentos e o primeiro ID de documento nos registos.

Em seguida, ligue-se à sua conta Azure Cosmos DB e crie o recipiente na base de `Items` `Tasks` dados.

## <a name="create-the-items-container"></a>Criar o recipiente de itens

1. Abra uma segunda instância do [portal do Azure](https://portal.azure.com) num novo separador no browser.

1. No lado esquerdo do portal, expanda a barra de ícones, escreva `cosmos` no campo de pesquisa e selecione **Azure Cosmos DB**.

    ![Procurar o serviço Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Escolha a sua conta do Azure Cosmos DB e, em seguida, selecione o **Data Explorer**. 

1. Em **SQL API,** escolha a base de dados **de Tarefas** e selecione **Novo Recipiente**.

    ![Criar um contentor](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. No **Recipiente adicionar,** utilize as definições mostradas na tabela abaixo da imagem. 

    ![Definir o recipiente de tarefas](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Definição|Valor sugerido|Descrição |
    | ---|---|--- |
    | **ID da Base de Dados** | Tarefas |O nome da base de dados nova. Isto deve corresponder ao nome definido no enlace de função. |
    | **ID do contentor** | Itens | O nome do novo contentor. Isto deve corresponder ao nome definido no enlace de função.  |
    | **[Chave de partição](../cosmos-db/partition-data.md)** | /categoria|Uma chave de partição que distribui uniformemente os dados para cada partição. A seleção da chave de partição correta é importante para a criação de um recipiente performante. | 
    | **Débito** |400 RU| Utilize o valor predefinido. Se pretender reduzir a latência, pode aumentar o débito mais tarde. |    

1. Clique **em OK** para criar o recipiente itens. Pode levar pouco tempo para o recipiente ser criado.

Depois de existir o recipiente especificado na ligação de função, pode testar a função adicionando itens a este novo recipiente.

## <a name="test-the-function"></a>Testar a função

1. Expanda o novo recipiente de **itens** no Data Explorer, escolha **itens**e, em seguida, selecione **New Item**.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Criar um item no recipiente de itens":::

1. Substitua o conteúdo do novo item pelo seguinte conteúdo e, em seguida, escolha **Guardar**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Mude para o primeiro separador do browser que contém a função no portal. Expanda os registos de função e certifique-se de que o novo documento acionou a função. Veja se o valor do ID do documento `task1` está escrito nos registos. 

    ![Ver mensagem nos registos.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Opcional) Volte para o seu documento, faça uma alteração e clique em **Atualizar**. Em seguida, volte para os registos de função e certifique-se de que a atualização também acionou a função.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Foi criada com uma função que é executada quando um documento é adicionado ou modificado no seu Azure Cosmos DB. Para obter mais informações sobre os acionadores do Azure Cosmos DB, veja [Enlaces do Cosmos DB das Funções do Azure](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
