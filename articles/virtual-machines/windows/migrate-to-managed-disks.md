---
title: VMs azure migratórios para discos geridos
description: Máquinas virtuais Migrate Azure criadas com discos não geridos em contas de armazenamento para utilizar discos geridos.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921356"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>VMs azure migratórios para discos geridos em Azure

A Azure Managed Disks simplifica a sua gestão de armazenamento removendo a necessidade de gerir separadamente as contas de armazenamento.  Também pode migrar os seus VMs Azure existentes para Discos Geridos para beneficiar de uma melhor fiabilidade dos VMs num Conjunto de Disponibilidade. Garante que os discos de diferentes VMs num Conjunto de Disponibilidade estão suficientemente isolados uns dos outros para evitar um único ponto de avarias. Coloca automaticamente discos de diferentes VMs num Conjunto de Disponibilidade em diferentes unidades de escala de armazenamento (selos) que limita o impacto das falhas da unidade de armazenamento único causadas devido a falhas de hardware e software.
Com base nas suas necessidades, pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte o nosso artigo [Selecione um tipo](disks-types.md) de disco

## <a name="migration-scenarios"></a>Cenários de migração

Pode migrar para Discos Geridos em seguintes cenários:

|Cenário  |Artigo  |
|---------|---------|
|Converter VMs autónomas e VMs num conjunto de disponibilidade para discos geridos     |[Converter VMs para usar discos geridos](convert-unmanaged-to-managed-disks.md)         |
|Converter um único VM do clássico para O Gestor de Recursos em discos geridos     |[Crie um VM a partir de um VHD clássico](create-vm-specialized-portal.md)         |
|Converta todos os VMs num vNet de clássico para Gestor de Recursos em discos geridos     |[Migrar recursos IaaS do clássico para o Gestor de Recursos](migration-classic-resource-manager-ps.md) e, em seguida, converter um [VM de discos não geridos para discos geridos](convert-unmanaged-to-managed-disks.md)         |
|Upgrade VMs com discos não geridos padrão para VMs com discos premium geridos     | Primeiro, [converter uma máquina virtual do Windows de discos não geridos para discos geridos](convert-unmanaged-to-managed-disks.md). Em seguida, [atualize o tipo de armazenamento de um disco gerido](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [discos geridos](managed-disks-overview.md)
- Reveja os [preços dos Discos Geridos](https://azure.microsoft.com/pricing/details/managed-disks/).
