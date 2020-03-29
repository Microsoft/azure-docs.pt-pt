---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935912"
---
Por enquanto, os discos ultra têm limitações adicionais, são os seguintes:

- São apoiados nas seguintes regiões, com um número variado de zonas de disponibilidade por região:
    - E.U.A. Leste 2
    - E.U.A. Leste
    - E.U.A.Oeste 2
    - Sudeste Asiático
    - Europa do Norte
    - Europa ocidental
    - Sul do Reino Unido 
- Só pode ser utilizado com zonas de disponibilidade (conjuntos de disponibilidade e implantações vm únicas fora das zonas não terão a capacidade de anexar um disco ultra)
- São suportados apenas nas seguintes séries VM:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todos os tamanhos vm estão disponíveis em todas as regiões apoiadas com discos ultra
- Estão disponíveis apenas como discos de dados e suportam apenas a dimensão do sector físico de 4k. Devido ao tamanho do sector nativo 4K do Ultra Disk, existem algumas aplicações que não serão compatíveis com discos ultra. Um exemplo seria a Oracle Database, que requer a libertação 12.2 ou mais tarde para suportar discos ultra.  
- Só pode ser criado como discos vazios  
- Ainda não suporte instantâneos de disco, imagens VM, conjuntos de disponibilidade e encriptação de disco Azure
- Não apoie ainda a integração com o Azure Backup ou a Recuperação do Site Azure
- O limite máximo atual para os IOPS em VMs GA é de 80.000.
- Se você gostaria de participar em uma pré-visualização limitada de um VM que pode realizar UltraDiskFeedback@microsoft 160.000 IOPS com discos ultra, por favor envie um e-mail .com