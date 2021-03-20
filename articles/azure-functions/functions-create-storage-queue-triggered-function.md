---
title: Criar uma função em Azure desencadeada por mensagens de fila
description: Utilize funções Azure para criar uma função sem servidor que seja invocada por uma mensagem submetida a uma fila em Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90981583"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Criar uma função acionada pelo Armazenamento de filas do Azure

Saiba como criar uma função que é acionada quando são submetidas mensagens para uma fila do Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="Aplicação de função criada com sucesso.." border="true":::

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Criar uma função acionada por Fila

1. Selecione **Funções** e, em seguida, **selecione + Adicione** para adicionar uma nova função.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Escolha um modelo de função no portal Azure." border="true":::

1. Escolha o modelo de **gatilho de armazenamento da fila Azure.**

1. Utilize as definições especificadas na tabela abaixo da imagem.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Nome e configuração da função de armazenamento de fila desencadeada." border="true":::


    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Exclusivo na aplicação Function App | O nome desta função acionada por fila. |
    | **Nome da fila**   | myqueue-items    | O nome da fila à qual ligar na sua conta de Armazenamento. |
    | **Ligação da conta de armazenamento** | AzureWebJobsStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |    

1. Selecione **Criar Função** para criar a sua função.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Crie a função de armazenamento de fila acionada." border="true":::

Em seguida, ligue-se à sua conta de armazenamento Azure e crie a fila de armazenamento **de itens myqueue.**

## <a name="create-the-queue"></a>Criar a fila

1. Na sua função, na página **'Vista Geral',** selecione o seu grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Selecione o seu grupo de recursos do portal Azure." border="true":::

1. Encontre e selecione a conta de armazenamento do seu grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Aceda à conta de armazenamento." border="true":::

1. Escolha **filas** e, em seguida, escolha **+ fila.** 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Adicione uma fila à sua conta de armazenamento no portal Azure." border="true":::

1. No campo **Nome,** `myqueue-items` escreva e, em seguida, selecione **Criar**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Diga o nome do recipiente de armazenamento da fila." border="true":::

Agora que tem uma fila de armazenamento, pode adicionar uma mensagem à mesma para testar a função.

## <a name="test-the-function"></a>Testar a função

1. Novamente no portal do Azure, navegue para a função, expanda os **Registos**, na parte inferior da página, e confirme que a transmissão de registos não está em pausa.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Expandir o registo no portal Azure." border="true":::

1. Numa janela separada do navegador, vá ao seu grupo de recursos no portal Azure e selecione a conta de armazenamento.

1. Selecione **as filas** e, em seguida, selecione o recipiente **myqueue-items.**

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Vá à sua fila de artigos de itens no portal Azure." border="true":::

1. **Selecione Adicionar mensagem** e escreva "Olá Mundo!" no **texto de mensagem**. Selecione **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="A screenshot mostra o botão de mensagem Adicionar selecionado e o campo de texto de mensagem realçado." border="true":::

1. Aguarde alguns segundos, regresse aos registos da sua função e certifique-se de que a mensagem nova foi lida a partir da fila.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Ver mensagem nos registos." border="true":::

1. De volta à sua fila de armazenamento, **selecione Refresh** e verifique se a mensagem foi processada e já não está na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando uma mensagem é adicionada a uma fila de armazenamento. Para obter mais informações sobre os acionadores do Armazenamento de filas, veja [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Enlaces da fila de Armazenamento das Funções do Azure).

Agora que criou a sua primeira função, vamos adicionar uma ligação de saída à função que escreve uma mensagem de volta para outra fila.

> [!div class="nextstepaction"]
> [Add messages to an Azure Storage queue using Functions](functions-integrate-storage-queue-output-binding.md) (Utilizar as Funções para adicionar mensagens a uma fila do Armazenamento do Azure)
