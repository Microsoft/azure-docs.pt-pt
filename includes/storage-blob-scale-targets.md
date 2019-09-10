---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184471"
---
| Resource | Destino        |
|----------|---------------|
| Tamanho máximo do contêiner de blob único | Igual à capacidade máxima da conta de armazenamento |
| Número máximo de blocos em um blob de blocos ou BLOB de acréscimo | 50.000 blocos |
| Tamanho máximo de um bloco em um blob de blocos | 100 MiB |
| Tamanho máximo de um blob de blocos | 50.000 X 100 MiB (aproximadamente 4,75 TiB) |
| Tamanho máximo de um bloco em um blob de acréscimo | 4 MiB |
| Tamanho máximo de um blob de acréscimo | 50.000 x 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de um blob de páginas | 8 TiB |
| Número máximo de políticas de acesso armazenadas por contêiner de BLOB | 5 |
|Taxa de transferência de destino para um único blob |Até os limites de entrada/saída da conta de armazenamento<sup>1</sup> |

<sup>1</sup> a taxa de transferência de objeto único depende de vários fatores, incluindo, mas não se limitando a: simultaneidade, tamanho da solicitação, nível de desempenho, velocidade da origem para carregamentos e destino para downloads. Para aproveitar os aprimoramentos de desempenho do [blob de blocos de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , use um tamanho de solicitação Put Blob ou Put block de > 4 MiB (> 256 KiB para armazenamento de blobs de blocos de desempenho Premium ou para data Lake Storage Gen2).
