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
ms.openlocfilehash: 86bbff167a2653fd8d89b566b551c4c53dd3614e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124555"
---
# <a name="what-are-azure-queues"></a>O que são filas do Azure?

O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens. Você acessa mensagens de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. As filas são normalmente usadas para criar uma lista de pendências de trabalho para processar de forma assíncrona.

## <a name="queue-service-concepts"></a>Conceitos de serviço Fila

O serviço Fila contém os seguintes componentes:

![Conceitos de fila](./media/storage-queues-introduction/queue1.png)

* **Formato da URL:** As filas são endereçáveis usando o seguinte formato de URL:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Conta de armazenamento:** Todo o acesso ao armazenamento do Azure é feito por meio de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) para obter detalhes acerca da capacidade das contas de armazenamento.

* **Espera** Uma fila contém um conjunto de mensagens. O nome da fila **deve** estar em letras minúsculas. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **Mensagem:** Uma mensagem, em qualquer formato, de até 64 KB. Antes da versão 2017-07-29, o tempo de vida máximo permitido é de sete dias. Para a versão 2017-07-29 ou posterior, o tempo de vida máximo pode ser qualquer número positivo ou-1 indicando que a mensagem não expira. Se esse parâmetro for omitido, o tempo de vida padrão será de sete dias.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de armazenamento](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Introdução às filas usando o .NET](storage-dotnet-how-to-use-queues.md)
