---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449947"
---
| Resource | Partilhas de ficheiros padrão | Partilhas de ficheiros de Premium |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de uma partilha de ficheiros | Nenhum valor mínimo; opção pay as you go | 100 giB; aprovisionado |
| Tamanho máximo de uma partilha de ficheiros | 5 TiB (GA), 100 TiB (pré-visualização) | 100 TiB |
| Tamanho máximo de um ficheiro numa partilha de ficheiros | 1 TiB | 1 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| IOPS máximo por partilha | 1000 IOPS (GA), 10 000 IOPS (pré-visualização) | 100 000 IOPS |
| Número máximo de políticas de acesso armazenadas por ficheiro partilhar | 5 | 5 |
| Taxa de transferência de destino para uma partilha de ficheiro único | Até 60 MiB/seg (GA), até 300 MiB/seg (pré-visualização) | Consulte premium partilha de entrada e saída valores de ficheiro|
| Saída máxima para uma partilha de ficheiro único | Ver o débito de destino de compartilhamento de arquivo padrão | Até 6,204 MiB/s |
| Máxima de entrada para uma partilha de ficheiro único | Ver o débito de destino de compartilhamento de arquivo padrão | Até 4.136 MiB/s |
| Máximos identificadores abertos por ficheiro | 2\.000 identificadores abertos | 2\.000 identificadores abertos |
| Número máximo de instantâneos de partilha | instantâneos de partilha de 200 | instantâneos de partilha de 200 |
| Comprimento do nome do objeto máximo (diretórios e arquivos) | 2048 carateres | 2048 carateres |
| Componente de nome do caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 carateres | 255 carateres |