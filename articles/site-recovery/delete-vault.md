---
title: Eliminar um cofre de serviços de recuperação configurado para o serviço Azure Site Recovery
description: Saiba como eliminar um cofre de serviços de recuperação configurado para o Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: b5d035308c50525449edf47131c4a6a8c62b750b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045529"
---
# <a name="delete-a-site-recovery-services-vault"></a>Eliminar um cofre dos serviços de recuperação de Site

As dependências que podem impedir que a eliminar um cofre do Azure Site Recovery. As ações que necessárias variam consoante o cenário de recuperação de sites. Para eliminar um cofre utilizado na cópia de segurança do Azure, veja [eliminar um cofre de cópia de segurança no Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Eliminar um cofre de recuperação de sites 
Para eliminar o cofre, siga os passos recomendados para o seu cenário.

### <a name="vmware-vms-to-azure"></a>VMs do VMware para o Azure

1. Elimine todas as VMs protegidas ao seguir os passos em [desative a proteção de um VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Eliminar todas as políticas de replicação ao seguir os passos em [eliminar uma política de replicação](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Eliminar referências ao vCenter ao seguir os passos em [eliminar um servidor vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Eliminar o servidor de configuração ao seguir os passos em [descontinuar um servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Elimine o cofre.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>VMs do Hyper-V (com VMM) para o Azure
1. Elimine todas as VMs protegidas ao seguir os passos em[desative a proteção para uma VM de Hyper-V (com VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Desassociação e eliminar todas as políticas de replicação ao navegar para o Cofre -> **infraestrutura do Site Recovery** -> **para o System Center VMM** -> **replicação Políticas**

3.  Eliminar referências aos servidores do VMM ao seguir os passos em [anular o registo de um ligado ao servidor do VMM](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Elimine o cofre.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>VMs de Hyper-V (sem o Virtual Machine Manager) para o Azure
1. Elimine todas as VMs protegidas ao seguir os passos em [desative a proteção para uma máquina virtual de Hyper-V (Hyper-V para o Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Desassociação e eliminar todas as políticas de replicação ao navegar para o Cofre -> **infraestrutura do Site Recovery** -> **para Sites de Hyper-V** -> **políticas de replicação**

3. Eliminar referências para servidores Hyper-V ao seguir os passos em [anular o registo de um anfitrião Hyper-V](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Elimine o site de Hyper-V.

5. Elimine o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Utilize o PowerShell para forçar a eliminação do Cofre 

> [!Important]
> Se estiver a testar o produto e não estão preocupados com perda de dados, utilize o método delete de imposição para remove rapidamente o Cofre e todas as respetivas dependências.
> O comando do PowerShell elimina todos os conteúdos do cofre e é **não reversível**.

Para eliminar o Cofre de recuperação de sites, mesmo se existirem itens protegidos, utilize estes comandos:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Saiba mais sobre [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault), e [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
