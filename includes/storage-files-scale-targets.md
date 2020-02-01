---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901403"
---
| Recurso | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de uma parte de arquivo | Sem mínimos; pagar à medida que vai | 100 GiB; provisionado |
| Tamanho máximo de uma parte de arquivo | 100 Tib*, 5 Tib | 100 Tib |
| Tamanho máximo de um ficheiro numa partilha de ficheiros | 1 TiB | 1 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| IOPS máximopor ação | 10.000 IOPS*, 1.000 IOPS | 100.000 IOPS |
| Número máximo de políticas de acesso armazenadas por partilha de ficheiros | 5 | 5 |
| Entrada de destino para uma única parte de ficheiro | até 300 MiB/sec*, até 60 MiB/seg,  | Ver valores de entrada de partilha de ficheiros premium e valores de egress|
| A máxima saída para uma única parte de ficheiro | Ver a entrada padrão do alvo de partilha de ficheiros | Até 6.204 MiB/s |
| Ingresso máximo para uma única parte de ficheiro | Ver a entrada padrão do alvo de partilha de ficheiros | Até 4.136 MiB/s |
| Cabos abertos máximos por ficheiro | 2\.000 pegas abertas | 2\.000 pegas abertas |
| Número máximo de instantâneos de ações | 200 fotos de partilha | 200 fotos de partilha |
| Comprimento máximo do objeto (diretórios e ficheiros) | 2\.048 caracteres | 2\.048 caracteres |
| Componente máximo do nome do caminho (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |

\* Disponível na maioria das regiões, consulte disponibilidade [regional](../articles/storage/files/storage-files-planning.md#regional-availability) para obter informações sobre as regiões disponíveis.
