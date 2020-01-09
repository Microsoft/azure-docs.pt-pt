---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392336"
---
| Recurso | Visar        |
|----------|---------------|
| Tamanho máximo do contêiner de blob único | Igual à capacidade máxima da conta de armazenamento |
| Número máximo de blocos em um blob de blocos ou BLOB de acréscimo | 50.000 blocos |
| Tamanho máximo de um bloco em um blob de blocos | 100 MiB |
| Tamanho máximo de um blob de blocos | 50.000 X 100 MiB (aproximadamente 4,75 TiB) |
| Tamanho máximo de um bloco em um blob de acréscimo | 4 MiB |
| Tamanho máximo de um blob de acréscimo | 50.000 x 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de um blob de páginas | 8 TiB |
| Número máximo de políticas de acesso armazenadas por contêiner de BLOB | 5 |
|Taxa de solicitação de destino para um único blob | Até 500 solicitações por segundo |
|Taxa de transferência de destino para um blob de página única | Até 60 MiB por segundo |
|Taxa de transferência de destino para um único blob de blocos |Até os limites de entrada/saída da conta de armazenamento<sup>1</sup> |

<sup>1</sup> a taxa de transferência para um único blob depende de vários fatores, incluindo, mas não se limitando a: simultaneidade, tamanho da solicitação, nível de desempenho, velocidade da origem para carregamentos e destino para downloads. Para aproveitar os aprimoramentos de desempenho de [blobs de blocos de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), carregue BLOBs ou blocos maiores. Especificamente, chame a operação [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) com um tamanho de bloco ou BLOB maior que 4 MIB para contas de armazenamento padrão. Para o blob de blocos Premium ou para contas de armazenamento Data Lake Storage Gen2, use um tamanho de bloco ou BLOB maior que 256 KiB.
