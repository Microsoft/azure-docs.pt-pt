---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392336"
---
| Recurso | Destino        |
|----------|---------------|
| Tamanho máximo do recipiente de bolha única | O mesmo que a capacidade máxima da conta de armazenamento |
| Número máximo de blocos em uma bolha de bloco ou bloco de apêndice | 50.000 blocos |
| Tamanho máximo de um bloco em uma bolha de bloco | 100 MiB |
| Tamanho máximo de uma bolha de bloco | 50.000 X 100 MiB (aproximadamente 4,75 TiB) |
| Tamanho máximo de um bloco em uma bolha de apêndice | 4 MiB |
| Tamanho máximo de uma bolha de apêndice | 50.000 x 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de uma bolha de página | 8 TiB |
| Número máximo de políticas de acesso armazenadas por contentor blob | 5 |
|Taxa de pedido de destino para uma única bolha | Até 500 pedidos por segundo |
|Entrada de destino para uma única página blob | Até 60 MiB por segundo |
|Entrada de alvo para uma única bolha de bloco |Até limites de entrada/saída da conta de armazenamento<sup>1</sup> |

<sup>1</sup> A entrada para uma única bolha depende de vários fatores, incluindo, mas não se limitando a: concurrency, tamanho de pedido, nível de desempenho, velocidade de fonte para uploads e destino para downloads. Para tirar partido das melhorias de desempenho das [bolhas de bloco de alta perspância,](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)carregue bolhas ou blocos maiores. Especificamente, ligue para a operação [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) com uma bolha ou tamanho de bloco superior a 4 MiB para contas de armazenamento padrão. Para uma bolha de bloco premium ou para contas de armazenamento de Data Lake Gen2, utilize um tamanho de bloco ou bolha superior a 256 KiB.
