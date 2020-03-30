---
title: Discos de resolução de problemas ligados aos VMs azure [ Microsoft Docs
description: Fornece links para os recursos de resolução de problemas para discos rígidos virtuais da máquina Azure (VHDs).
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061187"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Discos de resolução de problemas ligados a VMs Azure 

As Máquinas Virtuais Azure (VMs) dependem de Discos Rígidos Virtuais (VHDs) para o disco OS e quaisquer discos de dados anexados. Os VHDs são armazenados como bolhas de página em uma ou mais contas de Armazenamento Azure. Este artigo aponta para problemas de resolução de conteúdos para questões comuns que podem surgir com VHDs. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Erros de eliminação de armazenamento de resolução de problemas para um VM

Em certos casos, pode encontrar um erro ao apagar um recurso de armazenamento quando um VM numa implementação do Gestor de Recursos contém VHDs anexados. Para obter ajuda na resolução desta questão, consulte um dos seguintes artigos: 

  * Em VMs Linux: Erros de [eliminação de armazenamento na implementação do Gestor de Recursos](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Nos VMs do Windows: Erros de [eliminação de armazenamento na implementação do Gestor de Recursos](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Reinicializações inesperadas de VMs com VHDs anexados

Se encontrar reboots inesperados de um VM com um grande número de VHDs anexados, consulte um dos seguintes artigos:

  * Em VMs Linux: [Reboots inesperados de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Em VMs windows: [Reboots inesperados de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
