---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/01/2021
ms.author: tamram
ms.openlocfilehash: 16da73fe453760e2dc84e7d683c3a16c12b8a06f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218617"
---
| Recurso | Destino |
|-|-|
| Tamanho máximo de recipiente de bolha única | O mesmo que a capacidade máxima da conta de armazenamento |
| Número máximo de blocos numa bolha de bloco ou de apêndice | 50.000 blocos |
| Tamanho máximo de um bloco em uma bolha de bloco | 4000 MiB |
| Tamanho máximo de uma bolha de bloco | 50.000 X 4000 MiB (aproximadamente 190,7 TiB) |
| Tamanho máximo de um bloco em uma bolha de apêndice | 4 MiB |
| Tamanho máximo de uma bolha de apêndice | 50.000 x 4 MiB (aproximadamente 195 GiB) |
| Tamanho máximo de uma bolha de página | 8 TiB<sup>2</sup> |
| Número máximo de políticas de acesso armazenadas por recipiente blob | 5 |
| Taxa de pedido de destino para uma única bolha | Até 500 pedidos por segundo |
| Produção do alvo para uma bolha de uma página | Até 60 MiB por segundo<sup>2</sup> |
| Produção de destino para uma bolha de bloco único | Até à conta de armazenamento limites<sup>1</sup> |

<sup>1</sup> A produção de uma única bolha depende de vários fatores, incluindo, mas não se limitando a: concurrency, tamanho de pedido, nível de desempenho, velocidade de origem para uploads e destino para downloads. Para tirar partido das melhorias de desempenho das [bolhas de blocos de alta potência,](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)carreie bolhas ou blocos maiores. Especificamente, ligue para a operação [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) com uma bolha ou tamanho de bloco superior a 4 MiB para contas de armazenamento padrão. Para blob de bloco premium ou para contas de armazenamento de Data Lake Gen2, use um tamanho de bloco ou bolha superior a 256 KiB.

As bolhas de <sup>2</sup> páginas ainda não são suportadas em contas que têm a definição **de espaço hierárquico** sobre eles.

A tabela seguinte descreve os tamanhos máximos de bloco e bolha permitidos pela versão de serviço.

| Versão de serviço | Tamanho máximo do bloco (via Bloco de Put) | Tamanho máximo do blob (via Put Block List) | Tamanho máximo do blob através de uma única operação de escrita (via Put Blob) |
|-|-|-|-|
| Versão 2019-12-12 e mais tarde | 4000 MiB | Aproximadamente 190.7 TiB (4000 MiB X 50.000 blocos) | 5000 MiB (pré-visualização) |
| Versão 2016-05-31 através da versão 2019-07-07 | 100 MiB | Aproximadamente 4.75 TiB (100 MiB X 50.000 blocos) | 256 MiB |
| Versões anteriores a 2016-05-31 | 4 MiB | Aproximadamente 195 GiB (4 Blocos MiB X 50.000) | 64 MiB |
