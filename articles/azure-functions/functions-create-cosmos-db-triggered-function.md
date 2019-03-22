---
title: Criar uma função acionada pelo Azure Cosmos DB | Microsoft Docs
description: Utilize as Funções do Azure para criar uma função sem servidores que é invocada quando os dados são adicionados a uma base de dados no Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 941a35084ba811e3bf9224087336db9abbd5b5d5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137849"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Criar uma função acionada pelo Azure Cosmos DB

Saiba como criar uma função acionada quando os dados são adicionados ou alterados no Azure Cosmos DB. Para saber mais sobre o Azure Cosmos DB, veja [do Azure Cosmos DB: Computação de base de dados sem servidor com funções do Azure](../cosmos-db/serverless-computing-database.md).

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

1. Expanda a aplicação de funções e clique no botão **+**, junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Página de início rápido das funções no portal do Azure](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. No campo de pesquisa, escreva `cosmos` e escolha o modelo **Acionador do Azure Cosmos DB**.

1. Se lhe for pedido, selecione **instalar** para instalar a extensão do Azure Cosmos DB na function app. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Configure o novo acionador com as definições, conforme especificado na tabela abaixo da imagem.

    ![Criar a função acionada do Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Definição      | Valor sugerido  | Descrição                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nome** | Predefinição | Utilize o nome de função predefinido sugerido pelo modelo.|
    | **Ligação de conta do Azure Cosmos DB** | Nova definição | Selecione **Nova** e, em seguida, escolha a sua **Subscrição**, a **Conta de base de dados** que criou anteriormente e **Selecionar**. Esta ação cria uma definição da aplicação para a ligação da sua conta. Esta definição é utilizada pelo enlace para a ligação à base de dados. |
    | **Nome da coleção** | Itens | Nome da coleção a ser monitorizada. |
    | **Criar coleção de concessão, se não existir** | Assinalado | A coleção ainda não existe, como tal, deve ser criada. |
    | **Nome da base de dados** | Tarefas | Nome da base de dados com a coleção a ser monitorizada. |

1. Clique em **Criar** para criar a função acionada do Azure Cosmos DB. Assim que a função é criada, é apresentado o código de função baseado no modelo.  

    ![Modelo de função do Cosmos DB em C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Este modelo de função escreve o número de documentos e o primeiro ID de documento nos registos.

Em seguida, ligue-se à sua conta do Azure Cosmos DB e crie a `Items`coleção`Tasks` na base de dados.

## <a name="create-the-items-collection"></a>Criar coleção de Itens

1. Abra uma segunda instância do [portal do Azure](https://portal.azure.com) num novo separador no browser.

1. No lado esquerdo do portal, expanda a barra de ícones, escreva `cosmos` no campo de pesquisa e selecione **Azure Cosmos DB**.

    ![Procurar o serviço Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Escolha a sua conta do Azure Cosmos DB e, em seguida, selecione o **Data Explorer**. 

1. Em **Coleções**, escolha **taskDatabase** e selecione **Nova Coleção**.

    ![Criar uma coleção](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

1. Em **Adicionar Coleção**, utilize as definições apresentadas na tabela abaixo da imagem. 

    ![Definir o taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)

    | Definição|Valor sugerido|Descrição |
    | ---|---|--- |
    | **ID da Base de Dados** | Tarefas |O nome da base de dados nova. Isto deve corresponder ao nome definido no enlace de função. |
    | **ID da Coleção** | Itens | O nome da coleção nova. Isto deve corresponder ao nome definido no enlace de função.  |
    | **Capacidade de armazenamento** | Fixa (10 GB)|Utilize o valor predefinido. Este valor é a capacidade de armazenamento da base de dados. |
    | **Débito** |400 RU| Utilize o valor predefinido. Se pretender reduzir a latência, pode aumentar o débito mais tarde. |
    | **[Chave de partição](../cosmos-db/partition-data.md)** | /categoria|Uma chave de partição que distribui uniformemente os dados para cada partição. É importante selecionar a chave de partição correta para criar coleções com bom desempenho. | 

1. Clique em **OK** para criar a coleção Itens. Pode demorar um curto período de tempo para a coleção ser criada.

Depois de a coleção especificada no enlace da função existir, pode testar a função ao adicionar documentos a esta nova coleção.

## <a name="test-the-function"></a>Testar a função

1. Expanda a nova coleção **taskCollection** no Data Explorer, escolha **Documentos** e, em seguida, selecione **Novo Documento**.

    ![Criar um documento no taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

1. Substitua o conteúdo do novo documento pelo seguinte conteúdo e, em seguida, escolha **Guardar**.

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

## <a name="next-steps"></a>Passos Seguintes

Foi criada com uma função que é executada quando um documento é adicionado ou modificado no seu Azure Cosmos DB. Para obter mais informações sobre os acionadores do Azure Cosmos DB, veja [Enlaces do Cosmos DB das Funções do Azure](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
