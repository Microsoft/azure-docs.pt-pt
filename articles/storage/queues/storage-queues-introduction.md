---
title: Introdução às filas do Azure-armazenamento do Azure
description: Introdução às filas do Azure
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/07/2019
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: cbrooks
ms.openlocfilehash: 0e8bac8344bec06b58a22b8c9162cd8bd22ee700
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750431"
---
# <a name="what-are-azure-queues"></a>O que são as filas do Azure?

O Armazenamento de Filas do Azure é um serviço para alojar grandes quantidades de mensagens. Você acessa mensagens de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona.

## <a name="queue-service-concepts"></a>Conceitos de serviço Fila

O serviço Fila contém os seguintes componentes:

![Conceitos de fila](./media/storage-queues-introduction/queue1.png)

* **Formato do URL:** os ficheiros são endereçáveis com o seguinte formato de URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Conta de armazenamento:** Todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Para obter informações sobre a capacidade da conta de armazenamento, consulte [escalabilidade e metas de desempenho para contas de armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

* **Fila:** uma fila contém um conjunto de mensagens. O nome da fila **deve** estar em letras minúsculas. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Mensagem:** uma mensagem, em qualquer formato, até 64 KB. Antes da versão 2017-07-29, o tempo de vida máximo permitido é de sete dias. Para a versão 2017-07-29 ou posterior, o tempo de vida máximo pode ser qualquer número positivo ou-1 indicando que a mensagem não expira. Se esse parâmetro for omitido, o tempo de vida padrão será de sete dias.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de armazenamento](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Introdução às filas usando o .NET](storage-dotnet-how-to-use-queues.md)
