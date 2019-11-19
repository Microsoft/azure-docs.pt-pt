---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 24e755edbb18e85bcaa97435dee27418c1457221
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174998"
---
## <a name="what-is-queue-storage"></a>O que é o armazenamento de filas?

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento. O armazenamento de filas é geralmente usado para criar uma pendência de trabalho para processar de forma assíncrona.

## <a name="queue-service-concepts"></a>Conceitos de serviço Fila

O serviço Fila do Azure contém os seguintes componentes:

![Componentes do serviço Fila do Azure](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formato do URL:** os ficheiros são endereçáveis com o seguinte formato de URL:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **Conta de Armazenamento:** todos os acessos ao Storage do Azure são efetuados através de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../articles/storage/common/storage-scalability-targets.md) para obter detalhes acerca da capacidade das contas de armazenamento.
* **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens têm de estar numa fila. Tenha em atenção que o nome da fila tem de estar todo em minúsculas. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Mensagem:** uma mensagem, em qualquer formato, até 64 KB. O tempo máximo que uma mensagem pode permanecer na fila de espera é de 7 dias. Para a versão 2017-07-29 ou posterior, o tempo de vida máximo pode ser qualquer número positivo ou-1 indicando que a mensagem não expira. Se esse parâmetro for omitido, o tempo de vida padrão será de sete dias.

