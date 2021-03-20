---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78942058"
---
O quadro seguinte descreve a capacidade, a escalabilidade e os objetivos de desempenho para o armazenamento da mesa.

| Recurso | Destino |
|----------|---------------|
| Número de tabelas numa conta de armazenamento Azure | Limitado apenas pela capacidade da conta de armazenamento |
| Número de divisórias numa tabela | Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades em partição | Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho máximo de uma única mesa | 500 TiB |
| Tamanho máximo de uma única entidade, incluindo todos os valores de propriedade | 1 MiB |
| Número máximo de imóveis numa entidade de mesa | 255 (incluindo as três propriedades do sistema, **PartitionKey,** **RowKey** e **Timestamp)** |
| Tamanho total máximo de uma propriedade individual numa entidade | Varia por tipo de propriedade. Para obter mais informações, consulte **os Tipos de Propriedade** na [Compreensão do Modelo de Dados de Serviço de Tabela.](/rest/api/storageservices/understanding-the-table-service-data-model) |
| Tamanho da **PartitionKey** | Uma corda até 1 KiB em tamanho |
| Tamanho do **RowKey** | Uma corda até 1 KiB em tamanho |
| Dimensão de uma transação de grupo de entidades | Uma transação pode incluir no máximo 100 entidades e a carga útil deve ser inferior a 4 MiB de tamanho. Uma transação de grupo de entidades pode incluir uma atualização para uma entidade apenas uma vez. |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de pedidos por conta de armazenamento | 20.000 transações por segundo, o que pressupõe um tamanho de entidade de 1 KiB |
| Produção-alvo para uma única divisória de tabela (1 entidades KiB) | Até 2.000 entidades por segundo |