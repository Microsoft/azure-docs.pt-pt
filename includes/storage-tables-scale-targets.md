---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942058"
---
A tabela seguinte descreve os objetivos de capacidade, escalabilidade e desempenho para o armazenamento de Tabelas.

| Recurso | Destino |
|----------|---------------|
| Número de tabelas numa conta de armazenamento do Azure | Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições numa tabela | Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades numa partição | Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho máximo de uma tabela única | 500 TiB |
| Tamanho máximo de uma entidade única, incluindo todos os valores de propriedade | 1 MiB |
| Número máximo de propriedades numa entidade de tabela | 255 (incluindo três propriedades do sistema, **PartitionKey**, **RowKey** e **Timestamp**) |
| Tamanho total máximo de uma propriedade individual numa entidade | Varia consoante o tipo de propriedade. Para obter mais informações, veja **Property Types** (Tipos de Propriedade) em [Understanding the Table Service Data Model](/rest/api/storageservices/understanding-the-table-service-data-model) (Noções Básicas sobre o Modelo de Dados do Serviço Tabela). |
| Tamanho de **PartitionKey** | Uma cadeia de tamanho até 1 KiB |
| Tamanho de **RowKey** | Uma cadeia de tamanho até 1 KiB |
| Tamanho de uma transação do grupo de entidades | Uma transação pode incluir no máximo 100 entidades e o payload tem de ser inferior a 4 MiB. Uma transação do grupo de entidades só pode incluir uma atualização a uma entidade. |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de pedidos por conta de armazenamento | 20 000 transações por segundo, que assume um tamanho de entidade de 1 KiB |
| Débito de destino para uma partição de uma única tabela (entidades de 1 KiB) | Até 2000 entidades por segundo |