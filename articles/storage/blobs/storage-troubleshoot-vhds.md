---
title: Resolver problemas relacionados com discos anexados a VMs do Azure | Documentos da Microsoft
description: Fornece links para recursos para a máquina virtual do Azure de discos rígidos virtuais (VHDs) de resolução de problemas.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150776"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Resolver problemas relacionados com discos anexados a VMs do Azure 

Máquinas virtuais do Azure (VMs) baseiam-se em discos de dados de discos rígidos virtuais (VHDs) para o disco do SO e qualquer ligados. VHD são armazenadas como blobs de páginas num ou mais contas de armazenamento do Azure. Este artigo aponta para conteúdo para problemas comuns que podem surgir com VHDs de resolução de problemas. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Resolver problemas de erros de eliminação de armazenamento para uma VM

Em certos casos, pode encontrar um erro enquanto a eliminar um recurso de armazenamento, quando uma VM numa implementação do Gestor de recursos contém ligado VHDs. Para ajudar a resolver este problema, consulte um dos seguintes artigos: 

  * Em VMs do Linux: [Erros de eliminação de armazenamento na implementação do Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Em VMs do Windows: [Erros de eliminação de armazenamento na implementação do Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Resolver problemas de reinícios inesperados de VMs com VHDs ligadas

Se encontrar reinícios inesperados de uma VM com um grande número de VHDs ligadas, consulte um dos seguintes artigos:

  * Em VMs do Linux: [Reinícios inesperados de VMs com VHDs ligados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Em VMs do Windows: [Reinícios inesperados de VMs com VHDs ligados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
