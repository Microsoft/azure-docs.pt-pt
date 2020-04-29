---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78942058"
---
A tabela que se segue descreve a capacidade, a escalabilidade e os objetivos de desempenho para o armazenamento de mesas.

| Recurso | Destino |
|----------|---------------|
| Número de tabelas numa conta de armazenamento Azure | Limitado apenas pela capacidade da conta de armazenamento |
| Número de divisórias em uma mesa | Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades em partição | Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho máximo de uma única mesa | 500 TiB |
| Tamanho máximo de uma única entidade, incluindo todos os valores patrimoniais | 1 MiB |
| Número máximo de imóveis numa entidade de mesa | 255 (incluindo as três propriedades do sistema, **PartitionKey,** **RowKey**, e **Timestamp)** |
| Tamanho total máximo de uma propriedade individual numa entidade | Varia por tipo de propriedade. Para mais informações, consulte **Os Tipos** de Propriedade na [Compreensão do Modelo](/rest/api/storageservices/understanding-the-table-service-data-model)de Dados do Serviço de Tabela . |
| Tamanho da **Chave da Partição** | Uma corda até 1 KiB em tamanho |
| Tamanho da **Chave Row** | Uma corda até 1 KiB em tamanho |
| Tamanho de uma transação de grupo de entidades | Uma transação pode incluir no máximo 100 entidades e a carga útil deve ter menos de 4 MiB em tamanho. Uma transação de grupo de entidades pode incluir uma atualização para uma entidade apenas uma vez. |
| Número máximo de políticas de acesso armazenadas por tabela | 5 |
| Taxa máxima de pedidos por conta de armazenamento | 20.000 transações por segundo, o que pressupõe uma dimensão de 1-KiB |
| Entrada de destino para uma única partilha de tabela (1 KiB-entidades) | Até 2.000 entidades por segundo |