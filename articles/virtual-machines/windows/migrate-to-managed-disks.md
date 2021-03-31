---
title: Migrar VMs Azure para Discos Geridos
description: Migrar máquinas virtuais Azure criadas usando discos não geridos em contas de armazenamento para usar Discos Geridos.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 65b36db5185390cd6527a66c91eecd4712623df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564344"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs Azure para Discos Geridos em Azure

A Azure Managed Disks simplifica a sua gestão de armazenamento removendo a necessidade de gerir separadamente as contas de armazenamento.  Também pode migrar os seus VMs Azure existentes para Discos Geridos para beneficiar de uma melhor fiabilidade dos VMs num Conjunto de Disponibilidade. Garante que os discos de diferentes VMs num Conjunto de Disponibilidades estão suficientemente isolados uns dos outros para evitar um único ponto de avaria. Coloca automaticamente discos de diferentes VMs num Conjunto de Disponibilidade em diferentes unidades de escala de armazenamento (selos) que limitam o impacto de falhas de unidade de escala de armazenamento única causadas por falhas de hardware e software.
Com base nas suas necessidades, pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte o nosso artigo [Selecione um tipo de disco](../disks-types.md)

## <a name="migration-scenarios"></a>Cenários de migração

Pode migrar para Discos Geridos em seguintes cenários:

|Scenario  |Artigo  |
|---------|---------|
|Converter VMs autónomas e VMs num conjunto de disponibilidade para discos geridos     |[Converter VMs para utilizar discos geridos](convert-unmanaged-to-managed-disks.md)         |
|Converter um único VM do clássico para o Gestor de Recursos em discos geridos     |[Criar um VM a partir de um VHD clássico](create-vm-specialized-portal.md)         |
|Converter todos os VMs num vNet de clássico para Gestor de Recursos em discos geridos     |[Migrar recursos iaaS do clássico para o Gestor de Recursos](../migration-classic-resource-manager-ps.md) e depois converter um [VM de discos não geridos para discos geridos](convert-unmanaged-to-managed-disks.md)         |
|Atualizar VMs com discos não geridos padrão para VMs com discos premium geridos     | Em primeiro lugar, [converta uma máquina virtual windows de discos não geridos para discos geridos](convert-unmanaged-to-managed-disks.md). Em [seguida, atualize o tipo de armazenamento de um disco gerido](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [Discos Geridos](../managed-disks-overview.md)
- Reveja os [preços dos Discos Geridos.](https://azure.microsoft.com/pricing/details/managed-disks/)