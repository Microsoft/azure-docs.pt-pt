---
title: Níveis de serviço para ficheiros Azure NetApp | Microsoft Docs
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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 7eac6a40476cffe875a03de49c9c9311ffbf4d39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017071"
---
# <a name="service-levels-for-azure-netapp-files"></a>Níveis de serviços do Azure NetApp Files
Os níveis de serviço são um atributo de um pool de capacidade. Os níveis de serviço são definidos e diferenciados pela produção máxima permitida para um volume no pool de capacidade com base no contingente atribuído ao volume.

## <a name="supported-service-levels"></a>Níveis de serviço suportados

O Azure NetApp Files suporta três níveis de serviço: *Ultra,* *Premium* e *Standard*. 

* <a name="Ultra"></a>Armazenamento ultra

    O nível de armazenamento Ultra fornece até 128 MiB/s de produção por 1 TiB de capacidade aprovisionada. 

* <a name="Premium"></a>Armazenamento premium

    O nível de armazenamento Premium fornece até 64 MiB/s de produção por 1 TiB de capacidade abastada. 

* <a name="Standard"></a>Armazenamento padrão

    O nível de armazenamento standard fornece até 16 MiB/s de produção por 1 TiB de capacidade abastada.

## <a name="throughput-limits"></a>Limites de débito

O limite de produção de um volume é determinado pela combinação dos seguintes fatores:
* O nível de serviço da capacidade de piscina a que o volume pertence
* A quota atribuída ao volume  
* O tipo QoS *(automático* ou *manual)* da piscina de capacidade  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Limite de produção exemplos de volumes num pool de capacidade de QoS automático

O diagrama a seguir mostra exemplos de limite de produção de volumes num pool de capacidade de QoS automático:

![Ilustração de nível de serviço](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* No exemplo 1, será atribuído um volume de um pool de capacidade de QoS automático com o nível de armazenamento Premium atribuído 2 TiB de quota será atribuído um limite de produção de 128 MiB/s (2 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho da piscina de capacidade ou do consumo real de volume.

* No exemplo 2, será atribuído um volume de um pool de capacidade de QoS automático com o nível de armazenamento Premium atribuído a 100 GiB de quota será atribuído um limite de produção de 6,25 MiB/s (0.09765625 TiB * 64 MiB/s). Este cenário aplica-se independentemente do tamanho da piscina de capacidade ou do consumo real de volume.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Limite de produção exemplos de volumes num pool manual de capacidade QoS 

Se utilizar um pool manual de capacidade QoS, pode atribuir a capacidade e a produção para um volume de forma independente. Quando criar um volume num pool manual de capacidade QoS, pode especificar o valor de produção (MiB/S). A produção total atribuída a volumes num pool manual de capacidade QoS depende do tamanho da piscina e do nível de serviço. É tapado por (Tamanho da Piscina de Capacidade em TiB x Nível de Serviço Nível De Produção/TiB). Por exemplo, um pool de capacidade de 10 TiB com o nível de serviço Ultra tem uma capacidade total de produção de 1280 MiB/s (10 TiB x 128 MiB/s/TiB) disponível para os volumes.

Por exemplo, para um sistema SAP HANA, este pool de capacidade pode ser usado para criar os seguintes volumes. Cada volume fornece o tamanho e produção individual para satisfazer os requisitos da sua aplicação:

* Volume de dados SAP HANA: Tamanho 4 TiB com até 704 MiB/s
* Volume de log SAP HANA: Tamanho 0.5 TiB com até 256 MiB/s
* Volume compartilhado SAP HANA: Tamanho 1 TiB com até 64 MiB/s
* Volume de backup SAP HANA: Tamanho 4.5 TiB com até 256 MiB/s

O diagrama a seguir ilustra os cenários para os volumes SAP HANA:

![Cenários de volume QoS SAP HANA](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Passos seguintes

- [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Modelo de custo para os Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Criar uma piscina de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- [Acordo de Nível de Serviço (SLA) para ficheiros Azure NetApp](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Alterar o nível de serviço de um volume de forma dinâmica](dynamic-change-volume-service-level.md) 
- [Objetivos ao nível do serviço para a replicação entre regiões](cross-region-replication-introduction.md#service-level-objectives)
