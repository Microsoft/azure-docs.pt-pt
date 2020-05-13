---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5f14a269478541eaa5852697a917afb3d771841a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196960"
---
Por enquanto, os discos ultra têm limitações adicionais, são os seguintes:

As únicas opções de redundância de infraestruturas atualmente disponíveis para discos ultra são as zonas de disponibilidade. Os VMs que utilizam outras opções de despedimento não podem anexar um disco ultra.

O quadro seguinte descreve as regiões em que os discos ultra estão disponíveis, bem como as respetivas opções de disponibilidade:

> [!NOTE]
> Se uma região da lista seguinte não tiver zonas de disponibilidade ultra-disco capazes, então as VMs dessa região devem ser implantadas sem quaisquer opções de despedimento de infraestruturas para anexar um disco ultra.

|Regiões  |Número de zonas de disponibilidade suportando discos ultra  |
|---------|---------|
|US Gov - Virginia     |Nenhum         |
|E.U.A. Centro-Sul     |Nenhum         |
|E.U.A. Central     |Três zonas         |
|E.U.A. Oeste     |Nenhum         |
|E.U.A.Oeste 2    |Três zonas         |
|E.U.A. Leste     |Três zonas         |
|E.U.A. Leste 2     |Duas zonas         |
|Sudeste Asiático     |Três zonas         |
|Europa do Norte     |Três zonas          |
|Europa ocidental     |Três zonas          |
|Sul do Reino Unido     |Três zonas          |
|Leste do Japão     |Duas zonas         |



- São suportados apenas nas seguintes séries VM:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todos os tamanhos vm estão disponíveis em todas as regiões apoiadas com discos ultra
- Estão disponíveis apenas como discos de dados e suportam apenas a dimensão do sector físico de 4k. Devido ao tamanho do sector nativo 4K do Ultra Disk, existem algumas aplicações que não serão compatíveis com discos ultra. Um exemplo seria a Oracle Database, que requer a libertação 12.2 ou mais tarde para suportar discos ultra.  
- Só pode ser criado como discos vazios  
- Atualmente não suporta instantâneos de disco, imagens VM, conjuntos de disponibilidade, Anfitriões Dedicados Azure ou encriptação de disco Azure
- Atualmente não apoia a integração com o Azure Backup ou a Recuperação do Site Azure
- O limite máximo atual para os IOPS em VMs GA é de 80.000.

Os discos Ultra Azure oferecem até 16 TiB por região por subscrição por padrão, mas os discos ultra suportam uma maior capacidade por pedido. Para solicitar um aumento de capacidade, contacte o Suporte Azure.