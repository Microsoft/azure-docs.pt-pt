---
title: Início rápido do Azure-criar uma fila no armazenamento do Azure usando o portal do Azure | Microsoft Docs
description: Neste guia de início rápido, você usa o portal do Azure para criar uma fila. Em seguida, use a portal do Azure para adicionar uma mensagem, exibir as propriedades da mensagem e remover a mensagem da fila.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: d2f7b01c03b877a3dede496fc02f0d0cc9525cef
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721324"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Início rápido: Criar uma fila e adicionar uma mensagem com o portal do Azure

Neste guia de início rápido, você aprende a usar o [portal do Azure](https://portal.azure.com/) para criar uma fila no armazenamento do Azure e para adicionar e remover as mensagens da fila.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila no portal do Azure, siga estas etapas:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu à esquerda da conta de armazenamento, role até a seção **serviço fila** e, em seguida, selecione **filas**.
3. Selecione o botão **+ fila** .
4. Digite um nome para a nova fila. O nome da fila deve estar em letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere de traço (-).
6. Selecione **OK** para criar a fila.

    ![Captura de tela mostrando como criar uma fila no portal do Azure](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Adicionar uma mensagem

Em seguida, adicione uma mensagem à nova fila. Uma mensagem pode ter até 64 KB de tamanho.

1. Selecione a nova fila na lista de filas na conta de armazenamento.
1. Selecione o botão **+ Adicionar mensagem** para adicionar uma mensagem à fila. Insira uma mensagem no campo **texto da mensagem** . 
1. Especifique quando a mensagem expira. O tempo máximo que uma mensagem pode permanecer na fila é de 7 dias.
1. Indique se a mensagem deve ser codificada como Base64. É recomendável codificar dados binários.
1. Selecione o botão **OK** para adicionar a mensagem.

    ![Captura de tela mostrando como adicionar uma mensagem a uma fila](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Exibir Propriedades da mensagem

Depois de adicionar uma mensagem, o portal do Azure exibe uma lista de todas as mensagens na fila. Você pode exibir a ID da mensagem, o conteúdo da mensagem, a hora de inserção da mensagem e a hora de expiração da mensagem. Você também pode ver quantas vezes essa mensagem foi removida da fila.

![Captura de tela mostrando Propriedades da mensagem](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Remover uma mensagem da fila

Você pode remover da fila uma mensagem da frente da fila da portal do Azure. Quando você remover uma mensagem da fila, a mensagem será excluída. 

A remoção da fila sempre remove a mensagem mais antiga na fila. 

![Captura de tela mostrando como remover uma mensagem da fila do portal](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você aprendeu como criar uma fila, adicionar uma mensagem, exibir propriedades de mensagem e remover uma mensagem da fila na portal do Azure.

> [!div class="nextstepaction"]
> [O que são filas do Azure?](storage-queues-introduction.md)
