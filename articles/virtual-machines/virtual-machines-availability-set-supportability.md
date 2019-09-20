---
title: Suporte à adição de VMs do Azure a um conjunto de disponibilidade existente | Microsoft Docs
description: Suporte à adição de VMs do Azure a um conjunto de disponibilidade existente.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155443"
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
