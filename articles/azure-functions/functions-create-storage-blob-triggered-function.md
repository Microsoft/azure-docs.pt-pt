---
title: Criar uma função no Azure disparada pelo armazenamento de BLOBs
description: Utilize as Funções do Azure para criar uma função sem servidores que é invocada por itens que são adicionados ao Armazenamento de blobs do Azure.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: quickstart
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 65717e4373f64ae38a324fd19624f049dba9dfb1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769307"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Criar uma função acionada pelo Armazenamento de blobs do Azure

Saiba como criar uma função que é acionada quando são carregados ou atualizados ficheiros no Armazenamento de blobs do Azure.

![Ver mensagem nos registos.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Transferir e instalar o [Microsoft Azure Storage Explorer](https://storageexplorer.com/).
+ Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar uma aplicação de Funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicação Function App criada com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

Em seguida, vai criar uma função na aplicação Function App nova.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Criar uma função acionada pelo Armazenamento de blobs

1. Expanda a aplicação de funções e clique no botão **+** , junto a **Funções**. Se esta for a primeira função na sua aplicação de funções, selecione **No portal** e **Continuar**. Caso contrário, avance para o passo três.

   ![Início rápido das funções no portal do Azure](./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e **Terminar e ver os modelos**.

    ![Início Rápido das funções, escolher mais modelos](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

1. No campo de pesquisa, escreva `blob` e escolha o modelo **Acionador de blob**.

1. Se solicitado, selecione **instalar** para instalar a extensão de armazenamento do Azure e quaisquer dependências no aplicativo de funções. Depois de instalar com êxito, selecione **Continuar**.

    ![Instalar as extensões de enlace](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

1. Utilize as definições especificadas na tabela abaixo da imagem.

    ![Crie a função acionada pelo Armazenamento de blobs.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Definição | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Exclusivo na aplicação Function App | Nome desta função acionada por blob. |
    | **Caminho**   | samples-workitems/{name}    | Localização no Armazenamento de blobs a ser monitorizado. O nome do ficheiro do blob é transmitido no enlace como o parâmetro _name_.  |
    | **Ligação da conta de armazenamento** | AzureWebJobsStorage | Pode utilizar a ligação da conta de armazenamento que já está a ser utilizada pela sua aplicação Function App ou criar uma nova.  |

1. Clique em **Criar** para criar a nova função.

Em seguida, vai ligar à sua conta de Armazenamento do Azure e criar o contentor **samples-workitems**.

## <a name="create-the-container"></a>Criar o contentor

1. Na sua função, clique em **Integrar**, expanda **Documentação** e copie **Nome da conta** e **Chave da conta**. Vai utilizar estas credenciais para ligar à conta de armazenamento. Se já tiver ligado a sua conta de armazenamento, avance para o passo 4.

    ![Obtenha as credenciais de ligação da conta de Armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Execute a ferramenta [Microsoft Azure Storage Explorer](https://storageexplorer.com/), clique no ícone de ligação à esquerda, escolha **Utilizar um nome e uma chave de conta de armazenamento** e clique em **Seguinte**.

    ![Execute a ferramenta Microsoft Azure Storage Explorer.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Introduza o **Nome da conta** e a **Chave da conta** do passo 1, clique em **Seguinte** e em **Ligar**. 

    ![Introduza as credenciais de armazenamento e ligue-se.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Expanda a conta de armazenamento anexada, clique com o botão direito do mouse em **contêineres de blob**, clique em **criar contêiner de blob**, digite `samples-workitems`e pressione Enter.

    ![Crie uma fila de armazenamento.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Agora que já tem um contentor de blobs, pode carregar um ficheiro para o mesmo para testar a função.

## <a name="test-the-function"></a>Testar a função

1. Novamente no portal do Azure, navegue para a função, expanda os **Registos**, na parte inferior da página, e confirme que a transmissão de registos não está em pausa.

1. Em Gerenciador de Armazenamento, expanda sua conta de armazenamento, **contêineres de blob**e **exemplos-WorkItems**. Clique em **Carregar** e em **Carregar ficheiros...** .

    ![Carregue um ficheiro para o contentor de blobs.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. Na caixa de diálogo **Carregar ficheiros**, clique no campo **Ficheiros**. Procure um ficheiro no computador local, como um ficheiro de imagem, selecione-o, clique em **Abrir** e em **Carregar**.

1. Volte para os registos da função e confirme que o blob foi lido.

   ![Ver mensagem nos registos.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Quando a aplicação Function App é executada no plano Consumo predefinido, poderá haver um atraso de vários minutos entre a inclusão ou atualização do blob e o acionamento da função. Se precisar de baixa latência nas suas funções acionadas por blobs, considere executar a sua aplicação Function App num plano do Serviço de Aplicações.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Criou uma função que é executada quando um blob é adicionado ou atualizado no Armazenamento de blobs. Para obter mais informações sobre os acionadores do Armazenamento de blobs, veja [Azure Functions Blob storage bindings](functions-bindings-storage-blob.md) (Enlaces do Armazenamento de blobs das Funções do Azure).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
