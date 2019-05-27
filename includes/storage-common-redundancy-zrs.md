---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113790"
---
Armazenamento com redundância de zona (ZRS) replica os dados de forma síncrona em três clusters de armazenamento numa única região. Cada cluster de armazenamento é fisicamente separado dos outros e está localizado na sua própria zona de disponibilidade (AZ). Cada zona de disponibilidade&mdash;e o cluster ZRS dentro da mesma&mdash;é autónomo e inclui utilitários separados e funcionalidades de rede.

Quando armazena os dados numa conta de armazenamento com replicação ZRS, pode continuar a aceder e gerir os seus dados se não estiver disponível uma zona de disponibilidade. O ZRS fornece excelente desempenho e baixa latência. O ZRS oferece o mesmo [destinos de escalabilidade](../articles/storage/common/storage-scalability-targets.md) como [armazenamento localmente redundante (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Considere o ZRS para cenários que exigem a consistência, durabilidade e elevada disponibilidade. Mesmo que uma indisponibilidade ou desastre natural renderiza uma zona de disponibilidade indisponível, o ZRS oferece durabilidade para objetos de armazenamento de, pelo menos, 99.9999999999% (12 9 s) ao longo de um ano.

Para obter mais informações sobre as zonas de disponibilidade, consulte [descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).