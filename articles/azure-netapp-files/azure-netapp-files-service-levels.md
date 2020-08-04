---
title: Níveis de serviço para Ficheiros Azure NetApp Microsoft Docs
description: Descreve o desempenho de produção para os níveis de serviço dos Ficheiros Azure NetApp.
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
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533126"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviços do Azure NetApp Files
Os níveis de serviço são um atributo de um pool de capacidade. Os níveis de serviço são definidos e diferenciados pela produção máxima permitida para um volume no pool de capacidade com base no contingente atribuído ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço suportados

O Azure NetApp Files suporta três níveis de serviço: *Ultra,* *Premium*e *Standard*. 

* <a name="Ultra"></a>Armazenamento ultra

    O nível de armazenamento ultra fornece até 128 MiB/s de produção por 1 TiB da quota de volume atribuída. 

* <a name="Premium"></a>Armazenamento premium

    O nível de armazenamento Premium fornece até 64 MiB/s de produção por 1 TiB da quota de volume atribuída. 

* <a name="Standard"></a>Armazenamento padrão

    O nível de armazenamento standard fornece até 16 MiB/s de produção por 1 TiB da quota de volume atribuída.

## <a name="throughput-limits"></a>Limites de débito

O limite de produção de um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço da capacidade de piscina a que o volume pertence
* A quota atribuída ao volume  

Este conceito é ilustrado no diagrama abaixo:

![Ilustração de nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

No exemplo 1 acima, um volume de um pool de capacidade com o nível de armazenamento Premium que é atribuído 2 TiB de quota será atribuído um limite de produção de 128 MiB/s (2 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho da piscina de capacidade ou do consumo real de volume.

No exemplo 2 acima, um volume de um pool de capacidade com o nível de armazenamento Premium que é atribuído 100 GiB de quota será atribuído um limite de produção de 6,25 MiB/s (0.09765625 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho da piscina de capacidade ou do consumo real de volume.

## <a name="next-steps"></a>Passos seguintes

- [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Modelo de custo para os Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- [Acordo de Nível de Serviço (SLA) para ficheiros Azure NetApp](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Alterar o nível de serviço de um volume de forma dinâmica](dynamic-change-volume-service-level.md) 