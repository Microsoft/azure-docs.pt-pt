---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737236"
---
| Recurso | Destino        |
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

<sup>1</sup> débito único objeto depende de diversos fatores, incluindo, mas não limitado a: simultaneidade, tamanho da operação, o escalão de desempenho, velocidade da origem para carregamentos e de destino para as transferências.