---
title: Limites de serviço Azure Blockchain
description: Visão geral do serviço e limites funcionais no Serviço Azure Blockchain
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80676522"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no Serviço Azure Blockchain

O Azure Blockchain Service tem limites de serviço e funcionais, tais como o número de nós que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Escalão de preço

Os limites máximos nas transações e nos nós validadores dependem de se fornece o Serviço Azure Blockchain nos níveis básicos ou padrão de preços.

| Escalão de preço | Nó de transações máximas | Nódoa máximo validador |
|:---|:---:|:---:|
| Básico | 10 | 1 |
| Standard | 10 | 2 |

A sua rede de consórcios deve ter pelo menos dois nós de nível padrão do Serviço Azure Blockchain. Os nós de nível padrão incluem dois nós validadores. Quatro nós validadores são necessários para cumprir o consenso de Tolerância à [Falha Bizantina de Istambul.](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)

A utilização do nível básico é para desenvolvimento, teste e prova de conceitos. Utilize o nível padrão para implantações de nível de produção. Também deve utilizar o nível *Standard* se estiver a utilizar o Blockchain Data Manager ou a enviar um grande volume de transações privadas.

A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser usada por nó para dados de contabilidade e registos é de 1,8 terabytes.

O tamanho do livro de registos e do armazenamento de registos não é suportado.
## <a name="consortium-limits"></a>Limites do consórcio

* **Os nomes do consórcio e dos membros devem ser exclusivos** de outros nomes de consórcio e membros do Serviço Azure Blockchain.

* **Os nomes dos membros e do consórcio não podem ser alterados**

* **Todos os membros de um consórcio devem estar no mesmo nível de preços**

* **Todos os membros que participam num consórcio devem residir na mesma região**

    O primeiro membro criado num consórcio dita a região. Os membros convidados para o consórcio devem residir na mesma região que o primeiro membro. Limitar todos os membros à mesma região ajuda a garantir que o consenso da rede não seja impactado negativamente.

* **Um consórcio deve ter pelo menos um administrador**

    Se houver apenas um administrador num consórcio, não podem retirar-se do consórcio ou eliminar o seu membro até que outro administrador seja adicionado ou promovido no consórcio.

* **Membros removidos do consórcio não podem ser adicionados novamente**

    Pelo contrário, devem ser novamente convidados a aderir ao consórcio e criar um novo membro. Os seus recursos membros existentes não são suprimidos para preservar transações históricas.

* **Todos os membros de um consórcio devem usar a mesma versão de livro**

    Para obter mais informações sobre as versões de patching, atualizações e livros disponíveis no Azure Blockchain Service, consulte [Patching, atualizações e versões](ledger-versions.md).

## <a name="performance"></a>Desempenho

Não utilize a função de gás *eth.estima* para cada submissão de transação. A *função eth.estima* é intensiva em memória. Chamar a função várias vezes reduz drasticamente as transações por segundo.

Se possível, utilize um valor de gás conservador para submeter transações e minimize a utilização da estimativa de *eth.estima*.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre políticas de patching e upgrades de sistemas - [Patching, atualizações e versões](ledger-versions.md).
