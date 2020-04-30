---
title: 'Quickstart: Criar filas de armazenamento azure no portal'
description: Utilize o portal Azure para criar uma fila. Em seguida, use o portal Azure para adicionar uma mensagem, ver as propriedades da mensagem e desfilar a mensagem.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/06/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: 2876d2a680aa41372557a9f04c1d6a5eed56b8ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74269250"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Quickstart: Criar uma fila e adicionar uma mensagem com o portal Azure

Neste arranque rápido, aprende-se a usar o [portal Azure](https://portal.azure.com/) para criar uma fila no Armazenamento Azure e a adicionar e desfilar mensagens.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila no portal Azure, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu esquerdo para a conta de armazenamento, desloque-se para a secção de serviço de **fila** e, em seguida, selecione **Filas**.
3. Selecione o botão **+ Fila.**
4. Escreva um nome para a sua nova fila. O nome da fila deve ser minúsculo, deve começar com uma letra ou número, e pode incluir apenas letras, números e o caráter (-) do traço.
6. Selecione **OK** para criar a fila.

    ![Screenshot mostrando como criar uma fila no portal Azure](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Adicione uma mensagem

Em seguida, adicione uma mensagem à nova fila. Uma mensagem pode ter até 64 KB de tamanho.

1. Selecione a nova fila da lista de filas na conta de armazenamento.
1. Selecione o botão **+ Adicionar mensagem** para adicionar uma mensagem à fila. Introduza uma mensagem no campo de **texto da Mensagem.** 
1. Especifique quando a mensagem expirar. O tempo máximo que uma mensagem pode permanecer na fila é de 7 dias.
1. Indique se codifica a mensagem como Base64. Recomenda-se a codificação de dados binários.
1. Selecione o botão **OK** para adicionar a mensagem.

    ![Screenshot mostrando como adicionar uma mensagem a uma fila](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Ver propriedades da mensagem

Depois de adicionar uma mensagem, o portal Azure apresenta uma lista de todas as mensagens na fila. Pode visualizar o ID da mensagem, o conteúdo da mensagem, o tempo de inserção da mensagem e o tempo de validade da mensagem. Também pode ver quantas vezes esta mensagem foi desmarcada.

![Screenshot mostrando propriedades da mensagem](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Remover uma mensagem da fila

Pode desviar uma mensagem da frente da fila do portal Azure. Quando se desfila uma mensagem, a mensagem é apagada. 

Defilar remove sempre a mensagem mais antiga da fila. 

![Screenshot mostrando como desviar uma mensagem do portal](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma fila, adicionar uma mensagem, ver propriedades de mensagens e desfilar uma mensagem no portal Azure.

> [!div class="nextstepaction"]
> [O que são as filas azure?](storage-queues-introduction.md)
