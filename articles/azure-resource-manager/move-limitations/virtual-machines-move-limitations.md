---
title: Mover as máquinas virtuais do Azure para uma nova assinatura ou grupo de recursos | Microsoft Docs
description: Use Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou assinatura.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 7b9cce7ac367f42329e3198c75a7640a205d01fe
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035531"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover diretrizes para máquinas virtuais

Este artigo descreve os cenários que atualmente não têm suporte e as etapas para mover as máquinas virtuais com o backup.

## <a name="scenarios-not-supported"></a>Cenários sem suporte

Os cenários a seguir ainda não têm suporte:

* Managed Disks no Zonas de Disponibilidade não podem ser movidos para uma assinatura diferente.
* Conjuntos de dimensionamento de máquinas virtuais com Load Balancer SKU padrão ou IP público SKU Standard não podem ser movidos.
* Máquinas virtuais criadas a partir dos recursos de mercado com planos ligados não pode ser movidas entre grupos de recursos ou subscrições. Desprovisionar a máquina virtual na assinatura atual e implantá-la novamente na nova assinatura.
* Máquinas virtuais em uma rede virtual existente, mas você não está movendo todos os recursos na rede virtual.
* As máquinas virtuais de baixa prioridade e os conjuntos de dimensionamento de máquinas virtuais de baixa prioridade não podem ser movidos entre grupos de recursos ou assinaturas.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com o backup do Azure

Para mover as máquinas virtuais configuradas com o backup do Azure, use a seguinte solução alternativa:

* Localize o local da sua máquina virtual.
* Localize um grupo de recursos com o seguinte padrão de `AzureBackupRG_<location of your VM>_1` nomenclatura: por exemplo, AzureBackupRG_westus2_1
* Se no portal do Azure, em seguida, verificação "Mostrar tipos ocultos"
* Se, no PowerShell, utilize o `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Se, na CLI, utilize o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localizar o recurso com o `Microsoft.Compute/restorePointCollections` tipo que tem o padrão de nomenclatura`AzureBackup_<name of your VM that you're trying to move>_###########`
* Excluir este recurso. Esta operação exclui somente os pontos de recuperação instantâneas, não os dados de backup no cofre.
* Após a conclusão da exclusão, você pode mover o cofre e a máquina virtual para a assinatura de destino. Após a movimentação, você pode continuar os backups sem perda nos dados.
* Para obter informações sobre como mover cofres de serviço de recuperação para backup, consulte [limitações dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Passos Seguintes

Para obter comandos para mover recursos, consulte [mover recursos para um novo grupo de recursos ou assinatura](../resource-group-move-resources.md).
