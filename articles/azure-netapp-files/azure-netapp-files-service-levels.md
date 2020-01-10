---
title: Níveis de serviço para Azure NetApp Files | Microsoft Docs
description: Descreve o desempenho da taxa de transferência para os níveis de serviço de Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832590"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviços do Azure NetApp Files
Os níveis de serviço são um atributo de um pool de capacidade. Os níveis de serviço são definidos e diferenciados pela taxa de transferência máxima permitida para um volume no pool de capacidade com base na cota atribuída ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço com suporte

O Azure NetApp Files dá suporte a três níveis de serviço: *ultra*, *Premium*e *Standard*. 

* <a name="Ultra"></a>Ultra armazenamento

    A camada de armazenamento ultra fornece até 128 MiB/s de taxa de transferência por 1 TiB de cota de volume atribuída. 

* <a name="Premium"></a>Armazenamento Premium

    A camada de armazenamento Premium fornece até 64 MiB/s de taxa de transferência por 1 TiB de cota de volume atribuída. 

* <a name="Standard"></a>Armazenamento Standard

    A camada de armazenamento Standard fornece até 16 MiB/s de taxa de transferência por 1 TiB de cota de volume atribuída.

## <a name="throughput-limits"></a>Limites de débito

O limite de taxa de transferência para um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço do pool de capacidade ao qual o volume pertence
* A cota atribuída ao volume  

Esse conceito é ilustrado no diagrama a seguir:

![Ilustração de nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

No exemplo 1 acima, um volume de um pool de capacidade com a camada de armazenamento Premium atribuído a 2 TiB de cota será atribuído a um limite de taxa de transferência de 128 MiB/s (2 TiB * 64 MiB/s). Esse cenário se aplica independentemente do tamanho do pool de capacidade ou do consumo real do volume.

No exemplo 2 acima, um volume de um pool de capacidade com a camada de armazenamento Premium atribuído a 100 GiB de cota será atribuído a um limite de taxa de transferência de 6,25 MiB/s (0, 9765625 TiB * 64 MiB/s). Esse cenário se aplica independentemente do tamanho do pool de capacidade ou do consumo real do volume.

## <a name="next-steps"></a>Passos seguintes

- Consulte a [página de preços do Azure NetApp files](https://azure.microsoft.com/pricing/details/storage/netapp/) para obter o preço de diferentes níveis de serviço
- Consulte o [modelo de custo para Azure NetApp files](azure-netapp-files-cost-model.md) para o cálculo do consumo de capacidade em um pool de capacidade 
- [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- Consulte [contrato de nível de serviço (SLA) para Azure NetApp files](https://azure.microsoft.com/support/legal/sla/netapp/)