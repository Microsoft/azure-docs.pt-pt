---
title: Mover VMs do Azure para uma nova assinatura ou grupo de recursos
description: Use Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou assinatura.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b03e9618e621216f74cb02828183df7ee6b502ea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150928"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover diretrizes para máquinas virtuais

Este artigo descreve os cenários que atualmente não têm suporte e as etapas para mover as máquinas virtuais com o backup.

## <a name="scenarios-not-supported"></a>Cenários sem suporte

Os cenários a seguir ainda não têm suporte:

* Managed Disks no Zonas de Disponibilidade não podem ser movidos para uma assinatura diferente.
* Conjuntos de dimensionamento de máquinas virtuais com Load Balancer SKU padrão ou IP público SKU Standard não podem ser movidos.
* Máquinas virtuais criadas a partir dos recursos de mercado com planos ligados não pode ser movidas entre grupos de recursos ou subscrições. Desprovisionar a máquina virtual na assinatura atual e implantá-la novamente na nova assinatura.
* As máquinas virtuais em uma rede virtual existente não podem ser movidas para uma nova assinatura quando você não está movendo todos os recursos na rede virtual.
* As máquinas virtuais de baixa prioridade e os conjuntos de dimensionamento de máquinas virtuais de baixa prioridade não podem ser movidos entre grupos de recursos ou assinaturas.
* As máquinas virtuais em um conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com o backup do Azure

Para mover as máquinas virtuais configuradas com o backup do Azure, use a seguinte solução alternativa:

* Localize o local da sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se no portal do Azure, em seguida, verificação "Mostrar tipos ocultos"
* Se, no PowerShell, utilize o `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Se, na CLI, utilize o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localize o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Excluir este recurso. Esta operação exclui somente os pontos de recuperação instantâneas, não os dados de backup no cofre.
* Após a conclusão da exclusão, você pode mover o cofre e a máquina virtual para a assinatura de destino. Após a movimentação, você pode continuar os backups sem perda nos dados.
* Para obter informações sobre como mover cofres de serviço de recuperação para backup, consulte [limitações dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Passos seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).
