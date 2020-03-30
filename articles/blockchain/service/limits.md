---
title: Limites do Serviço Blockchain Azure
description: Visão geral do serviço e limites funcionais no Serviço Azure Blockchain
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455655"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no Serviço Blockchain Azure

O Serviço Azure Blockchain tem limites de serviço e funcionais, tais como o número de nós que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Escalão de preço

Os limites máximos para transações e nódeos validadores dependem se você fornecer o Serviço Azure Blockchain em níveis básicos ou standard de preços.

| Escalão de preço | Nósodetransações max | Nódoreos validadores max |
|:---|:---:|:---:|
| Básico | 10 | 1 |
| Standard | 10 | 2 |

A alteração do nível de preços entre o Básico e o Standard após a criação dos membros não é suportada.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser usada por nó para dados e registos de livros é de 1,8 terabytes.

Não é suportado o tamanho do livro de contabilidade e do armazenamento de registos.

## <a name="consortium-limits"></a>Limites do consórcio

* **Os nomes do consórcio e dos membros devem ser exclusivos** de outros nomes de consórcio e membros no Serviço Azure Blockchain.

* **Os nomes dos membros e do consórcio não podem ser alterados**

* **Todos os membros de um consórcio devem estar no mesmo nível de preços**

* **Todos os membros que participam num consórcio devem residir na mesma região**

    O primeiro membro criado num consórcio dita a região. Os membros convidados do consórcio devem residir na mesma região que o primeiro membro. Limitar todos os membros à mesma região ajuda a garantir que o consenso da rede não seja impactado negativamente.

* **Um consórcio deve ter pelo menos um administrador**

    Se houver apenas um administrador num consórcio, não podem retirar-se do consórcio ou eliminar o seu membro até que outro administrador seja adicionado ou promovido no consórcio.

* **Os membros retirados do consórcio não podem ser adicionados novamente**

    Pelo contrário, devem ser reconvidados a juntarem-se ao consórcio e a criarem um novo membro. Os recursos existentes para membros não são eliminados para preservar transações históricas.

* **Todos os membros de um consórcio devem estar usando a mesma versão de livro-razão**

    Para obter mais informações sobre as versões de patching, atualizações e livros disponíveis no Serviço Blockchain Azure, consulte [Patching, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas relativas a patching e upgrades de sistemas - [Patching, atualizações e versões](ledger-versions.md).
