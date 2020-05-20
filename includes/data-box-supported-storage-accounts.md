---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: f230fc247c6ad94bfdfb3cdbc0f897d66313b039
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696479"
---
Aqui está uma lista das contas de armazenamento suportadas e dos tipos de armazenamento para o dispositivo Data Box. Para obter uma lista completa de todos os tipos de contas de armazenamento e as suas capacidades completas, consulte [tipos de contas de armazenamento](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Blob página*** |**Ficheiros Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | S | S | S |
| Norma v1 de uso geral  | S | S | S | Tanto quentes como frescos são suportados.|
| Prémio v1 de propósito geral  |  | S| | |
| Norma v2 de uso geral  | S | S | S | Tanto quentes como frescos são suportados.|
| Prémio v2 de propósito geral  |  |S | | |
| Padrão de armazenamento blob |S | | |Tanto quentes como frescos são suportados. |

\**- Os dados enviados para as bolhas de página devem ser 512 bytes alinhados, tais como VHDs.*
