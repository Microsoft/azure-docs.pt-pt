---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114826"
---
| Resource | Destino        |
|----------|---------------|
| Tamanho máximo do contentor de BLOBs único | Mesmo que a capacidade das contas de armazenamento máximo |
| Número máximo de blocos num bloco de BLOBs ou blob de acréscimo | 50 000 blocos |
| Tamanho máximo de um bloco de um blob de blocos | 100 MiB |
| Tamanho máximo de um blob de blocos | 50 000 x 100 MiB (aproximadamente de 4.75 TiB) |
| Tamanho máximo de um bloco num blob de acréscimo | 4 MiB |
| Tamanho máximo de um blob de acréscimo | 50 000 x 4 MiB (aproximadamente, 195 GiB) |
| Tamanho máximo de um blob de página | 8 TiB |
| Número máximo de políticas de acesso armazenadas por contentor de BLOBs | 5 |
|Taxa de transferência de destino para o blob único |Até os limites de entrada/saída de conta de armazenamento<sup>1</sup> |

<sup>1</sup> débito único objeto depende de diversos fatores, incluindo, mas não limitado a: simultaneidade, tamanho do pedido, o escalão de desempenho, velocidade da origem para carregamentos e de destino para as transferências. Para tirar partido das [blob de blocos de alto débito](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) aprimoramentos de desempenho, utilize um tamanho de pedido de colocar o Blob ou colocação de blocos de 4 > MiB (> 256 KiB para armazenamento de BLOBs de blocos de desempenho premium ou para a geração 2 de armazenamento do Data Lake).
