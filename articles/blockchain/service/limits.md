---
title: Limites do Azure Blockchain
description: Descrição geral do serviço e limites funcionais no serviço do Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028174"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites de serviço do Azure Blockchain

O serviço do Azure Blockchain tem o serviço e os limites funcionais como o número de nós que pode ter um membro, restrições de consortium e quantidades de armazenamento.

## <a name="pricing-tier"></a>Escalão de preço

Os limites máximos em transações e nós de validador dependem se aprovisionar o serviço de Azure Blockchain básico ou Standard escalões de preço.

| Escalão de preço | Máxima de nós de transação | Máxima de nós de validador |
|:---|:---:|:---:|
| Básica | 10 | 1 |
| Standard | 10 | 2 |

Alterar o escalão de preços entre básico e Standard após a criação de membro não é suportada.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser utilizado por nó para dados de contabilidade e registos de é de 1 terabyte.

Não é suportada a diminuir o tamanho de armazenamento do razão e registo.

## <a name="consortium-limits"></a>Limites de consórcio

* **Nomes de Consortium e membro tem de ser exclusivos** de outros nomes consortium e membro no serviço do Azure Blockchain.

* **Não não possível alterar os nomes de membro e consortium**

* **Todos os membros de um consórcio tem de estar no mesmo escalão de preços**

* **Todos os membros que participam de um consórcio tem de residir na mesma região**

    O primeiro membro criado num consórcio dita a região. Membros convidados para o consortium têm de residir na mesma região que o primeiro membro. Limitação de todos os membros da mesma região ajuda a garantir a consenso de rede não é afetado negativamente.

* **Um consórcio tem de ter, pelo menos, um administrador**

    Se houver apenas um administrador num consórcio, não é possível remover a próprios o consortium ou eliminar o membro até que outro administrador é adicionado ou promovido no consortium.

* **Membros removidos do consortium não podem ser adicionados novamente**

    Em vez disso, eles devem ser novamente convidados para aderir o consortium e criar um novo membro. O recurso de membro existente não são eliminados para preservar transações históricas.

* **Todos os membros de um consórcio tem de utilizar a mesma versão de contabilidade**

    Para obter mais informações sobre a aplicação de patches, atualizações e versões de contábeis disponíveis no serviço do Azure Blockchain, consulte [a aplicação de patches, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Passos Seguintes

* [Aplicação de patches, atualizações e versões](ledger-versions.md)
