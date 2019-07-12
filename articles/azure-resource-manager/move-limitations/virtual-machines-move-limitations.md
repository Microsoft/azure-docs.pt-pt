---
title: Mover máquinas virtuais do Azure para a nova subscrição ou grupo de recursos | Documentos da Microsoft
description: Utilize o Azure Resource Manager para mover máquinas virtuais para um novo grupo de recursos ou subscrição.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 095ed1c8d2328b1eb391042125526696ba8cda49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723548"
---
# <a name="move-guidance-for-virtual-machines"></a>Mover a documentação de orientação para máquinas virtuais

Este artigo descreve os cenários que não são atualmente suportados e os passos para mover máquinas virtuais com a cópia de segurança.

## <a name="scenarios-not-supported"></a>Cenários não suportados

Ainda não são suportados os seguintes cenários:

* Discos geridos em zonas de disponibilidade não podem ser movidos para uma subscrição diferente.
* Máquinas virtuais com o certificado armazenadas no Key Vault pode ser movidas para um novo grupo de recursos na mesma subscrição, mas não em várias subscrições.
* Não é possível mover a conjuntos de dimensionamento de máquina virtual com o Balanceador de carga de SKU Standard ou IP público de SKU Standard.
* Máquinas virtuais criadas a partir dos recursos de mercado com planos ligados não pode ser movidas entre grupos de recursos ou subscrições. Desaprovisionar a máquina virtual na subscrição atual e implemente novamente na subscrição nova.
* Máquinas virtuais na rede virtual existente, mas não estão a mover todos os recursos na rede virtual.

## <a name="virtual-machines-with-azure-backup"></a>Máquinas virtuais com o Azure Backup

Para mover máquinas virtuais configuradas com o Azure Backup, utilize a seguinte solução:

* Encontre a localização da sua máquina Virtual.
* Encontrar um grupo de recursos com o seguinte padrão de nomenclatura: `AzureBackupRG_<location of your VM>_1` AzureBackupRG_westus2_1 por exemplo,
* Se no portal do Azure, em seguida, verificação "Mostrar tipos ocultos"
* Se, no PowerShell, utilize o `Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1` cmdlet
* Se, na CLI, utilize o `az resource list -g AzureBackupRG_<location of your VM>_1`
* Localizar o recurso com o tipo `Microsoft.Compute/restorePointCollections` que tem o padrão de nomenclatura `AzureBackup_<name of your VM that you're trying to move>_###########`
* Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantânea, não os dados de cópia de segurança no cofre.
* Após a eliminação estiver concluída, pode mover a máquina virtual e o cofre para a subscrição de destino. Após a mudança, pode continuar a cópias de segurança sem perda de dados.
* Para obter informações sobre movimento cofres de serviços de recuperação para cópia de segurança, consulte [limitações dos serviços de recuperação](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="next-steps"></a>Passos Seguintes

Para obter comandos mover os recursos, veja [mover recursos para um novo grupo de recursos ou subscrição](../resource-group-move-resources.md).
