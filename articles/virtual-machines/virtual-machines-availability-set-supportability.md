---
title: Suportabilidade de adicionar as VMs do Azure para um disponibilidade existente definido | Documentos da Microsoft
description: Suportabilidade de adicionar as VMs do Azure a um conjunto de disponibilidade existente.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092145"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Suportabilidade de adicionar as VMs do Azure a um conjunto de disponibilidade existente

Poderá, ocasionalmente, encontrar limitações ao adicionar novas máquinas virtuais (VMs) a um conjunto de disponibilidade existente. A tabela a seguir fornece detalhes sobre a série VM pode misturar no mesmo conjunto de disponibilidade.

Segue-se a matriz de suporte para combinar tipos diferentes de VMs:

Série e o conjunto de disponibilidade|Segunda VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primeira VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas as outras séries não é possível no mesmo conjunto pois requerem um hardware específico de disponibilidade.

Não é possível misturar o tamanho de VM a8/A9 devido a requirment na rede de back-end RDMA dedicada.
