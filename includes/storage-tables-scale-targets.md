---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/20/2019
ms.author: tamram
ms.openlocfilehash: 5ab03b682dd0ed1dc7b198e89c86e7a74c6275cd
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67457489"
---
| Resource | Destino |
|----------|---------------|
| Tamanho máximo de uma única tabela | 500 TiB |
| Tamanho máximo de uma entidade de tabela | 1 MiB |
| Número máximo de propriedades em uma entidade de tabela | 255, que inclui três propriedades do sistema: PartitionKey, RowKey e timestamp |
| Tamanho total máximo dos valores de propriedade em uma entidade | 1 MiB |
| Tamanho total máximo de uma propriedade individual em uma entidade | Varia por tipo de propriedade. Para obter mais informações, consulte **tipos de propriedade** em [noções básicas sobre o modelo de dados do serviço tabela](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de pedidos por conta de armazenamento | 20.000 transações por segundo, que assumem um tamanho de entidade de 1 KiB |
| Taxa de transferência de destino para uma única partição de tabela (1 KiB-Entities) | Até 2.000 entidades por segundo |