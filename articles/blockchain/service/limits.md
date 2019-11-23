---
title: Azure Blockchain Service limits
description: Overview of the service and functional limits in Azure Blockchain Service
ms.date: 05/02/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 0500d7c5e2d05d185b4d032a33c008726d996df1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326161"
---
# <a name="limits-in-azure-blockchain-service"></a>Limits in Azure Blockchain Service

Azure Blockchain Service has service and functional limits such as the number of nodes a member can have, consortium restrictions, and storage amounts.

## <a name="pricing-tier"></a>Escalão de preço

Maximum limits on transactions and validator nodes depend on whether you provision Azure Blockchain Service at Basic or Standard pricing tiers.

| Escalão de preço | Max transaction nodes | Max validator nodes |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Padrão | 10 | 2 |

Changing the pricing tier between Basic and Standard after member creation is not supported.

## <a name="storage-capacity"></a>Capacidade de armazenamento

The maximum amount of storage that can be used per node for ledger data and logs is 1.8 terabytes.

Decreasing ledger and log storage size is not supported.

## <a name="consortium-limits"></a>Consortium limits

* **Consortium and member names must be unique** from other consortium and member names in the Azure Blockchain Service.

* **Member and consortium names cannot be changed**

* **All members in a consortium must be in the same pricing tier**

* **All members that participate in a consortium must reside in the same region**

    The first member created in a consortium dictates the region. Invited members to the consortium must reside in the same region as the first member. Limiting all members to the same region helps ensure network consensus is not negatively impacted.

* **A consortium must have at least one administrator**

    If there is only one administrator in a consortium, they cannot remove themselves from the consortium or delete their member until another administrator is added or promoted in the consortium.

* **Members removed from the consortium cannot be added again**

    Rather, they must be reinvited to join the consortium and create a new member. Their existing member resource are not deleted to preserve historical transactions.

* **All members in a consortium must be using the same ledger version**

    For more information on the patching, updates, and ledger versions available in Azure Blockchain Service, see [Patching, updates, and versions](ledger-versions.md).

## <a name="next-steps"></a>Passos seguintes

* [Patching, updates, and versions](ledger-versions.md)
