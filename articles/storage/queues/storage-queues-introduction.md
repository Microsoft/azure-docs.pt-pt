---
title: Introdução às Filas Azure - Azure Storage
description: Consulte uma introdução às Filas Azure, um serviço para armazenar um grande número de mensagens. Um serviço de fila contém um formato URL, conta de armazenamento, fila e mensagem.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: c16f02922b2e815ddd4986ab07bf260b20d871ee
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87826505"
---
# <a name="what-are-azure-queues"></a>O que são as filas do Azure?

O Armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Acede a mensagens de qualquer parte do mundo através de chamadas autenticadas utilizando HTTP ou HTTPS. Uma mensagem de fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até ao limite total de capacidade de uma conta de armazenamento. As filas são comumente usadas para criar um atraso de trabalho para processar assíncronos.

## <a name="queue-service-concepts"></a>Conceitos de serviço de fila

O serviço Fila contém os seguintes componentes:

![Diagrama mostrando a relação entre uma conta de armazenamento, filas e mensagens](./media/storage-queues-introduction/queue1.png)

* **Formato do URL:** os ficheiros são endereçáveis com o seguinte formato de URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Conta de armazenamento:** Todo o acesso ao Azure Storage é feito através de uma conta de armazenamento. Para obter informações sobre a capacidade da conta de armazenamento, consulte [os objetivos de escalabilidade e desempenho para as contas de armazenamento padrão.](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

* **Fila:** uma fila contém um conjunto de mensagens. O nome da fila **deve** ser minúsculo. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Mensagem:** uma mensagem, em qualquer formato, até 64 KB. Antes da versão 2017-07-29, o tempo máximo de vida permitido é de sete dias. Para a versão 2017-07-29 ou posterior, o tempo máximo de vida pode ser qualquer número positivo, ou -1 indicando que a mensagem não expira. Se este parâmetro for omitido, o tempo de vida padrão é de sete dias.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de armazenamento](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Começar com filas usando .NET](storage-dotnet-how-to-use-queues.md)
