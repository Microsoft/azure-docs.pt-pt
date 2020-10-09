---
title: Suporte de adição de VMs Azure a um conjunto de disponibilidade existente / Microsoft Docs
description: Este artigo fornece uma matriz de suporte sobre qual série VM você pode misturar no mesmo conjunto de disponibilidade
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77122925"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Suporte de adição de VMs Azure a um conjunto de disponibilidade existente

Pode ocasionalmente encontrar limitações quando adicionar novas máquinas virtuais (VMs) a um conjunto de disponibilidade existente. Os seguintes detalhes do gráfico que séries VM pode misturar no mesmo conjunto de disponibilidade.

Aqui está a matriz de suporte para misturar diferentes tipos de VMs:

Série & Conjunto de Disponibilidade|Segundo VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeiro VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas as outras séries não podiam estar no mesmo conjunto de disponibilidade porque requerem um hardware específico.

O tamanho A8/A9 VM não pode ser misturado devido à exigência na rede de backend dedicada rdma.
