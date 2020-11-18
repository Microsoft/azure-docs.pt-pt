---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680857"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites adicionais de nível de ações de ficheiros premium

|Área  |Destino  |
|---------|---------|
|Aumento/diminuição do tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB, até 100.000|
|IOPS rebentando    |3x IOPS por GiB, até 100.000|
|Taxa de Egress         |60 MiB/s + 0,06 * GiB a provisionado        |
|Taxa de entrada| 40 MiB/s + 0,04 * GiB a provisionado |

#### <a name="file-level-limits"></a>Limites de nível de ficheiro

|Área  |Arquivo padrão  |Arquivo premium  |
|---------|---------|---------|
|Tamanho     |1 TiB         |4 TiB         |
|Max IOPS por ficheiro      |1,000         |Até 8.000*         |
|Pegas simultâneas     |2.000         |2.000         |
|Saída     |Ver valores de produção de ficheiros padrão         |300 MiB/seg (Até 1 GiB/s com pré-visualização SMB Multicanal)**         |
|Entrada     |Ver valores de produção de ficheiros padrão         |200 MiB/seg (Até 1 GiB/s com pré-visualização SMB Multicanal)**        |
|Débito     |Até 60 MiB/seg         |Ver valores de entrada/saída de ficheiros premium         |

\*<sup>Aplica-se à leitura e à escrita de IOs (tamanhos de IO tipicamente menores <=64K). As operações de metadados, para além de leituras e escritos, podem ser mais baixas. </sup>

\*\*<sup>Sujeito a limites de rede de máquinas, largura de banda disponível, tamanhos de IO, profundidade de fila e outros fatores. Para mais detalhes consulte o [desempenho do SMB Multicannel](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
