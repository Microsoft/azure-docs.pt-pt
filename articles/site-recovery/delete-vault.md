---
title: Eliminar um cofre de recuperação de sítios Azure
description: Saiba como eliminar um cofre de Serviços de Recuperação configurado para recuperação do site azure
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894969"
---
# <a name="delete-a-site-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação de Sites

Este artigo descreve como apagar um cofre de Serviços de Recuperação para Recuperação do Site. Para eliminar um cofre utilizado na Cópia de Segurança Azure, consulte [Eliminar um cofre de reserva em Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Antes de começar

Antes de poder eliminar um cofre, tem de remover os servidores registados e os itens no cofre. O que precisa remover depende dos cenários de replicação que implementou. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Apagar um cofre-Azure VM para Azure

1. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) para eliminar todos os VMs protegidos.
2. Então, apague o cofre.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Elimine um VMde vmware de cofre para o Azure

1. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para eliminar todos os VMs protegidos.
2. Siga [estes passos](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) para eliminar todas as políticas de replicação.
3. Elimine referências ao vCenter utilizando [estes passos](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Siga [estas instruções](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) para desativar um servidor de configuração.
5. Então, apague o cofre.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Eliminar um VM de hiper-v de abóbada (com VMM) para O Azure

1. Siga [estes passos](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) para eliminar VMs Hiper-V geridos pelo System Center VMM.
2. Desvincular e apagar todas as políticas de replicação. Faça isso no seu cofre > > Infraestrutura de **Recuperação**do Local para políticas de**replicação****vMM** > do Centro de Sistemas.
3. Siga [estes passos](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) para desmarcar um servidor VMM ligado.
4. Então, apague o cofre.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Elimine um VM hiper-V a azure

1. Siga [estes passos](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) para eliminar todos os VMs protegidos.
2. Desvincular e apagar todas as políticas de replicação. Faça isso no seu cofre > Infraestrutura > de **Recuperação**do Local para políticas de > **replicação**de**sites hiper-V**.
3. Siga [estas instruções](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) para desmarcar um hospedeiro Hyper-V.
4. Elimine o site Hyper-V.
5. Então, apague o cofre.


## <a name="use-powershell-to-force-delete-the-vault"></a>Use powerShell para forçar a eliminação do cofre 

> [!Important]
> Se estiver a testar o produto e não estiver preocupado com a perda de dados, use o método de eliminação da força para remover rapidamente o cofre e todas as suas dependências.
> O comando PowerShell elimina todo o conteúdo do cofre e não é **reversível**.

Para eliminar o cofre de recuperação do local, mesmo que existam itens protegidos, utilize estes comandos:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Saiba mais sobre [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)e [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
