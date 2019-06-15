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
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133172"
---
Armazenamento com redundância de zona (ZRS) replica os dados de forma síncrona em três clusters de armazenamento numa única região. Cada cluster de armazenamento é fisicamente separado dos outros e está localizado na sua própria zona de disponibilidade (AZ). Cada zona de disponibilidade&mdash;e o cluster ZRS dentro da mesma&mdash;é autónomo e inclui utilitários separados e funcionalidades de rede. Um pedido de escrita para uma conta de armazenamento ZRS com êxito devolve apenas depois dos dados são gravados para todas as réplicas entre os três clusters.

Quando armazena os dados numa conta de armazenamento com replicação ZRS, pode continuar a aceder e gerir os seus dados se não estiver disponível uma zona de disponibilidade. O ZRS fornece excelente desempenho e baixa latência. O ZRS oferece o mesmo [destinos de escalabilidade](../articles/storage/common/storage-scalability-targets.md) como [armazenamento localmente redundante (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Considere o ZRS para cenários que exigem a consistência, durabilidade e elevada disponibilidade. Mesmo que uma indisponibilidade ou desastre natural renderiza uma zona de disponibilidade indisponível, o ZRS oferece durabilidade para objetos de armazenamento de, pelo menos, 99.9999999999% (12 9 s) ao longo de um ano.

Para obter mais informações sobre as zonas de disponibilidade, consulte [descrição geral das zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview).