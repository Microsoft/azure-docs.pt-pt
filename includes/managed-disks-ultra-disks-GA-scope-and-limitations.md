---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa9327bd0ba6763aa4e89630611aabb3c5195655
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448744"
---
Por enquanto, os discos ultra têm limitações adicionais, são os seguintes:

As únicas opções de redundância de infraestrutura atualmente disponíveis para discos ultra são zonas de disponibilidade. Os VMs que utilizam quaisquer outras opções de redundância não podem anexar um disco ultra.

O quadro que se segue descreve as regiões em que os discos ultra estão disponíveis, bem como as respetivas opções de disponibilidade:

> [!NOTE]
> Se uma região da lista seguinte não tiver zonas de disponibilidade ultra-disquetes capazes, então os VM nessa região devem ser implantados sem opções de redundância de infraestrutura para anexar um disco ultra.

|Regiões  |Número de zonas de disponibilidade que suportam discos ultra  |
|---------|---------|
|US Gov - Virginia     |Nenhuma         |
|E.U.A. Centro-Sul     |Nenhuma         |
|E.U.A. Central     |Três zonas         |
|E.U.A. Oeste     |Nenhuma         |
|E.U.A.Oeste 2    |Três zonas         |
|E.U.A. Leste     |Três zonas         |
|E.U.A. Leste 2     |Duas zonas         |
|Ásia do Sul     |Três zonas         |
|Europa do Norte     |Três zonas          |
|Europa Ocidental     |Três zonas          |
|Sul do Reino Unido     |Três zonas          |
|Leste do Japão     |Duas zonas         |
|França Central    |Duas zonas        |


- São suportados apenas nas seguintes séries VM:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todos os tamanhos de VM estão disponíveis em todas as regiões suportadas com discos ultra
- Só estão disponíveis como discos de dados e suportam apenas o tamanho do sector físico 4k. Devido ao tamanho do sector nativo 4K do Ultra Disk, existem algumas aplicações que não serão compatíveis com discos ultra. Um exemplo seria a Oracle Database, que requer a libertação 12.2 ou posterior para suportar discos ultra.  
- Só pode ser criado como discos vazios  
- Atualmente não suporta instantâneos de disco, imagens VM, conjuntos de disponibilidade, Anfitriões Dedicados Azure ou encriptação de disco Azure
- Atualmente não suporta a integração com a Azure Backup ou Azure Site Recovery
- Apenas suporta leituras não em cache e escritos não em cache
- O limite máximo atual para iops em VMs GA é de 80.000.

Os discos Azure ultra oferecem até 16 TiB por região por subscrição por padrão, mas os discos ultra suportam maior capacidade por pedido. Para solicitar um aumento de capacidade, contacte o Suporte Azure.