---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 91f91b1260cc445f90c2608fc5259ad61acd37ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90533202"
---
Aqui está uma lista das contas de armazenamento suportadas e dos tipos de armazenamento para o dispositivo Data Box. Para obter uma lista completa de todos os tipos de contas de armazenamento e todas as suas capacidades, consulte [tipos de contas de armazenamento.](/azure/storage/common/storage-account-overview#types-of-storage-accounts)

Para as encomendas de importação, a tabela seguinte mostra as contas de armazenamento suportadas.

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Bolha de página*** |**Ficheiros Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | S | S | S |
| Padrão v1 para fins gerais  | S | S | S | Tanto quentes como frescos são suportados.|
| Finalidade geral v1 Premium  |  | S| | |
| Padrão v2 para fins gerais  | S | S | S | Tanto quentes como frescos são suportados.|
| V2 Premium para fins gerais  |  |S | | |
| Padrão de armazenamento de bolhas |S | | |Tanto quentes como frescos são suportados. |

\**- Os dados enviados para as bolhas de página devem estar alinhados com 512 bytes, tais como VHDs.*

Para as encomendas de exportação, a tabela seguinte mostra as contas de armazenamento suportadas.

| **Conta de armazenamento / Tipos de armazenamento suportados** | **Blob de blocos** |**Bolha de página*** |**Ficheiros Azure** |**Níveis de acesso suportados**|
| --- | --- | -- | -- | -- |
| Padrão Clássico | S | S | S | |
| Padrão v1 para fins gerais  | S | S | S | Quente, Fresco|
| Finalidade geral v1 Premium  |  | S| | |
| Padrão v2 para fins gerais  | S | S | S | Quente, Fresco|
| V2 Premium para fins gerais  |  |S | | |
| Padrão de armazenamento de bolhas |S | | |Quente, Fresco |
| Bloco Blob armazenamento Premium |S | | |Quente, Fresco |
| Armazenamento blob de página Premium | |S | | |

> [!IMPORTANT]
> - Para contas para fins gerais, a Caixa de Dados não suporta tipos de armazenamento de fila, tabela e disco para encomendas de importação. Para encomendas de exportação, a Data Box não suporta tipos de armazenamento de fila, tabela, disco e Azure Data Lake Gen 2 para contas para fins gerais.
> - A Data Box não suporta blobs de apêndice para contas blob de armazenamento de blob e blocos de armazenamento.
> - A Caixa de Dados não suporta contas de Armazenamento de Ficheiros Premium.
> - Os dados enviados para as bolhas de página devem estar alinhados com 512 bytes, tais como VHDs.
> - Um máximo de 80 TB pode ser exportado.
> - O histórico de ficheiros e as fotos blob não são exportadas.


