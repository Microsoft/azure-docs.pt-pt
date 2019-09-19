---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67449947"
---
| Resource | Compartilhamentos de arquivos padrão | Compartilhamentos de arquivos Premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de um compartilhamento de arquivos | Sem mínimo; pré-pago | 100 GiB; provisionado |
| Tamanho máximo de um compartilhamento de arquivos | 5 TiB (GA), 100 TiB (versão prévia) | 100 TiB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos | 1 TiB | 1 TiB |
| Número máximo de arquivos em um compartilhamento de arquivos | Sem limite | Sem limite |
| IOPS máximo por compartilhamento | 1\.000 IOPS (GA), 10.000 IOPS (versão prévia) | IOPS DE 100.000 |
| Número máximo de políticas de acesso armazenadas por compartilhamento de arquivos | 5 | 5 |
| Taxa de transferência de destino para um único compartilhamento de arquivos | Até 60 MiB/s (GA), até 300 MiB/s (visualização) | Consulte valores de entrada e saída do compartilhamento de arquivos Premium|
| Egresso máxima de um único compartilhamento de arquivo | Consulte taxa de transferência de destino de compartilhamento de arquivos padrão | Até 6.204 MiB/s |
| Máximo de entrada para um único compartilhamento de arquivos | Consulte taxa de transferência de destino de compartilhamento de arquivos padrão | Até 4.136 MiB/s |
| Máximo de identificadores abertos por arquivo | 2\.000 identificadores abertos | 2\.000 identificadores abertos |
| Número máximo de instantâneos de compartilhamento | 200 instantâneos de compartilhamento | 200 instantâneos de compartilhamento |
| Comprimento máximo do nome do objeto (diretórios e arquivos) | 2\.048 caracteres | 2\.048 caracteres |
| Componente de nome de caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 caracteres | 255 caracteres |