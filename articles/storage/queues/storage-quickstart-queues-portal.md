---
title: Início rápido do Azure - criar uma fila no armazenamento do Azure no portal do Azure | Documentos da Microsoft
description: Neste início rápido, utilize o portal do Azure para criar uma fila. Em seguida, utilizar o portal do Azure para adicionar uma mensagem, ver propriedades da mensagem e retirar a mensagem da fila.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/06/2019
ms.author: tamram
ms.openlocfilehash: 8a5dba5f949d5c20e0fbf752caac4125b8aecd52
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726248"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Início rápido: Criar uma fila e adicione uma mensagem com o portal do Azure

Neste início rápido, irá aprender a utilizar o [portal do Azure](https://portal.azure.com/) para criar uma fila no armazenamento do Azure e para adicionar e remover mensagens da fila.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila no portal do Azure, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.
2. No menu da esquerda para a conta de armazenamento, desloque-se para o **serviço fila** secção, em seguida, selecione **filas**.
3. Selecione o **+ fila** botão.
4. Escreva um nome para a sua nova fila. O nome da fila tem de estar em minúsculo, tem de começar com uma letra ou número e pode incluir apenas letras, números e o caráter de travessão (-).
6. Selecione **OK** para criar a fila.

    ![Captura de ecrã que mostra como criar uma fila no portal do Azure](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Adicione uma mensagem

Em seguida, adicione uma mensagem para a nova fila. Uma mensagem pode ser até 64 KB de tamanho.

1. Selecione a nova fila a partir da lista de filas na conta de armazenamento.
1. Selecione o **+ Adicionar mensagem** botão para adicionar uma mensagem à fila. Introduza uma mensagem na **mensagem de texto** campo. 
1. Especifique quando expira a mensagem. O tempo máximo que uma mensagem pode permanecer na fila é de 7 dias.
1. Indica se a codificar a mensagem como Base64. Recomenda-se a codificação de dados binários
1. Selecione o **OK** botão para adicionar a mensagem.

    ![Captura de ecrã que mostra como adicionar uma mensagem numa fila](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Ver as propriedades de mensagem

Depois de adicionar uma mensagem, o portal do Azure apresenta uma lista de todas as mensagens na fila. Pode ver o ID da mensagem, o conteúdo da mensagem, a mensagem de tempo de inserção e a hora de expiração de mensagem. Também pode ver o número de vezes que esta mensagem foi retirada da fila.

![Captura de ecrã que mostra as propriedades da mensagem](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Remover uma mensagem da fila

Pode remover da fila de uma mensagem da frente da fila do portal do Azure. Quando remover da fila de uma mensagem, a mensagem é eliminada. 

Retirar da fila sempre remove a mensagem mais antiga na fila. 

![Captura de ecrã que mostra como uma mensagem a partir do portal de remoções da fila](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a criar uma fila, adicione uma mensagem, ver propriedades da mensagem e tirar da fila de uma mensagem no portal do Azure.

> [!div class="nextstepaction"]
> [Quais são as filas do Azure?](storage-queues-introduction.md)
