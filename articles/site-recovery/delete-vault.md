---
title: Excluir um cofre dos serviços de recuperação configurado para o serviço de Azure Site Recovery
description: Saiba como excluir um cofre dos serviços de recuperação configurado para Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876031"
---
# <a name="delete-a-site-recovery-services-vault"></a>Excluir um cofre de serviços de Site Recovery

As dependências podem impedir que você exclua um cofre de Azure Site Recovery. As ações que você precisa executar variam de acordo com o cenário de Site Recovery. Para excluir um cofre usado no backup do Azure, consulte [excluir um cofre de backup no Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Excluir um cofre de Site Recovery 
Para excluir o cofre, siga as etapas recomendadas para seu cenário.
### <a name="azure-vms-to-azure"></a>VMs do Azure para o Azure

1. Exclua todas as VMs protegidas seguindo as etapas em [desabilitar a proteção para um VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure).
2. Exclua o cofre.

### <a name="vmware-vms-to-azure"></a>VMs do VMware para o Azure

1. Exclua todas as VMs protegidas seguindo as etapas em [desabilitar a proteção para um VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Exclua todas as políticas de replicação seguindo as etapas em [excluir uma política de replicação](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Exclua as referências ao vCenter seguindo as etapas em [excluir um servidor vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Exclua o servidor de configuração seguindo as etapas em [desativar um servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Exclua o cofre.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>VMs do Hyper-V (com VMM) para o Azure
1. Exclua todas as VMs protegidas seguindo as etapas em[desabilitar a proteção para uma VM do Hyper-V (com o VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Desassociar & excluir todas as políticas de replicação navegando até seu cofre-> **site Recovery infraestrutura** > para**políticas de replicação** **do System Center VMM** -> 

3.  Exclua as referências aos servidores VMM seguindo as etapas em [cancelar o registro de um servidor VMM conectado](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Exclua o cofre.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>VMs do Hyper-V (sem Virtual Machine Manager) para o Azure
1. Exclua todas as VMs protegidas seguindo as etapas em [desabilitar a proteção para uma máquina virtual do Hyper-v (Hyper-v para o Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Desassociar & excluir todas as políticas de -> replicação navegando até seu cofre-> **site Recovery infraestrutura** > **para sites do Hyper-V políticas de** **replicação**

3. Exclua as referências a servidores Hyper-V seguindo as etapas em [cancelar o registro de um host Hyper-v](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Exclua o site do Hyper-V.

5. Exclua o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Usar o PowerShell para forçar a exclusão do cofre 

> [!Important]
> Se você estiver testando o produto e não estiver preocupado com a perda de dados, use o método de exclusão forçada para remover rapidamente o cofre e todas as suas dependências.
> O comando do PowerShell exclui todo o conteúdo do cofre e **não**é reversível.

Para excluir o cofre Site Recovery mesmo se houver itens protegidos, use estes comandos:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Saiba mais sobre [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
