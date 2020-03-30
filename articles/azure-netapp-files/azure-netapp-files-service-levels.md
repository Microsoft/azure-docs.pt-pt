---
title: Níveis de serviço para Ficheiros Azure NetApp [ Microsoft Docs
description: Descreve o desempenho da entrada para os níveis de serviço dos Ficheiros Azure NetApp.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832590"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviços do Azure NetApp Files
Os níveis de serviço são um atributo de um pool de capacidade. Os níveis de serviço são definidos e diferenciados pela potência máxima permitida para um volume no conjunto de capacidades com base no contingente atribuído ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço suportados

O Azure NetApp Files suporta três níveis de serviço: *Ultra,* *Premium*e *Standard.* 

* <a name="Ultra"></a>Ultra armazenamento

    O nível de armazenamento Ultra fornece até 128 MiB/s de produção por 1 TiB de quota de volume atribuída. 

* <a name="Premium"></a>Armazenamento premium

    O nível de armazenamento Premium fornece até 64 MiB/s de produção por 1 TiB de quota de volume atribuída. 

* <a name="Standard"></a>Armazenamento Standard

    O nível de armazenamento Standard fornece até 16 MiB/s de produção por 1 TiB de quota de volume atribuída.

## <a name="throughput-limits"></a>Limites de débito

O limite de entrada para um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço do pool de capacidade a que o volume pertence
* A quota atribuída ao volume  

Este conceito é ilustrado no diagrama abaixo:

![Ilustração de nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

No exemplo 1 acima, será atribuído um volume de um conjunto de capacidades com o nível de armazenamento Premium atribuído a 2 TiB de quota será atribuído um limite de produção de 128 MiB/s (2 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho do pool de capacidade ou do consumo real de volume.

No exemplo 2 acima, será atribuído um volume de um pool de capacidade com o nível de armazenamento Premium a atribuir 100 GiB de quota será atribuído um limite de produção de 6,25 MiB/s (0.09765625 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho do pool de capacidade ou do consumo real de volume.

## <a name="next-steps"></a>Passos seguintes

- Consulte a página de preços dos [Ficheiros Azure NetApp](https://azure.microsoft.com/pricing/details/storage/netapp/) para o preço dos diferentes níveis de serviço
- Ver [modelo de custo para Ficheiros Azure NetApp](azure-netapp-files-cost-model.md) para o cálculo do consumo de capacidade num conjunto de capacidades 
- [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- Ver [Acordo de Nível de Serviço (SLA) para Ficheiros Azure NetApp](https://azure.microsoft.com/support/legal/sla/netapp/)