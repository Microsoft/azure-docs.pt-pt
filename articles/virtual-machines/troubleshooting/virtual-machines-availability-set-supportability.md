---
title: Suporte à adição de VMs do Azure a um conjunto de disponibilidade existente | Microsoft Docs
description: Este artigo fornece uma matriz de suporte sobre qual série de VMs você pode misturar no mesmo conjunto de disponibilidade
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: a9ca8f219bef787de04b51600209bfd3a24dd166
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122925"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Suporte à adição de VMs do Azure a um conjunto de disponibilidade existente

Ocasionalmente, você pode encontrar limitações ao adicionar novas VMs (máquinas virtuais) a um conjunto de disponibilidade existente. O gráfico a seguir fornece detalhes sobre a série de VMs que você pode misturar no mesmo conjunto de disponibilidade.

Aqui está a matriz de suporte para misturar diferentes tipos de VMs:

Série & conjunto de disponibilidade|Segunda VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeira VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas as outras séries não puderam estar no mesmo conjunto de disponibilidade porque exigem um hardware específico.

O tamanho da VM A8/A9 não pode ser misturado devido ao requisito na rede de back-end RDMA dedicada.
