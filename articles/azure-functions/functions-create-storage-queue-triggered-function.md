---
title: Criar uma função em Azure desencadeada por mensagens de fila
description: Utilize as Funções do Azure para criar uma função sem servidores que é invocada por mensagens que são submetidas para filas do Armazenamento do Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 98f0290aad9971bdb0c0b265d96e96d8ac34b99d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756515"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Criar uma função acionada pelo Armazenamento de filas do Azure

Saiba como criar uma função que é acionada quando são submetidas mensagens para uma fila do Armazenamento do Azure.

![Ver mensagem nos registos.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

- Transferir e instalar o [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Criar uma função acionada por Fila

1. Expanda a sua **+** aplicação de função e clique no botão ao lado das **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Início rápido das funções no portal do Azure](./media/functions-create-storage-queue-triggered-function/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

1. No campo de pesquisa, escreva `queue` e escolha o modelo **Acionador de fila**.

1. Se for solicitado, selecione **Instalar** para instalar a extensão de Armazenamento Azure e quaisquer dependências na aplicação de funções. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

1. Utilize as definições especificadas na tabela abaixo da imagem.

    ![Configure a função acionada pela fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Exclusivo na aplicação Function App | O nome desta função acionada por fila. |
    | **Nome da fila**   | myqueue-items    | O nome da fila à qual ligar na sua conta de Armazenamento. |
    | **Ligação da conta de armazenamento** | AzureWebJobsStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |    

1. Clique em **Criar** para criar a nova função.

Em seguida, vai ligar à sua conta de Armazenamento do Azure e criar a fila de armazenamento **myqueue-items**.

## <a name="create-the-queue"></a>Criar a fila

1. Na sua função, clique em **Integrar**, expanda **Documentação** e copie **Nome da conta** e **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento no Explorador de Armazenamento do Azure. Se já tiver ligado a sua conta de armazenamento, avance para o passo 4.

    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Execute a ferramenta [Microsoft Azure Storage Explorer](https://storageexplorer.com/), clique no ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave de conta de armazenamento** e clique em **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Introduza o **Nome da conta** e a **Chave da conta** do passo 1, clique em **Seguinte** e em **Ligar**.

    ![Introduza as credenciais de armazenamento e ligue-se.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda a conta de armazenamento anexada, clique `myqueue-items`nas **filas**de cliques à direita, clique em Criar **Fila,** tipo e, em seguida, prima introduzir.

    ![Crie uma fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Agora que tem uma fila de armazenamento, pode adicionar uma mensagem à mesma para testar a função.

## <a name="test-the-function"></a>Testar a função

1. De volta ao portal Azure, navegue para a sua função, expanda os **Registos** na parte inferior da página e certifique-se de que o streaming de registos não é interrompido.

1. No Storage Explorer, expanda a sua conta de armazenamento, **filas,** e **itens myqueue,** em seguida, clique em **Adicionar Mensagem**.

    ![Adicione uma mensagem à fila.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Escreva a sua mensagem “Hello World” em **Texto da mensagem** e clique em **OK**.

1. Aguarde alguns segundos, regresse aos registos da sua função e certifique-se de que a mensagem nova foi lida a partir da fila.

    ![Ver mensagem nos registos.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Novamente no Storage Explorer, clique em **Atualizar** e verifique se a mensagem foi processada e já não está na fila.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando uma mensagem é adicionada a uma fila de armazenamento. Para obter mais informações sobre os acionadores do Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure).

Agora que criou a sua primeira função, vamos adicionar uma ligação de saída à função que escreve uma mensagem de volta a outra fila.

> [!div class="nextstepaction"]
> [Add messages to an Azure Storage queue using Functions](functions-integrate-storage-queue-output-binding.md) (Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure)
