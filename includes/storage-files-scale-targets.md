---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: ec75cf35bb503e49885664a6682e3be319cc45f6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554066"
---
| Recurso | Partilhas de ficheiros padrão | Partilhas de ficheiros de Premium (pré-visualização) |
|----------|---------------|------------------------------------------|
| Tamanho mínimo de uma partilha de ficheiros | Nenhum valor mínimo; opção pay as you go | 100 GiB |
| Tamanho máximo de uma partilha de ficheiros | 5 TiB | 5 TiB |
| Tamanho máximo de um ficheiro numa partilha de ficheiros | 1 TiB | 1 TiB |
| Número máximo de ficheiros numa partilha de ficheiros | Sem limite | Sem limite |
| IOPS máximo por partilha | 1000 IOPS | Linha de base IOPS 5,120<br />15.360 IOPS com rajada |
| Número máximo de políticas de acesso armazenadas por ficheiro partilhar | 5 | 5 |
| Taxa de transferência de destino para uma partilha de ficheiro único | Até 60 MiB/seg | Até 612 MiB/seg (aprovisionado) |
| Máximos identificadores abertos por ficheiro | 2.000 identificadores abertos | 2.000 identificadores abertos |
| Número máximo de instantâneos de partilha | instantâneos de partilha de 200 | instantâneos de partilha de 200 |
| Comprimento do nome do objeto máximo (diretórios e arquivos) | 2048 carateres | 2048 carateres |
| Componente de nome do caminho máximo (no caminho \A\B\C\D, cada letra é um componente) | 255 carateres | 255 carateres |