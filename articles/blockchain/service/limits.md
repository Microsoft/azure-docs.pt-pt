---
title: Limites do serviço Blockchain do Azure
description: Visão geral dos limites funcionais e de serviço no serviço Blockchain do Azure
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455655"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no serviço Blockchain do Azure

O serviço Blockchain do Azure tem limites funcionais e de serviço, como o número de nós que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Escalão de preço

Os limites máximos em transações e nós de validador dependem se você provisiona o serviço Blockchain do Azure em tipos de preço básico ou padrão.

| Escalão de preço | Máximo de nós de transação | Máximo de nós de validador |
|:---|:---:|:---:|
| Básica | 10 | 1 |
| Standard | 10 | 2 |

A alteração do tipo de preço entre básico e Standard após a criação do membro não é suportada.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser usada por nó para os dados e logs do razão é de 1,8 terabytes.

Não há suporte para a diminuição do tamanho do armazenamento do razão e do log.

## <a name="consortium-limits"></a>Limites do consórcio

* O **consórcio e os nomes de membros devem ser exclusivos** de outros nomes de consórcio e membro no serviço Blockchain do Azure.

* **Os nomes member e Consortium não podem ser alterados**

* **Todos os membros em um consórcio devem estar no mesmo tipo de preço**

* **Todos os membros que participam de um consórcio devem residir na mesma região**

    O primeiro membro criado em um consórcio determina a região. Membros convidados para o consórcio devem residir na mesma região que o primeiro membro. Limitar todos os membros à mesma região ajuda a garantir que o consenso da rede não seja afetado negativamente.

* **Um consórcio deve ter pelo menos um administrador**

    Se houver apenas um administrador em um consórcio, eles não poderão se remover do consórcio nem excluir seus membros até que outro administrador seja adicionado ou promovido no consórcio.

* **Os membros removidos do consórcio não podem ser adicionados novamente**

    Em vez disso, eles devem ser reconvidados para ingressar no consórcio e criar um novo membro. O recurso de membro existente não é excluído para preservar as transações históricas.

* **Todos os membros em um consórcio devem usar a mesma versão do razão**

    Para obter mais informações sobre patches, atualizações e versões do razão disponíveis no serviço Blockchain do Azure, consulte [patches, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as políticas relacionadas a patches e atualizações de sistemas – [patches, atualizações e versões](ledger-versions.md).
