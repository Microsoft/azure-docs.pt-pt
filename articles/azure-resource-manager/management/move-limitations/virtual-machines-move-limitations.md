---
title: Move Os VMs Azure para um novo grupo de subscrição ou recursos
description: Utilize o Gestor de Recursos Azure para mover máquinas virtuais para um novo grupo de recursos ou subscrição.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 97c49f90dab2aafd89de322e57ad44ff1fc9d367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479764"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover orientação para máquinas virtuais

Este artigo descreve os cenários que não são suportados atualmente e os passos para mover máquinas virtuais com backup.

## <a name="scenarios-not-supported"></a>Cenários não suportados

Os seguintes cenários ainda não são suportados:

* Discos geridos em Zonas de Disponibilidade não podem ser movidos para uma subscrição diferente.
* Conjuntos de escala de máquina virtual com balanceor de carga Padrão SKU ou IP público Padrão SKU não podem ser movidos.
* As máquinas virtuais criadas a partir de recursos do Marketplace com planos anexados não podem ser movidas através de grupos de recursos ou subscrições. Desfore a máquina virtual na subscrição atual e volte a implantar-se na nova subscrição.
* As máquinas virtuais de uma rede virtual existente não podem ser transferidas para uma nova subscrição quando não está a mover todos os recursos da rede virtual.
* Máquinas virtuais de baixa prioridade e conjuntos de escala de máquinas virtuais de baixa prioridade não podem ser movidos através de grupos de recursos ou subscrições.
* As máquinas virtuais num conjunto de disponibilidade não podem ser movidas individualmente.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com Backup Azure

Para mover máquinas virtuais configuradas com Cópia de Segurança Azure, utilize a seguinte suver:

* Encontre a localização da sua Máquina Virtual.
* Encontre um grupo de recursos `AzureBackupRG_<location of your VM>_1` com o seguinte padrão de nomeação: por exemplo, AzureBackupRG_westus2_1
* Se no portal Azure, verifique "Mostrar tipos ocultos"
* Se estiver no PowerShell, utilize o `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Se no CLI, use o`az resource list -g AzureBackupRG_<location of your VM>_1`
* Encontre o recurso `Microsoft.Compute/restorePointCollections` com tipo que tem o padrão de nomeação`AzureBackup_<name of your VM that you're trying to move>_###########`
* Apague este recurso. Esta operação elimina apenas os pontos de recuperação instantâneas, não os dados de back-up no cofre.
* Depois de ser apagado, pode mover o cofre e a máquina virtual para a subscrição do alvo. Após a mudança, pode continuar as cópias de segurança sem perda de dados.
* Para obter informações sobre a deslocação de cofres do Serviço de Recuperação para backup, consulte [as limitações dos Serviços](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)de Recuperação .

## <a name="next-steps"></a>Passos seguintes

Para que os comandos movam recursos, consulte [mover recursos para um novo grupo de recursos ou subscrição](../move-resource-group-and-subscription.md).
