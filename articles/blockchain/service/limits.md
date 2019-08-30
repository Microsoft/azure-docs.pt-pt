---
title: Limites de Blockchain do Azure
description: Visão geral dos limites funcionais e de serviço no serviço Blockchain do Azure
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: aeed84f19da3843d043eafef9d7444661901c53e
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147135"
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

## <a name="next-steps"></a>Passos seguintes

* [Aplicação de patch, atualizações e versões](ledger-versions.md)
