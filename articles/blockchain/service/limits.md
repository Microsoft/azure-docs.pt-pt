---
title: Limites do Serviço Blockchain Azure
description: Visão geral do serviço e limites funcionais no Serviço Azure Blockchain
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529580"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no Serviço Blockchain Azure

O Serviço Azure Blockchain tem limites de serviço e funcionais, tais como o número de nós que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Escalão de preço

Os limites máximos para transações e nódeos validadores dependem se você fornecer o Serviço Azure Blockchain em níveis de preços básicos ou standard.

| Escalão de preço | Nósodetransações max | Nódoreos validadores max |
|:---|:---:|:---:|
| Básico | 10 | 1 |
| Standard | 10 | 2 |

A sua rede de consórcios deverá ter pelo menos dois nódos os nível padrão do Azure Blockchain Service. Os nódosos de nível padrão incluem dois nódeos validadores. São necessários quatro nódosos validadores para se encontrarem com o consenso de [Tolerância à Falta bizantina](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)de Istambul.

A utilização do nível básico é para desenvolvimento, teste e prova de conceitos. Utilize o nível padrão para as implementações de nível de produção. Também deve utilizar o nível *Standard* se estiver a utilizar o Gestor de Dados blockchain ou a enviar um elevado volume de transações privadas.

A alteração do nível de preços entre a criação de base e a norma após a criação dos membros não é suportada.

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

    Pelo contrário, devem ser reconvidados a juntarem-se ao consórcio e a criarem um novo membro. Os recursos dos membros existentes não são suprimidos para preservar transações históricas.

* **Todos os membros de um consórcio devem estar usando a mesma versão de livro-razão**

    Para obter mais informações sobre as versões de patching, atualizações e livros disponíveis no Serviço Blockchain Azure, consulte [Patching, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as políticas relativas a patching e upgrades de sistemas - [Patching, atualizações e versões](ledger-versions.md).
