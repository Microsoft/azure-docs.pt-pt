---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 12/23/2020
ms.author: alkohli
ms.openlocfilehash: 11958c54dd1f54e424b71eb00780f5309a1c0bab
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209559"
---
Aqui está uma lista das contas de armazenamento suportadas e tipos de armazenamento para um dispositivo Data Box. Para obter uma lista completa de todas as capacidades para todos os tipos de contas de armazenamento, consulte [tipos de contas de armazenamento](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Para as encomendas de importação, a tabela seguinte mostra as contas de armazenamento suportadas.

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Bolha de página** _ |_ *Ficheiros Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | Y | Y | Y |
| Padrão v1 para fins gerais  | Y | Y | Y | Tanto quentes como frescos são suportados.|
| Finalidade geral v1 Premium  |  | Y| | |
| Padrão v2 para fins gerais  | Y | Y | Y | Tanto quentes como frescos são suportados.|
| V2 Premium para fins gerais  |  |Y | | |
| Arquitorage Azure Premium |  |  | Y |  |  
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
| Arquitorage Azure Premium |  |  | Y |  |
| Padrão de armazenamento de bolhas |Y | | |Quente, Fresco |
| Bloco Blob armazenamento Premium |Y | | |Quente, Fresco |
| Armazenamento blob de página Premium | |Y | | |

> [!IMPORTANT]
> - Para contas para fins gerais, a Caixa de Dados não suporta tipos de armazenamento de fila, tabela e disco para encomendas de importação. Para encomendas de exportação, a Data Box não suporta tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2 para contas para fins gerais.
> - A Data Box não suporta blobs de apêndice para contas blob de armazenamento de blob e blocos de armazenamento.
> - Os dados enviados para as bolhas de página devem estar alinhados com 512 bytes, tais como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de ficheiros e as fotos blob não são exportadas.