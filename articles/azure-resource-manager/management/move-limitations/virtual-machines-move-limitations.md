---
title: Mover VMs Azure para novo grupo de subscrição ou recursos
description: Utilize o Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: e5bd004b6619db9c9882b8e9e6005309317b8ca5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744643"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover orientação para máquinas virtuais

Este artigo descreve os cenários que não são suportados atualmente e os passos para mover máquinas virtuais com cópia de segurança.

## <a name="scenarios-not-supported"></a>Cenários não suportados

Os seguintes cenários ainda não são apoiados:

* Os Discos Geridos em Zonas de Disponibilidade não podem ser transferidos para uma subscrição diferente.
* Os conjuntos de balança de máquinas virtuais com o balanceador de carga SKU padrão ou o IP público standard SKU não podem ser movidos.
* Máquinas virtuais criadas a partir de recursos do Marketplace com planos anexados não podem ser movidas através de grupos de recursos ou subscrições. Desavisar a máquina virtual na subscrição atual, e implementar novamente na nova subscrição.
* As máquinas virtuais de uma rede virtual existente não podem ser transferidas para uma nova subscrição quando não está a mover todos os recursos da rede virtual.
* Máquinas virtuais de baixa prioridade e conjuntos de escala de máquina virtual de baixa prioridade não podem ser movidos através de grupos de recursos ou subscrições.
* As máquinas virtuais num conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com backup Azure

Para mover máquinas virtuais configuradas com Azure Backup, deve eliminar os pontos de restauro do cofre.

Se [a eliminação suave](../../../backup/backup-azure-security-feature-cloud.md) estiver ativada para a sua máquina virtual, não pode mover a máquina virtual enquanto estes pontos de restauro são mantidos. [Desative a eliminação suave](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) ou aguarde 14 dias após a eliminação dos pontos de restauro.

### <a name="portal"></a>Portal

1. Pare temporariamente a cópia de segurança e retenha os dados de backup.
2. Para mover máquinas virtuais configuradas com backup Azure, faça os seguintes passos:

   1. Encontre a localização da sua máquina virtual.
   2. Encontre um grupo de recursos com o seguinte padrão de nomeação: `AzureBackupRG_<location of your VM>_1` . Por exemplo, *AzureBackupRG_westus2_1*
   3. No portal Azure, verifique **os tipos ocultos do Show**.
   4. Encontre o recurso com o tipo **Microsoft.Compute/restorePointCollections** que tenha o padrão de nomeação `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.
   6. Depois de concluída a operação de eliminação, pode mover a sua máquina virtual.

3. Mova o VM para o grupo de recursos-alvo.
4. Retoma o apoio.

### <a name="powershell"></a>PowerShell

* Encontre a localização da sua Máquina Virtual.
* Encontre um grupo de recursos com o seguinte padrão de nomeação: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se em PowerShell, use o `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Encontre o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomeação`AzureBackup_<name of your VM that you're trying to move>_###########`
* Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.

### <a name="azure-cli"></a>CLI do Azure

* Encontre a localização da sua Máquina Virtual.
* Encontre um grupo de recursos com o seguinte padrão de nomeação: `AzureBackupRG_<location of your VM>_1` por exemplo, AzureBackupRG_westus2_1
* Se em CLI, usar o`az resource list -g AzureBackupRG_<location of your VM>_1`
* Encontre o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomeação`AzureBackup_<name of your VM that you're trying to move>_###########`
* Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.

## <a name="next-steps"></a>Passos seguintes

* Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).

* Para obter informações sobre a movimentação de cofres do Serviço de Recuperação para cópia de segurança, consulte [as limitações dos Serviços de Recuperação](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
