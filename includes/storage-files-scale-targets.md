---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 99314538f90404d7c2b72da0dd2da2d8ac60a08a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291714"
---
| Recurso | Partilhas de ficheiros padrão | Partilhas de ficheiros de Premium (pré-visualização) |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de uma partilha de ficheiros | Nenhum valor mínimo; opção pay as you go | 100 giB; aprovisionado |
| Tamanho máximo de uma partilha de ficheiros | 5 TiB | 5 TiB (pré-visualização pública), 100 TiB (pré-visualização pública limitada) |
| Tamanho máximo de um ficheiro numa partilha de ficheiros | 1 TiB | 1 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| IOPS máximo por partilha | 1000 IOPS | IOPS bases 5,120 com limite de rajada 15.360 (pré-visualização pública), 100 000 IOPS (pré-visualização pública limitada)|
| Número máximo de políticas de acesso armazenadas por ficheiro partilhar | 5 | 5 |
| Taxa de transferência de destino para uma partilha de ficheiro único | Até 60 MiB/seg | Consulte premium partilha de entrada e saída valores de ficheiro|
| Saída máxima para uma partilha de ficheiro único | Ver o débito de destino de compartilhamento de arquivo padrão | Até 368 MiB/s (pré-visualização pública), até 6,204 MiB/s (pré-visualização pública limitada) |
| Máxima de entrada para uma partilha de ficheiro único | Ver o débito de destino de compartilhamento de arquivo padrão | Até 245 MiB/s (pré-visualização pública), até 4.136 MiB/s (pré-visualização pública limitada) |
| Máximos identificadores abertos por ficheiro | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de partilha | instantâneos de partilha de 200 | instantâneos de partilha de 200 |
| Comprimento do nome do objeto máximo (diretórios e arquivos) | 2048 carateres | 2048 carateres |
| Componente de nome do caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 carateres | 255 carateres |