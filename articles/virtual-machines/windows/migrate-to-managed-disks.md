---
title: Migrar VMs do Azure para Managed Disks | Microsoft Docs
description: Migre as máquinas virtuais do Azure criadas usando discos não gerenciados nas contas de armazenamento para usar Managed Disks.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7487d53195b45664b094ccc3a8418bd0c700e052
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693486"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para Managed Disks no Azure

O Azure Managed Disks simplifica o gerenciamento de armazenamento, eliminando a necessidade de gerenciar contas de armazenamento separadamente.  Você também pode migrar suas VMs do Azure existentes para Managed Disks para se beneficiar da melhor confiabilidade das VMs em um conjunto de disponibilidade. Ele garante que os discos de VMs diferentes em um conjunto de disponibilidade estejam suficientemente isolados entre si para evitar um ponto único de falhas. Ele coloca automaticamente discos de diferentes VMs em um conjunto de disponibilidade em diferentes unidades de escala de armazenamento (carimbos), o que limita o impacto de falhas de unidade de escala de armazenamento único causadas devido a falhas de hardware e software.
Com base em suas necessidades, você pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte nosso artigo [selecionar um tipo de disco](disks-types.md)

## <a name="migration-scenarios"></a>Cenários de migração

Você pode migrar para o Managed Disks nos seguintes cenários:

|Cenário  |Artigo  |
|---------|---------|
|Converter VMs autónomas e VMs num conjunto de disponibilidade para discos geridos     |[Converter VMs para usar discos gerenciados](convert-unmanaged-to-managed-disks.md)         |
|Converter uma única VM do clássico para o Resource Manager em discos gerenciados     |[Criar uma VM com base em um VHD clássico](create-vm-specialized-portal.md)         |
|Converter todas as VMs em uma vNet do clássico para o Gerenciador de recursos em discos gerenciados     |[Migre recursos de IaaS do clássico para o Gerenciador de recursos](migration-classic-resource-manager-ps.md) e [Converta uma VM de discos não gerenciados em discos gerenciados](convert-unmanaged-to-managed-disks.md)         |
|Atualizar VMs com discos não gerenciados padrão para VMs com discos Premium gerenciados     | Primeiro, [Converta uma máquina virtual do Windows de discos não gerenciados em discos gerenciados](convert-unmanaged-to-managed-disks.md). Em seguida, [atualize o tipo de armazenamento de um disco gerenciado](convert-disk-storage.md).         |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Managed disks](managed-disks-overview.md)
- Examine os [preços de Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/).
