---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.custom: seo-python-october2019
ms.openlocfilehash: 23b93f507ef6abe19a0202b28afa31d28490b2b3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80151327"
---
## <a name="what-is-queue-storage"></a>O que é o armazenamento de fila?

O Armazenamento de Filas do Azure é um serviço para armazenar um grande número de mensagens que podem ser acedidas a partir de qualquer local no mundo através de chamadas autenticadas com HTTP ou HTTPS. Uma mensagem de fila única pode ter até 64 KB e uma fila pode conter milhões de mensagens, até ao limite da capacidade total de uma conta de armazenamento. O armazenamento de fila é frequentemente usado para criar um atraso de trabalho para processar assincronicamente.

## <a name="queue-service-concepts"></a>Conceitos de serviço de fila

O serviço De Fila Azure contém os seguintes componentes:

![Componentes de serviço de fila azure](./media/storage-queue-concepts-include/azure-queue-service-components.png)

* **Formato URL:** As filas são endereçadas utilizando`<storage account>`o seguinte formato URL: http:// .queue.core.windows.net/`<queue>`
  
    O seguinte URL endereça uma fila no diagrama:  
  
    `http://myaccount.queue.core.windows.net/incoming-orders`

* **Conta de Armazenamento:** todos os acessos ao Storage do Azure são efetuados através de uma conta de armazenamento. Para obter mais informações sobre contas de armazenamento, consulte a [visão geral da conta de armazenamento](../articles/storage/common/storage-account-overview.md).
* **Fila:** uma fila contém um conjunto de mensagens. Todas as mensagens têm de estar numa fila. Tenha em atenção que o nome da fila tem de estar todo em minúsculas. Para obter informações sobre a nomenclatura de filas, veja [Nomenclatura de Filas e Metadados](https://msdn.microsoft.com/library/azure/dd179349.aspx).
* **Mensagem:** uma mensagem, em qualquer formato, até 64 KB. O tempo máximo que uma mensagem pode permanecer na fila de espera é de 7 dias. Para a versão 2017-07-29 ou mais tarde, o tempo máximo de vida pode ser qualquer número positivo, ou -1 indicando que a mensagem não expira. Se este parâmetro for omitido, o tempo de vida padrão é de sete dias.

