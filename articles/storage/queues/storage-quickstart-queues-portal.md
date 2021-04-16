---
title: 'Quickstart: Criar filas de armazenamento Azure no portal'
description: Utilize o portal Azure para criar uma fila. Em seguida, utilize o portal Azure para adicionar uma mensagem, ver as propriedades da mensagem e desaquezar a mensagem.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- mode-portal
ms.openlocfilehash: f0e7a5a514f2d68fce025ea9fb354f29fa068561
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534389"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Quickstart: Criar uma fila e adicionar uma mensagem com o portal Azure

Neste arranque rápido, aprende-se a usar o [portal Azure](https://portal.azure.com/) para criar uma fila no Azure Storage e para adicionar e descaír mensagens.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila no portal Azure, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu esquerdo para a conta de armazenamento, percorra a secção **de Armazenamento de Fila** e, em seguida, selecione As **filas**.
3. Selecione o botão **+ Fila.**
4. Escreva um nome para a sua nova fila. O nome da fila deve ser minúsculo, deve começar com uma letra ou número, e pode incluir apenas letras, números e o caráter (-) traço.
6. Selecione **OK** para criar a fila.

    ![Screenshot mostrando como criar uma fila no portal Azure](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Adicionar uma mensagem

Em seguida, adicione uma mensagem à nova fila. Uma mensagem pode ter até 64 KB de tamanho.

1. Selecione a nova fila da lista de filas na conta de armazenamento.
1. Selecione o botão **+ Adicionar mensagem** para adicionar uma mensagem à fila. Introduza uma mensagem no campo **de texto 'Mensagem'.**
1. Especificar quando a mensagem expira. Os valores válidos que podem ser **introduzidos nos Expirações no** campo são entre 1 segundo e 7 dias. Selecione **A Mensagem nunca expira** para indicar uma mensagem que permanecerá na fila até que seja explicitamente removida.
1. Indique se deve codificar a mensagem como Base64. Recomenda-se a codificação de dados binários.
1. Selecione o botão **OK** para adicionar a mensagem.

    ![Screenshot mostrando como adicionar uma mensagem a uma fila](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Ver propriedades de mensagens

Depois de adicionar uma mensagem, o portal Azure apresenta uma lista de todas as mensagens na fila. Pode visualizar o ID da mensagem, o conteúdo da mensagem, o tempo de inserção da mensagem e o tempo de validade da mensagem. Também pode ver quantas vezes esta mensagem foi descoduada.

![Screenshot mostrando propriedades de mensagens](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Remover uma mensagem da fila

Pode descoduar uma mensagem na parte da frente da fila a partir do portal Azure. Quando descodiona uma mensagem, a mensagem é apagada.

A dequeueção remove sempre a mensagem mais antiga da fila.

![Screenshot mostrando como desquecção de uma mensagem do portal](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a criar uma fila, adicionar uma mensagem, ver propriedades de mensagens e desaquio uma mensagem no portal Azure.

> [!div class="nextstepaction"]
> [O que é o Armazenamento de Filas do Azure?](storage-queues-introduction.md)
