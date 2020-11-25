---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: e7b7fae094ad15bc1732778b6a4a3259fb4dd3b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027057"
---
| Recurso | Ações de ficheiros padrão\* | Ações de ficheiros premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de uma partilha de ficheiros | Sem mínimo; pagar à medida que vai | 100 GiB; a provisionado |
| Tamanho máximo de uma partilha de ficheiros | 100 TiB \* \* , 5 TiB | 100 TiB |
| Tamanho máximo de um ficheiro numa partilha de ficheiros | 1 TiB | 4 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| IOPS máximo por ação | 10.000 IOPS, \* \* 1.000 IOPS ou 100 pedidos em 100ms | 100.000 OPS |
| Número máximo de políticas de acesso armazenadas por partilha de ficheiros | 5 | 5 |
| Produção de destino para uma única partilha de ficheiros | até 300 MiB/seg, \* \* até 60 MiB/seg,  | Ver valores de entradas e saídas de ficheiros premium|
| Saída máxima para uma única partilha de ficheiros | Ver produção-alvo de partilha de ficheiros padrão | Até 6.204 MiB/s |
| Entrada máxima para uma única partilha de ficheiros | Ver produção-alvo de partilha de ficheiros padrão | Até 4.136 MiB/s |
| Pegas abertas máximas por ficheiro ou diretório | 2.000 pegas abertas | 2.000 pegas abertas |
| Número máximo de instantâneos de partilha | 200 fotos partilhadas | 200 fotos partilhadas |
| Comprimento máximo do nome do objeto (diretórios e ficheiros) | 2.048 caracteres | 2.048 caracteres |
| Componente de nome de pathname máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |
| Limite de ligação rígido (apenas NFS) | N/D | 178 |
| Número máximo de canais multicanais SMB | N/D | 4 |

\* Os limites das ações de ficheiros padrão aplicam-se aos três níveis disponíveis para as ações de ficheiros padrão: transação otimizada, quente e fria.

\*\* O predefinimento das ações de ficheiros padrão é 5 TiB, ver [Enable e criar grandes ações de ficheiros](../articles/storage/files/storage-files-how-to-create-large-file-share.md) para os detalhes sobre como aumentar a escala de ações de ficheiros padrão até 100 TiB.
