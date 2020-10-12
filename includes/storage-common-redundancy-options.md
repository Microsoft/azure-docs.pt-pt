---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157228"
---
As opções de despedimento de uma conta de armazenamento incluem:

* Armazenamento localmente redundante (LRS): Uma estratégia simples e de redundância de baixo custo. Os dados são copiados sincronizadamente três vezes na região primária.
* Armazenamento redundante de zona (ZRS): Redundância para cenários que exijam elevada disponibilidade. Os dados são copiados sincronizadamente em três zonas de disponibilidade de Azure na região primária.
* Armazenamento geo-redundante (GRS): Redundância regional para proteger contra interrupções regionais. Os dados são copiados sincronizadamente três vezes na região primária, depois copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geo-redundante de acesso à leitura (RA-GRS).
* Armazenamento de zonas-redundantes (GZRS) (pré-visualização): Redundância para cenários que exijam elevada disponibilidade e durabilidade máxima. Os dados são copiados sincronizadamente em três zonas de disponibilidade de Azure na região primária, depois copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento de zonas de acesso à leitura redundantes (RA-GZRS).

Para obter mais informações sobre as opções de despedimento no Azure Storage, consulte [a redundância do Azure Storage](../articles/storage/common/storage-redundancy.md).
