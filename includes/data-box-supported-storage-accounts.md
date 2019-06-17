---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242161"
---
Aqui está uma lista de contas de armazenamento suportadas e os tipos de armazenamento para o dispositivo do Data Box. Para obter uma lista completa de todos os diferentes tipos de contas de armazenamento e as respetivas funcionalidades completas, consulte [tipos de contas de armazenamento](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Conta de armazenamento / suportado tipos de armazenamento** | **Blob de blocos** |**BLOBs de páginas*** |**Ficheiros do Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Standard clássico | S | S | S |
| Standard para fins gerais v1  | S | S | S | São suportadas frequente e esporádico.|
| Premium para fins gerais v1  |  | S| | |
| Para fins gerais v2 Standard  | S | S | S | São suportadas frequente e esporádico.|
| Premium para fins gerais v2  |  |S | | |
| Armazenamento de BLOBs Standard |S | | |São suportadas frequente e esporádico. |

\* *-Dados carregados para blobs de páginas tem de ser alinhados, como VHDs de 512 bytes.*

>[!NOTE]
> Contas de armazenamento Gen 2 do Azure Data Lake não são suportadas.
