---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665624"
---
| Recurso | Destino | Alvo (Pré-visualização) |
|-|-|-|
| Tamanho máximo de recipiente de bolha única | O mesmo que a capacidade máxima da conta de armazenamento |  |
| Número máximo de blocos numa bolha de bloco ou de apêndice | 50.000 blocos |  |
| Tamanho máximo de um bloco em uma bolha de bloco | 100 MiB | 4000 MiB (pré-visualização) |
| Tamanho máximo de uma bolha de bloco | 50.000 X 100 MiB (aproximadamente 4,75 TiB) | 50.000 X 4000 MiB (aproximadamente 190.7 TiB) (pré-visualização) |
| Tamanho máximo de um bloco em uma bolha de apêndice | 4 MiB |  |
| Tamanho máximo de uma bolha de apêndice | 50.000 x 4 MiB (aproximadamente 195 GiB) |  |
| Tamanho máximo de uma bolha de página | 8 TiB<sup>2</sup> |  |
| Número máximo de políticas de acesso armazenadas por recipiente blob | 5 |  |
| Taxa de pedido de destino para uma única bolha | Até 500 pedidos por segundo |  |
| Produção do alvo para uma bolha de uma página | Até 60 MiB por segundo<sup>2</sup> |  |
| Produção de destino para uma bolha de bloco único | Até à conta de armazenamento limites<sup>1</sup> |  |

<sup>1</sup> A produção de uma única bolha depende de vários fatores, incluindo, mas não se limitando a: concurrency, tamanho de pedido, nível de desempenho, velocidade de origem para uploads e destino para downloads. Para tirar partido das melhorias de desempenho das [bolhas de blocos de alta potência,](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)carreie bolhas ou blocos maiores. Especificamente, ligue para a operação [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) com uma bolha ou tamanho de bloco superior a 4 MiB para contas de armazenamento padrão. Para blob de bloco premium ou para contas de armazenamento de Data Lake Gen2, use um tamanho de bloco ou bolha superior a 256 KiB.

As bolhas de <sup>2</sup> páginas ainda não são suportadas em contas que têm a definição **de espaço hierárquico** sobre eles.

A tabela seguinte descreve os tamanhos máximos de bloco e bolha permitidos pela versão de serviço.

| Versão de serviço | Tamanho máximo do bloco (via Bloco de Put) | Tamanho máximo do blob (via Put Block List) | Tamanho máximo do blob através de uma única operação de escrita (via Put Blob) |
|-|-|-|-|
| Versão 2019-12-12 e mais tarde | 4000 MiB (pré-visualização) | Aproximadamente 190.7 TiB (4000 MiB X 50.000 blocos) (pré-visualização) | 5000 MiB (pré-visualização) |
| Versão 2016-05-31 através da versão 2019-07-07 | 100 MiB | Aproximadamente 4.75 TiB (100 MiB X 50.000 blocos) | 256 MiB |
| Versões anteriores a 2016-05-31 | 4 MiB | Aproximadamente 195 GiB (4 Blocos MiB X 50.000) | 64 MiB |
