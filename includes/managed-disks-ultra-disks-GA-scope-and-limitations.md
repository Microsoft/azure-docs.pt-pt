---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e94b14584f8eece3d772f67b680df4a2ffb8b5cd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603275"
---
Por enquanto, os discos ultra têm limitações adicionais, são os seguintes:

As únicas opções de redundância de infraestrutura atualmente disponíveis para discos ultra são zonas de disponibilidade. Os VMs que utilizam quaisquer outras opções de redundância não podem anexar um disco ultra.

O quadro que se segue descreve as regiões em que os discos ultra estão disponíveis, bem como as respetivas opções de disponibilidade:

> [!NOTE]
> Se uma região da lista seguinte não tiver zonas de disponibilidade ultra-disquetes capazes, então os VM nessa região devem ser implantados sem opções de redundância de infraestrutura para anexar um disco ultra.

|Regiões  |Opções de redundância  |
|---------|---------|
|Sul do Brasil     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|Índia Central     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|Ásia Leste     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|Alemanha Centro-Oeste     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|Coreia do Sul Central     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|E.U.A. Centro-Sul    |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|US Gov - Arizona     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|US Gov - Virginia     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|US Gov - Texas     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|E.U.A. Oeste     |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)        |
|Austrália Central    |Apenas VMs únicos (conjuntos de disponibilidade e conjuntos de escala de máquina virtual não são suportados)|
|Leste da Austrália     |Três zonas de disponibilidade         |
|Sudeste Asiático    |Três zonas de disponibilidade        |
|Canadá Central     |Três zonas de disponibilidade          |
|E.U.A. Central     |Três zonas de disponibilidade          |
|E.U.A. Leste     |Três zonas de disponibilidade          |
|E.U.A. Leste 2     |Três zonas de disponibilidade         |
|França Central    |Duas zonas de disponibilidade        |
|Leste do Japão    |Três zonas de disponibilidade        |
|Europa do Norte    |Três zonas de disponibilidade        |
|Sul do Reino Unido    |Três zonas de disponibilidade        |
|Europa Ocidental    | Três zonas de disponibilidade|
|E.U.A. Oeste 2    |Três zonas de disponibilidade|

- São suportados apenas nas seguintes séries VM:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todos os tamanhos de VM estão disponíveis em todas as regiões suportadas com discos ultra.
- Só estão disponíveis como discos de dados. 
- Suporte tamanho do sector físico 4k por padrão. O tamanho do sector 512E está disponível como uma oferta geralmente disponível (sem necessidade de inscrição), mas atualmente só está disponível usando o CLI ou a powershell. A maioria das aplicações são compatíveis com tamanhos do sector 4k, mas, algumas requerem 512 tamanhos do setor byte. Um exemplo seria a Oracle Database, que requer a libertação 12.2 ou mais tarde para suportar os discos nativos 4k. Para versões mais antigas do Oracle DB, é necessário o tamanho do sector 512 byte.
- Só podem ser criados como discos vazios.
- Atualmente não suporta instantâneos de disco, imagens VM, conjuntos de disponibilidade, Anfitriões Dedicados Azure ou encriptação de disco Azure.
- Atualmente não suporta a integração com a Azure Backup ou Azure Site Recovery.
- Apenas suporta leituras não em cache e escritos não em cache.
- O limite máximo atual para iops em VMs GA é de 80.000.

Os discos Azure ultra oferecem até 16 TiB por região por subscrição por padrão, mas os discos ultra suportam maior capacidade por pedido. Para solicitar um aumento de capacidade, contacte o Suporte Azure.
