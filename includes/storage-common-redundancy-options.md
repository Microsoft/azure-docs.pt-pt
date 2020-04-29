---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77157228"
---
As opções de despedimento para uma conta de armazenamento incluem:

* Armazenamento localmente redundante (LRS): Uma estratégia simples e de redundância de baixo custo. Os dados são copiados sincronizadamente três vezes dentro da região primária.
* Armazenamento redundante em zona (ZRS): Redundância para cenários que requerem elevada disponibilidade. Os dados são copiados sincronizadamente em três zonas de disponibilidade azure na região primária.
* Armazenamento geo-redundante (GRS): redundância trans-regional para proteger contra interrupções regionais. Os dados são copiados sincronizadamente três vezes na região primária, depois copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geo-redundante de acesso de leitura (RA-GRS).
* Armazenamento geo-zona-redundante (GZRS) (pré-visualização): Redundância para cenários que requerem alta disponibilidade e durabilidade máxima. Os dados são copiados sincronizadamente em três zonas de disponibilidade azure na região primária, e depois copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geozona-redundante de acesso à leitura (RA-GZRS).

Para obter mais informações sobre opções de despedimento no Armazenamento Azure, consulte o [despedimento do Azure Storage.](../articles/storage/common/storage-redundancy.md)
