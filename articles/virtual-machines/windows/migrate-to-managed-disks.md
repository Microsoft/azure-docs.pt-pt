---
title: Migrar VMs do Azure para discos geridos | Documentos da Microsoft
description: Migre máquinas virtuais do Azure criadas com discos não geridos nas contas de armazenamento para utilizar os Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7a390c5231f715ce778c0cc267211b8bfb5934d2
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418410"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para discos geridos no Azure

Managed Disks do Azure simplifica a gestão de armazenamento, eliminando a necessidade de gerir separadamente contas de armazenamento.  Também pode migrar as VMs do Azure existentes para os Managed Disks para beneficiar de confiabilidade de VMs num conjunto de disponibilidade. Ele garante que os discos de VMs diferentes no conjunto de disponibilidade estão suficientemente isolados uns dos outros para evitar único ponto de falhas. Coloca automaticamente discos de VMs diferentes no conjunto de disponibilidade em unidades de escala de armazenamento diferentes (carimbos de data /) que limita o impacto das falhas de unidade de escala de armazenamento únicas provocadas por hardware e de falhas de software.
Com base nas suas necessidades, pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponível, consulte nosso artigo [selecione um tipo de disco](disks-types.md)

## <a name="migration-scenarios"></a>Cenários de migração

Pode migrar para Managed Disks nos seguintes cenários:

|Cenário  |Artigo  |
|---------|---------|
|Converter VMs autónomas e VMs num conjunto de disponibilidade para discos geridos     |[Converter VMs para utilizar discos geridos](convert-unmanaged-to-managed-disks.md)         |
|Converter uma VM única de clássico para Resource Manager em discos geridos     |[Criar uma VM a partir de um VHD clássico](create-vm-specialized-portal.md)         |
|Converter todas as VMs numa vNet de clássico para Resource Manager em discos geridos     |[Migrar recursos de IaaS da implementação clássica para Resource Manager](migration-classic-resource-manager-ps.md) e, em seguida, [converter uma VM de discos não geridos para managed disks](convert-unmanaged-to-managed-disks.md)         |
|Atualizar VMs com discos não geridos standard para VMs com discos geridos premium     | Primeiro, [converter uma máquina virtual do Windows de discos não geridos para managed disks](convert-unmanaged-to-managed-disks.md). Em seguida, [atualizar o tipo de armazenamento de um disco gerido](convert-disk-storage.md).         |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Managed Disks](managed-disks-overview.md)
- Reveja os [os preços dos discos geridos](https://azure.microsoft.com/pricing/details/managed-disks/).
