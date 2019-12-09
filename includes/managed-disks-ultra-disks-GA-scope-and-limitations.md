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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935912"
---
Por enquanto, ultra discos têm limitações adicionais, como a seguir:

- Têm suporte nas seguintes regiões, com um número variável de zonas de disponibilidade por região:
    - Este dos E.U.A. 2
    - Este dos E.U.A.
    - E.U.A. Oeste 2
    - Sudeste da Ásia
    - Europa do Norte
    - Europa Ocidental
    - Sul do Reino Unido 
- Só pode ser usado com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora das zonas não terão a capacidade de anexar um ultra Disk)
- Há suporte apenas na seguinte série de VMs:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nem todo tamanho de VM está disponível em todas as regiões com suporte com ultra discos
- Estão disponíveis somente como discos de dados e dão suporte apenas ao tamanho de setor físico de 4K. Devido ao tamanho do setor nativo de 4K do ultra Disk, há alguns aplicativos que não serão compatíveis com ultra discos. Um exemplo seria Oracle Database, que requer a versão 12,2 ou posterior para dar suporte a ultra discos.  
- Só pode ser criado como discos vazios  
- Ainda não há suporte para instantâneos de disco, imagens de VM, conjuntos de disponibilidade e Azure Disk Encryption
- Ainda não há suporte para integração com o backup do Azure ou Azure Site Recovery
- O limite máximo atual para IOPS em VMs GA é 80.000.
- Se você quiser participar de uma versão prévia limitada de uma VM que pode atingir 160.000 IOPS com ultra discos, envie um email para UltraDiskFeedback@microsoft. com