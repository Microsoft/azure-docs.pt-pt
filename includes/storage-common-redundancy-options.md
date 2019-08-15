---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029844"
---
As opções de replicação para uma conta de armazenamento incluem:

* [LRS (armazenamento com redundância local)](../articles/storage/common/storage-redundancy-lrs.md): Uma estratégia de replicação simples e de baixo custo. Os dados são replicados de forma síncrona três vezes dentro da região primária.
* [Armazenamento com redundância de zona (ZRS)](../articles/storage/common/storage-redundancy-zrs.md): Replicação para cenários que exigem alta disponibilidade. Os dados são replicados de forma síncrona em três zonas de disponibilidade do Azure na região primária.
* [Armazenamento com redundância geográfica (GRS)](../articles/storage/common/storage-redundancy-grs.md): Replicação entre regiões para proteger contra interrupções regionais. Os dados são replicados de forma síncrona três vezes na região primária e, em seguida, replicados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS).
* [Armazenamento com redundância de zona geográfica (GZRS) (visualização)](../articles/storage/common/storage-redundancy-gzrs.md): Replicação para cenários que exigem alta disponibilidade e durabilidade máxima. Os dados são replicados de forma síncrona em três zonas de disponibilidade do Azure na região primária e, em seguida, replicados assincronamente para a região secundária. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS).
