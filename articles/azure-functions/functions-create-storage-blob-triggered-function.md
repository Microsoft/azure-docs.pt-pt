---
title: Criar uma função em Azure desencadeada pelo armazenamento Blob
description: Utilize funções Azure para criar uma função sem servidor que seja invocada por itens adicionados a um recipiente de armazenamento Blob.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83123127"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Crie uma função em Azure que é desencadeada pelo armazenamento blob

Saiba como criar uma função ativada quando os ficheiros são carregados ou atualizados num recipiente de armazenamento Blob.

## <a name="prerequisites"></a>Pré-requisitos

+ Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Criou com sucesso a sua nova aplicação de função.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="Aplicação Function App criada com êxito." border="true":::

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Criar uma função de armazenamento Azure Blob desencadeada

1. Selecione **Funções**e, em seguida, **selecione + Adicione** para adicionar uma nova função.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Escolha o modelo de **gatilho de armazenamento Azure Blob.**

1. Utilize as definições especificadas na tabela abaixo da imagem.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Aplicação Function App criada com êxito." border="true":::

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nova Função** | Exclusivo na aplicação Function App | Nome desta função acionada por blob. |
    | **Caminho**   | samples-workitems/{nome}    | Localização no Armazenamento de blobs a ser monitorizado. O nome do ficheiro do blob é transmitido no enlace como o parâmetro _name_.  |
    | **Ligação da conta de armazenamento** | AzureWebJobsStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |

1. Selecione **Criar Função** para criar a sua função.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Aplicação Function App criada com êxito." border="true":::

Em seguida, crie o recipiente **de amostras-workitems.**

## <a name="create-the-container"></a>Criar o contentor

1. Na sua função, na página **'Vista Geral',** selecione o seu grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Encontre e selecione a conta de armazenamento do seu grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Escolha **recipientes**e, em seguida, escolha **+ Recipiente**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. No campo **Nome,** `samples-workitems` escreva e, em seguida, selecione **Criar**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Aplicação Function App criada com êxito." border="true":::

Agora que já tem um contentor de blobs, pode carregar um ficheiro para o mesmo para testar a função.

## <a name="test-the-function"></a>Testar a função

1. Novamente no portal do Azure, navegue para a função, expanda os **Registos**, na parte inferior da página, e confirme que a transmissão de registos não está em pausa.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Numa janela separada do navegador, vá ao seu grupo de recursos no portal Azure e selecione a conta de armazenamento.

1. Selecione **recipientes**e, em seguida, selecione o recipiente **de trabalho de amostras.**

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Selecione **Upload**e, em seguida, selecione o ícone da pasta para escolher um ficheiro para carregar.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Aplicação Function App criada com êxito." border="true":::

1. Navegue por um ficheiro no seu computador local, como um ficheiro de imagem, escolha o ficheiro. Selecione **Abrir** e, em seguida, **carregar**.

1. Volte para os registos da função e confirme que o blob foi lido.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Aplicação Function App criada com êxito." border="true":::

    >[!NOTE]
    > Quando a aplicação Function App é executada no plano Consumo predefinido, poderá haver um atraso de vários minutos entre a inclusão ou atualização do blob e o acionamento da função. Se precisar de baixa latência nas suas funções acionadas por blobs, considere executar a sua aplicação Function App num plano do Serviço de Aplicações.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando um blob é adicionado ou atualizado no Armazenamento de blobs. Para obter mais informações sobre os acionadores do Armazenamento de blobs, veja [Azure Functions Blob storage bindings](functions-bindings-storage-blob.md) (Enlaces do Armazenamento de blobs das Funções do Azure).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
