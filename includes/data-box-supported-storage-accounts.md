---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563102"
---
Aqui está uma lista das contas de armazenamento suportadas e dos tipos de armazenamento para o dispositivo Data Box. Para obter uma lista completa de todos os tipos de contas de armazenamento e todas as suas capacidades, consulte [tipos de contas de armazenamento.](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)

Para as encomendas de importação, a tabela seguinte mostra as contas de armazenamento suportadas.

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Bolha de página** _ |_ *Ficheiros Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | Y | Y | Y |
| Padrão v1 para fins gerais  | Y | Y | Y | Tanto quentes como frescos são suportados.|
| Finalidade geral v1 Premium  |  | Y| | |
| Padrão v2 para fins gerais  | Y | Y | Y | Tanto quentes como frescos são suportados.|
| V2 Premium para fins gerais  |  |Y | | |
| Padrão de armazenamento de bolhas |Y | | |Tanto quentes como frescos são suportados. |

\**- Os dados enviados para as bolhas de página devem estar alinhados com 512 bytes, tais como VHDs.*

Para as encomendas de exportação, a tabela seguinte mostra as contas de armazenamento suportadas.

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Bolha de página** _ |_ *Ficheiros Azure** |**Níveis de acesso suportados**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | Y | Y | Y | |
| Padrão v1 para fins gerais  | Y | Y | Y | Quente, Fresco|
| Finalidade geral v1 Premium  |  | Y| | |
| Padrão v2 para fins gerais  | Y | Y | Y | Quente, Fresco|
| V2 Premium para fins gerais  |  |Y | | |
| Padrão de armazenamento de bolhas |Y | | |Quente, Fresco |
| Bloco Blob armazenamento Premium |Y | | |Quente, Fresco |
| Armazenamento blob de página Premium | |Y | | |

> [!IMPORTANT]
> - Para contas para fins gerais, a Caixa de Dados não suporta tipos de armazenamento de fila, tabela e disco para encomendas de importação. Para encomendas de exportação, a Data Box não suporta tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2 para contas para fins gerais.
> - A Data Box não suporta blobs de apêndice para contas blob de armazenamento de blob e blocos de armazenamento.
> - A Caixa de Dados não suporta contas de Armazenamento de Ficheiros Premium.
> - Os dados enviados para as bolhas de página devem estar alinhados com 512 bytes, tais como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de ficheiros e as fotos blob não são exportadas.