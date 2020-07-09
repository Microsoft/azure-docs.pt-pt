---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: f230fc247c6ad94bfdfb3cdbc0f897d66313b039
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83696479"
---
Aqui está uma lista das contas de armazenamento suportadas e dos tipos de armazenamento para o dispositivo Data Box. Para obter uma lista completa de todos os tipos de contas de armazenamento e todas as suas capacidades, consulte [tipos de contas de armazenamento.](/azure/storage/common/storage-account-overview#types-of-storage-accounts)

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Bolha de página*** |**Ficheiros Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | S | S | S |
| Padrão v1 para fins gerais  | S | S | S | Tanto quentes como frescos são suportados.|
| Finalidade geral v1 Premium  |  | S| | |
| Padrão v2 para fins gerais  | S | S | S | Tanto quentes como frescos são suportados.|
| V2 Premium para fins gerais  |  |S | | |
| Padrão de armazenamento de bolhas |S | | |Tanto quentes como frescos são suportados. |

\**- Os dados enviados para as bolhas de página devem estar alinhados com 512 bytes, tais como VHDs.*
