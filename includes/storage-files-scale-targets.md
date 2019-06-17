---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: df5d8ff941c742d546208c27c7af4e7bcbeec08a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073768"
---
| Resource | Partilhas de ficheiros padrão | Partilhas de ficheiros de Premium (pré-visualização) |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de uma partilha de ficheiros | Nenhum valor mínimo; opção pay as you go | 100 giB; aprovisionado |
| Tamanho máximo de uma partilha de ficheiros | 5 TiB | 100 TiB |
| Tamanho máximo de um ficheiro numa partilha de ficheiros | 1 TiB | 1 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| IOPS máximo por partilha | 1000 IOPS | 100 000 IOPS |
| Número máximo de políticas de acesso armazenadas por ficheiro partilhar | 5 | 5 |
| Taxa de transferência de destino para uma partilha de ficheiro único | Até 60 MiB/seg | Consulte premium partilha de entrada e saída valores de ficheiro|
| Saída máxima para uma partilha de ficheiro único | Ver o débito de destino de compartilhamento de arquivo padrão | Até 6,204 MiB/s |
| Máxima de entrada para uma partilha de ficheiro único | Ver o débito de destino de compartilhamento de arquivo padrão | Até 4.136 MiB/s |
| Máximos identificadores abertos por ficheiro | 2\.000 identificadores abertos | 2\.000 identificadores abertos |
| Número máximo de instantâneos de partilha | instantâneos de partilha de 200 | instantâneos de partilha de 200 |
| Comprimento do nome do objeto máximo (diretórios e arquivos) | 2048 carateres | 2048 carateres |
| Componente de nome do caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 carateres | 255 carateres |