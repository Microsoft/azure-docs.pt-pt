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
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463936"
---
O ZRS (armazenamento com redundância de zona) Replica seus dados de forma síncrona em três clusters de armazenamento em uma única região. Cada cluster de armazenamento é fisicamente separado dos outros e está localizado em sua própria zona de disponibilidade (AZ). Cada&mdash;de zona de disponibilidade e o cluster ZRS dentro dela&mdash;é autônomo e inclui utilitários separados e recursos de rede. Uma solicitação de gravação para uma conta de armazenamento ZRS retorna com êxito somente depois que os dados são gravados em todas as réplicas entre os três clusters.

Ao armazenar seus dados em uma conta de armazenamento usando a replicação do ZRS, você pode continuar a acessar e gerenciar seus dados se uma zona de disponibilidade ficar indisponível. O ZRS fornece excelente desempenho e baixa latência. O ZRS oferece as mesmas metas de escalabilidade que o [LRS (armazenamento com redundância local)](../articles/storage/common/storage-redundancy-lrs.md). Para obter mais informações sobre metas de escalabilidade para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](../articles/storage/common/scalability-targets-standard-account.md).

Considere o ZRS para cenários que exigem consistência, durabilidade e alta disponibilidade. Mesmo se uma interrupção ou um desastre natural renderizar uma zona de disponibilidade indisponível, o ZRS oferece durabilidade para objetos de armazenamento de pelo menos 99,9999999999% (12 9 ' s) em um determinado ano.

O armazenamento com redundância de zona geográfica (GZRS) (visualização) Replica seus dados de forma síncrona em três zonas de disponibilidade do Azure na região primária e, em seguida, replica os dados de maneira assíncrona para a região secundária. O GZRS fornece alta disponibilidade junto com a durabilidade máxima. O GZRS foi projetado para fornecer pelo menos a durabilidade de objetos de 99.99999999999999% (16 9) em um determinado ano. Para acesso de leitura aos dados na região secundária, habilite o armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS). Para obter mais informações sobre o GZRS, consulte [armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)](../articles/storage/common/storage-redundancy-gzrs.md).

Para obter mais informações sobre zonas de disponibilidade, consulte [zonas de disponibilidade visão geral](https://docs.microsoft.com/azure/availability-zones/az-overview).
