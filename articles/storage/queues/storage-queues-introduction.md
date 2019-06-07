---
title: Introdução às filas do Azure - armazenamento do Azure
description: Introdução às filas do Azure
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: overview
ms.date: 06/07/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: bc3045e3d3b6977555818fcdb3dcaf3246ebd200
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754804"
---
# <a name="what-are-azure-queues"></a>O que são as Filas do Azure?

Armazenamento de filas do Azure é um serviço para armazenar grandes quantidades de mensagens. Acessar as mensagens de qualquer lugar no mundo através de chamadas autenticadas, utilizando HTTP ou HTTPS. Uma mensagem de fila pode ser até 64 KB de tamanho. Uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

## <a name="common-uses"></a>Utilizações comuns

Utilizações comuns do Armazenamento de filas:

* Criação de um registo de tarefas pendentes do trabalho para processamento de forma assíncrona
* Transmissão de mensagens de uma função da Web do Azure para uma função de trabalho do Azure

## <a name="queue-service-concepts"></a>Conceitos do serviço fila

O serviço Fila contém os seguintes componentes:

![Conceitos de fila](./media/storage-queues-introduction/queue1.png)

* **Formato de URL:** Ficheiros são endereçáveis através do formato de URL seguinte:

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **Conta de armazenamento:** Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) para obter detalhes acerca da capacidade das contas de armazenamento.

* **Fila:** Uma fila contém um conjunto de mensagens. O nome da fila **tem** estar todo em minúsculas. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).

* **mensagem:** Uma mensagem, em qualquer formato, de até 64 KB. Antes da versão 07-29 de 2017, o tempo de vida máximo permitido é de sete dias. Para a versão 2017-07-29 ou posterior, o máximo tempo de vida pode ser qualquer número positivo ou -1 que indica que a mensagem não expire. Se este parâmetro for omitido, a predefinição time-to-live é de sete dias.

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma conta de armazenamento](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Introdução às filas com .NET](storage-dotnet-how-to-use-queues.md)
