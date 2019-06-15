---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8a596293a5c1572b30ea6101dad16328c8db2634
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159796"
---
## <a name="what-is-queue-storage"></a>O que é o Armazenamento de Filas?
O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento.

Utilizações comuns do Armazenamento de filas:

* Criação de um registo de tarefas pendentes do trabalho para processamento de forma assíncrona
* Transmissão de mensagens de uma função da Web do Azure para uma função de trabalho do Azure

## <a name="queue-service-concepts"></a>Conceitos do Serviço Fila
O serviço Fila contém os seguintes componentes:

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **Formato de URL:** Ficheiros são endereçáveis através do formato de URL seguinte:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Conta de armazenamento:** Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../articles/storage/common/storage-scalability-targets.md) para obter detalhes acerca da capacidade das contas de armazenamento.
* **Fila:** Uma fila contém um conjunto de mensagens. Todas as mensagens têm de estar numa fila. Tenha em atenção que o nome da fila tem de estar todo em minúsculas. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **mensagem:** Uma mensagem, em qualquer formato, de até 64 KB. O tempo máximo que uma mensagem pode permanecer na fila de espera é de 7 dias.

