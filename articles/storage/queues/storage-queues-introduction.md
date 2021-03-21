---
title: Introdução ao Armazenamento da Fila Azure - Armazenamento Azure
description: Consulte uma introdução ao Azure Queue Storage, um serviço para armazenar um grande número de mensagens. Um serviço de armazenamento de fila contém um formato URL, conta de armazenamento, fila e mensagem.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8c5c97fbb72934dd99ec784ccf8e08eec059c31b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590584"
---
# <a name="what-is-azure-queue-storage"></a>O que é o Armazenamento de Filas do Azure?

O Armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Acede a mensagens de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos.

## <a name="queue-storage-concepts"></a>Conceitos de armazenamento de fila

O armazenamento da fila contém os seguintes componentes:

![Diagrama mostrando a relação entre uma conta de armazenamento, filas e mensagens.](./media/storage-queues-introduction/queue1.png)

- **Formato do URL:** os ficheiros são endereçáveis com o seguinte formato de URL:

  `https://<storage account>.queue.core.windows.net/<queue>`

  O seguinte URL endereça uma fila no diagrama:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Conta de armazenamento:** Todo o acesso ao Azure Storage é feito através de uma conta de armazenamento. Para obter informações sobre a capacidade da conta de armazenamento, consulte [os objetivos de escalabilidade e desempenho para as contas de armazenamento padrão.](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

- **Fila:** uma fila contém um conjunto de mensagens. O nome da fila **deve** ser minúsculo. Para obter informações sobre o nome de filas, consulte [as filas de nomeação e metadados](/rest/api/storageservices/naming-queues-and-metadata).

- **Mensagem:** uma mensagem, em qualquer formato, até 64 KB. Antes da versão 2017-07-29, o tempo máximo de vida permitido é de sete dias. Para a versão 2017-07-29 ou posterior, o tempo máximo de vida pode ser qualquer número positivo, ou -1 indicando que a mensagem não expira. Se este parâmetro for omitido, o tempo de vida padrão é de sete dias.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Começa com o Armazenamento da Fila usando .NET](storage-dotnet-how-to-use-queues.md)
- [Começar com o armazenamento da fila usando Java](storage-java-how-to-use-queue-storage.md)
- [Começar com armazenamento de fila usando Python](storage-python-how-to-use-queue-storage.md)
- [Começa com o armazenamento da fila usando Node.js](storage-nodejs-how-to-use-queues.md)
