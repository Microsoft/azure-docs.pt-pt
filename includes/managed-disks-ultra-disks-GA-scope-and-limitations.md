---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008652"
---
Por enquanto, os discos ultra têm limitações adicionais, são os seguintes:

As únicas opções de redundância de infraestruturas atualmente disponíveis para discos ultra são as zonas de disponibilidade. Os VMs que utilizam outras opções de despedimento não podem anexar um disco ultra.

O quadro seguinte descreve as regiões em que os discos ultra estão disponíveis, bem como as respetivas opções de disponibilidade:

> [!NOTE]
> Algumazona de disponibilidade nestas regiões não oferece discos ultra.

|Regiões  |Sem redundância de infraestruturas  |Zonas de disponibilidade  |
|---------|---------|---------|
|E.U.A. Oeste     |Sim         |Não         |
|E.U.A.Oeste 2    |Não         |Sim         |
|E.U.A. Leste     |Não         |Sim         |
|E.U.A. Leste 2     |Não         |Sim         |
|Sudeste Asiático     |Não         |Sim         |
|Europa do Norte     |Não         |Sim         |
|Europa ocidental     |Não         |Sim         |
|Sul do Reino Unido     |Não         |Sim         |

- São suportados apenas nas seguintes séries VM:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todos os tamanhos vm estão disponíveis em todas as regiões apoiadas com discos ultra
- Estão disponíveis apenas como discos de dados e suportam apenas a dimensão do sector físico de 4k. Devido ao tamanho do sector nativo 4K do Ultra Disk, existem algumas aplicações que não serão compatíveis com discos ultra. Um exemplo seria a Oracle Database, que requer a libertação 12.2 ou mais tarde para suportar discos ultra.  
- Só pode ser criado como discos vazios  
- Atualmente não suporta instantâneos de disco, imagens VM, conjuntos de disponibilidade, Anfitriões Dedicados Azure ou encriptação de disco Azure
- Atualmente não apoia a integração com o Azure Backup ou a Recuperação do Site Azure
- O limite máximo atual para os IOPS em VMs GA é de 80.000.

Os discos Ultra Azure oferecem até 16 TiB por região por subscrição por padrão, mas os discos ultra suportam uma maior capacidade por pedido. Para solicitar um aumento de capacidade, contacte o Suporte Azure.