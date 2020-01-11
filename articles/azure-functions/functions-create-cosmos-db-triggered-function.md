---
title: Criar uma função acionada pelo Azure Cosmos DB
description: Utilize as Funções do Azure para criar uma função sem servidores que é invocada quando os dados são adicionados a uma base de dados no Azure Cosmos DB.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: quickstart
ms.date: 10/02/2018
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 830c7cdee247118ed24fc9b3a2a9efe8609c75d0
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863301"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Criar uma função acionada pelo Azure Cosmos DB

Saiba como criar uma função acionada quando os dados são adicionados ou alterados no Azure Cosmos DB. Para saber mais sobre o Azure Cosmos DB, veja [Azure Cosmos DB: Serverless database computing using Azure Functions (Azure Cosmos DB: computação de base de dados sem servidor com as Funções do Azure)](../cosmos-db/serverless-computing-database.md).

![Ver mensagem nos registos.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Tem de ter uma conta do Azure Cosmos DB que utilize a API SQL para poder criar o acionador.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Criar acionador do Azure Cosmos DB

1. Expanda a aplicação de funções e clique no botão **+** , junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Início rápido das funções no portal do Azure](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. No campo de pesquisa, escreva `cosmos` e escolha o modelo **Acionador do Azure Cosmos DB**.

1. Se solicitado, selecione **instalar** para instalar a extensão de Azure Cosmos DB no aplicativo de funções. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Configure o novo acionador com as definições, conforme especificado na tabela abaixo da imagem.

    ![Criar a função acionada do Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nome** | Predefinição | Utilize o nome de função predefinido sugerido pelo modelo.|
    | **Ligação de conta do Azure Cosmos DB** | Nova definição | Selecione **Novo** e, em seguida, escolha a sua **Subscrição**, a **Conta de base de dados** que criou anteriormente e **Selecionar**. Esta ação cria uma definição da aplicação para a ligação da sua conta. Esta definição é utilizada pelo enlace para a ligação à base de dados. |
    | **Nome do contêiner** | Itens | Nome do contêiner a ser monitorado. |
    | **Criar contêiner de concessão se ele não existir** | Assinalado | O contêiner ainda não existe, portanto, crie-o. |
    | **Nome da base de dados** | Tarefas | Nome do banco de dados com o contêiner a ser monitorado. |

1. Clique em **Criar** para criar a função acionada do Azure Cosmos DB. Assim que a função é criada, é apresentado o código de função baseado no modelo.  

    ![Modelo de função do Cosmos DB em C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Este modelo de função escreve o número de documentos e o primeiro ID de documento nos registos.

Em seguida, conecte-se à sua conta do Azure Cosmos DB e crie o contêiner `Items` no banco de dados `Tasks`.

## <a name="create-the-items-container"></a>Criar o contêiner de itens

1. Abra uma segunda instância do [portal do Azure](https://portal.azure.com) num novo separador no browser.

1. No lado esquerdo do portal, expanda a barra de ícones, escreva `cosmos` no campo de pesquisa e selecione **Azure Cosmos DB**.

    ![Procurar o serviço Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Escolha a sua conta do Azure Cosmos DB e, em seguida, selecione o **Data Explorer**. 

1. Em **API do SQL**, escolha banco de dados **tarefas** e selecione **novo contêiner**.

    ![Criar um contentor](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. Em **Adicionar contêiner**, use as configurações mostradas na tabela abaixo da imagem. 

    ![Definir o contêiner tarefas](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Definição|Valor sugerido|Descrição |
    | ---|---|--- |
    | **ID da Base de Dados** | Tarefas |O nome da base de dados nova. Isto deve corresponder ao nome definido no enlace de função. |
    | **ID do Contentor** | Itens | O nome do novo contêiner. Isto deve corresponder ao nome definido no enlace de função.  |
    | **[Chave de partição](../cosmos-db/partition-data.md)** | /categoria|Uma chave de partição que distribui uniformemente os dados para cada partição. A seleção da chave de partição correta é importante na criação de um contêiner de alto desempenho. | 
    | **Débito** |400 RU| Utilize o valor predefinido. Se pretender reduzir a latência, pode aumentar o débito mais tarde. |    

1. Clique em **OK** para criar o contêiner itens. Pode levar um curto tempo para que o contêiner seja criado.

Depois que o contêiner especificado na associação de função existir, você poderá testar a função adicionando itens a esse novo contêiner.

## <a name="test-the-function"></a>Testar a função

1. Expanda o novo contêiner **itens** em data Explorer, escolha **itens**e, em seguida, selecione **novo item**.

    ![Criar um item no contêiner itens](./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png)

1. Substitua o conteúdo do novo item pelo conteúdo a seguir e escolha **salvar**.

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
