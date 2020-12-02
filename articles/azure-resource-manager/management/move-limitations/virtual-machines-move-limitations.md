---
title: Mover VMs Azure para novo grupo de subscrição ou recursos
description: Utilize o Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: b1032b5a632bcac82cb9ae1f1b3df7b49f5463f5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456323"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover orientação para máquinas virtuais

Este artigo descreve os cenários que não são suportados atualmente e os passos para mover máquinas virtuais com cópia de segurança.

## <a name="scenarios-not-supported"></a>Cenários não suportados

Os seguintes cenários ainda não são apoiados:

* Os conjuntos de balança de máquinas virtuais com o balanceador de carga SKU padrão ou o IP público standard SKU não podem ser movidos.
* As máquinas virtuais de uma rede virtual existente não podem ser transferidas para uma nova subscrição quando não está a mover todos os recursos da rede virtual.
* Máquinas virtuais criadas a partir de recursos do Marketplace com planos anexados não podem ser movidas através de subscrições. Para uma possível solução alternativa, consulte [máquinas virtuais com planos marketplace.](#virtual-machines-with-marketplace-plans)
* Máquinas virtuais de baixa prioridade e conjuntos de escala de máquina virtual de baixa prioridade não podem ser movidos através de grupos de recursos ou subscrições.
* As máquinas virtuais num conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="azure-disk-encryption"></a>Encriptação do disco Azure

Não é possível mover uma máquina virtual que esteja integrada com um cofre-chave para implementar [encriptação de disco Azure para VMs Linux](../../../virtual-machines/linux/disk-encryption-overview.md) ou [Encriptação de Disco Azure para VMs do Windows](../../../virtual-machines/windows/disk-encryption-overview.md). Para mover o VM, tem de desativar a encriptação.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-marketplace-plans"></a>Máquinas virtuais com planos de Marketplace

Máquinas virtuais criadas a partir de recursos do Marketplace com planos anexados não podem ser movidas através de subscrições. Para contornar esta limitação, pode desavisionar a máquina virtual na subscrição atual e implantá-la novamente na nova subscrição. Os seguintes passos ajudam-no a recriar a máquina virtual na nova subscrição. No entanto, podem não funcionar para todos os cenários. Se o plano já não estiver disponível no Mercado, estes passos não funcionarão.

1. Copiar informações sobre o plano.

1. Ou clona o disco DE para a subscrição de destino ou move o disco original depois de eliminar a máquina virtual da subscrição de origem.

1. Na subscrição de destino, aceite os termos do Marketplace para o seu plano. Pode aceitar os termos executando o seguinte comando PowerShell:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   Ou, pode criar uma nova instância de uma máquina virtual com o plano através do portal. Pode eliminar a máquina virtual depois de aceitar os termos na nova subscrição.

1. Na subscrição de destino, recrie a máquina virtual a partir do disco de OS clonado utilizando o PowerShell, CLI ou um modelo de Gestor de Recursos Azure. Inclua o plano de mercado que está ligado ao disco. As informações sobre o plano devem coincidir com o plano que adquiriu na nova subscrição.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com backup Azure

Para mover máquinas virtuais configuradas com Azure Backup, deve eliminar os pontos de restauro do cofre.

Se [a eliminação suave](../../../backup/backup-azure-security-feature-cloud.md) estiver ativada para a sua máquina virtual, não pode mover a máquina virtual enquanto estes pontos de restauro são mantidos. [Desative a eliminação suave](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) ou aguarde 14 dias após a eliminação dos pontos de restauro.

### <a name="portal"></a>Portal

1. Pare temporariamente a cópia de segurança e guarde os dados de reserva.
2. Para mover máquinas virtuais configuradas com backup Azure, faça os seguintes passos:

   1. Encontre a localização da sua máquina virtual.
   2. Encontre um grupo de recursos com o seguinte padrão de nomeação: `AzureBackupRG_<VM location>_1` . Por exemplo, o nome está no formato de *AzureBackupRG_westus2_1*.
   3. No portal Azure, verifique **os tipos ocultos do Show**.
   4. Encontre o recurso com o tipo **Microsoft.Compute/restorePointCollections** que tenha o padrão de nomeação `AzureBackup_<VM name>_###########` .
   5. Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.
   6. Depois de concluída a operação de eliminação, pode mover a sua máquina virtual.

3. Mova o VM para o grupo de recursos-alvo.
4. Retoma o apoio.

### <a name="powershell"></a>PowerShell

1. Encontre a localização da sua máquina virtual.

1. Encontre um grupo de recursos com o padrão de nomeação - `AzureBackupRG_<VM location>_1` . Por exemplo, o nome pode ser `AzureBackupRG_westus2_1` .

1. Se estiver a mover apenas uma máquina virtual, obtenha a recolha de pontos de restauro para aquela máquina virtual.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Se estiver a mover todas as máquinas virtuais com back-ups neste local, obtenha as coleções de pontos de restauro para essas máquinas virtuais.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Elimine cada recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>CLI do Azure

1. Encontre a localização da sua máquina virtual.

1. Encontre um grupo de recursos com o padrão de nomeação - `AzureBackupRG_<VM location>_1` . Por exemplo, o nome pode ser `AzureBackupRG_westus2_1` .

1. Se estiver a mover apenas uma máquina virtual, obtenha a recolha de pontos de restauro para aquela máquina virtual.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Se estiver a mover todas as máquinas virtuais com back-ups neste local, obtenha as coleções de pontos de restauro para essas máquinas virtuais.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Elimine cada recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Passos seguintes

* Para que os comandos movimentem recursos, consulte [mover recursos para novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).

* Para obter informações sobre a movimentação de cofres do Serviço de Recuperação para cópia de segurança, consulte [as limitações dos Serviços de Recuperação](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
