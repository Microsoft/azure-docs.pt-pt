---
title: Apagar um cofre de recuperação do local de Azure
description: Saiba como apagar um cofre dos Serviços de Recuperação configurado para a recuperação do local de Azure
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426389"
---
# <a name="delete-a-site-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação de Sites

Este artigo descreve como apagar um cofre dos Serviços de Recuperação para recuperação do local. Para eliminar um cofre utilizado na Cópia de Segurança Azure, consulte [eliminar um cofre de reserva em Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Antes de começar

Antes de apagar um cofre, tem de remover servidores registados e itens no cofre. O que precisa remover depende dos cenários de replicação que implementou. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Apagar um abóbada-Azure VM para Azure

1. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) para eliminar todos os VMs protegidos.
2. Então, apague o cofre.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Apagar um VM de abóbada-VMware para Azure

1. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para eliminar todos os VMs protegidos.
2. Siga [estes passos](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) para eliminar todas as políticas de replicação.
3. Elimine as referências ao vCenter utilizando [estes passos](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Siga estas instruções para [desativar](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) um servidor de configuração.
5. Então, apague o cofre.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Excluir um abóbada-Hiper-V VM (com VMM) para Azure

1. Siga [estes passos](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) para eliminar os VMs Hiper-V geridos pelo System Center VMM.
2. Desassociem e apaguem todas as políticas de replicação. Faça isto no seu cofre > **Infraestrutura de recuperação do local**  >  para políticas de replicação do Centro de **Sistemas VMM**  >  .
3. Siga [estes passos](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) para não registar um servidor VMM ligado.
4. Então, apague o cofre.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Apagar um cofre-Hiper-V VM para Azure

1. Siga [estes passos](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) para eliminar todos os VM protegidos.
2. Desassociem e apaguem todas as políticas de replicação. Faça isso no seu cofre > **infraestrutura de recuperação do local**  >  para políticas de replicação de sites **hiper-V**  >  .
3. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) para não registar um hospedeiro Hiper-V.
4. Apague o site Hyper-V.
5. Então, apague o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Use PowerShell para forçar a apagar o cofre 

> [!Important]
> Se estiver a testar o produto e não estiver preocupado com a perda de dados, utilize o método de eliminação de força para remover rapidamente o cofre e todas as suas dependências.
> O comando PowerShell elimina todo o conteúdo do cofre e não é **reversível**.

Para eliminar o cofre de recuperação do local mesmo que existam itens protegidos, utilize estes comandos:

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

Saiba mais sobre [o Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
