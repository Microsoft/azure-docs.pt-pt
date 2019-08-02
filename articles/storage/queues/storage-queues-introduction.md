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
ms.openlocfilehash: 67e4874fcca93633140b7630ceadd273d1646f86
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721179"
---
# <a name="what-are-azure-queues"></a>O que são as Filas do Azure?

O armazenamento de filas do Azure é um serviço para armazenar grandes números de mensagens. Você acessa mensagens de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma mensagem da fila pode ter até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

## <a name="common-uses"></a>Usos comuns

Utilizações comuns do Armazenamento de filas:

* Criação de um registo de tarefas pendentes do trabalho para processamento de forma assíncrona
* Transmissão de mensagens de uma função da Web do Azure para uma função de trabalho do Azure

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

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma conta de armazenamento](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Introdução às filas usando o .NET](storage-dotnet-how-to-use-queues.md)
