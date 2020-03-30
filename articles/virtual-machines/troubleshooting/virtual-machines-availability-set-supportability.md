---
title: Capacidade de apoio à adição de VMs Azure a um conjunto de disponibilidade existentes Microsoft Docs
description: Este artigo fornece uma matriz de suporte sobre a qual série vM pode misturar no mesmo conjunto de disponibilidade
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122925"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Capacidade de suporte de adicionar VMs Azure a um conjunto de disponibilidade existente

Pode ocasionalmente encontrar limitações quando adiciona novas máquinas virtuais (VMs) a um conjunto de disponibilidade existente. Os seguintes detalhes do gráfico que pode misturar na mesma série de disponibilidade.

Aqui está a matriz de suporte para misturar diferentes tipos de VMs:

Conjunto de disponibilidade de série&|Segunda VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeiro VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas as outras séries não podiam estar no mesmo conjunto de disponibilidade porque requerem um hardware específico.

O tamanho do VM A8/A9 não pode ser misturado devido à exigência da rede de backend dedicada RDMA.
