---
title: Remover servidores e desativar proteção | Documentos da Microsoft
description: Este artigo descreve como anular o registo de servidores a partir de um cofre de recuperação de Site e para desativar a proteção para máquinas virtuais e servidores físicos.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 20d5c4628d729b8dff8b1d72f80beac0ec2e8f67
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569753"
---
# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar proteção

Este artigo descreve como anular o registo de servidores a partir de um cofre dos serviços de recuperação e como desativar a proteção para máquinas protegidas pelo Site Recovery.


## <a name="unregister-a--configuration-server"></a>Anular o registo de um servidor de configuração

Se replicar VMs de VMware ou servidores físicos Windows/Linux para o Azure, pode cancelar o registro de um servidor de configuração de solução desligada a partir de um cofre da seguinte forma:

1. [Desative a proteção de máquinas virtuais](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Desassociação ou eliminar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) políticas de replicação.
3. [Eliminar o servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Anular o registo de um servidor do VMM

1. Pare a replicação de máquinas virtuais em nuvens no servidor do VMM que pretende remover.
2. Elimine quaisquer mapeamentos de rede utilizados pelo nuvens no servidor VMM que pretende eliminar. No **infraestrutura do Site Recovery** > **para o System Center VMM** > **mapeamento da rede**, faça duplo clique o mapeamento de rede > **Eliminar**.
3. Tenha em atenção o ID do servidor VMM.
4. Desassociação políticas de replicação de nuvens no servidor do VMM que pretende remover.  Na **infraestrutura do Site Recovery** > **para o System Center VMM** >  **políticas de replicação**, faça duplo clique a política associada. A cloud com o botão direito > **Disassociate**.
5. Elimine o servidor do VMM ou o nó ativo. Na **infraestrutura do Site Recovery** > **para o System Center VMM** > **servidores VMM**, com o botão direito do servidor > **eliminar** .
6. Se o servidor do VMM estava num estado desligado, em seguida, transfira e execute o [script de limpeza](https://aka.ms/asr-cleanup-script-vmm) no servidor do VMM. Abra o PowerShell com o **executar como administrador** opção, para alterar a diretiva de execução para o âmbito de predefinição (LocalMachine). No script, especifique o ID do servidor VMM que pretende remover. O script remove o registo e informações do servidor de emparelhamento na cloud.
5. Execute o script de limpeza em qualquer servidor VMM secundário.
6. Execute o script de limpeza em qualquer outros passivos VMM nós de cluster que tenham o fornecedor instalado.
7. Desinstale o fornecedor manualmente no servidor do VMM. Se tiver um cluster, remova de todos os nós.
8. Se as máquinas virtuais foram a replicar para o Azure, terá de desinstalar o agente dos serviços de recuperação do Microsoft de anfitriões de Hyper-V nas nuvens eliminadas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Anular o registo de um anfitrião de Hyper-V num Site de Hyper-V

Anfitriões de Hyper-V que não são geridos pelo VMM são reunidos num site de Hyper-V. Remova um anfitrião num site Hyper-V da seguinte forma:

1. Desative a replicação das VMs de Hyper-V localizada no anfitrião.
2. Desassociação políticas para o site Hyper-V. Na **infraestrutura do Site Recovery** > **para Sites de Hyper-V** >  **políticas de replicação**, faça duplo clique a política associada. Com o botão direito do site > **Disassociate**.
3. Elimine anfitriões Hyper-V. Na **infraestrutura do Site Recovery** > **para Sites de Hyper-V** > **anfitriões Hyper-V**, com o botão direito do servidor > **eliminar** .
4. Elimine o site de Hyper-V depois de todos os anfitriões foram removidos do mesmo. Na **infraestrutura do Site Recovery** > **para Sites de Hyper-V** > **Sites Hyper-V**, com o botão direito do site > **eliminar** .
5. Se o anfitrião Hyper-V estava num **desligado** de estado, em seguida, execute o seguinte script em cada anfitrião de Hyper-V que removeu. O script limpa as definições no servidor e anula o registo do cofre.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Desative a proteção para uma VM de VMware ou servidor físico (VMware para o Azure)

1. Na **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. Na **desative a replicação** , selecione uma destas opções:
    - **Desativar a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação no servidor de configuração é limpo e faturação do Site Recovery para este servidor protegido é interrompida. Tenha em atenção que esta opção só pode ser utilizada quando o servidor de configuração está no estado ligado.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem for eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (a faturação é interrompida). Configuração de replicação no servidor de configuração **não irá** ser limpos. 

> [!NOTE]
> Em ambas as opções das serviço de mobilidade não será desinstalado dos servidores protegidos, terá de desinstalá-lo manualmente. Se pretender proteger o servidor novamente usando o mesmo servidor de configuração, pode ignorar a desinstalar o serviço de mobilidade.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Desative a proteção para uma máquina virtual de Hyper-V (Hyper-V para o Azure)

> [!NOTE]
> Utilize este procedimento se estiver a replicar VMs Hyper-V para o Azure sem um servidor do VMM. Se estiver a replicar suas máquinas virtuais utilizando o **System Center VMM para o Azure** cenário, em seguida, siga a instruções desative a proteção Hyper-V virtual da máquina a replicar com o System Center VMM para o cenário do Azure

1. Na **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. Na **desative a replicação**, pode selecionar as seguintes opções:
    - **Desativar a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação na máquina virtual no local serão limpos e faturação do Site Recovery para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem for eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (a faturação é interrompida). Configuração de replicação na máquina virtual no local **não irá** ser limpos. 

    > [!NOTE]
    > Se tiver escolhido o **remover** opção, em seguida, execute o seguinte conjunto de scripts para limpar as definições de replicação no local servidor Hyper-V.
1. No servidor de origem Hyper-V anfitrião, para remover a replicação para a máquina virtual. Substitua SQLVM1 com o nome da sua máquina virtual e execute o script a partir de um PowerShell administrativo

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Desative a proteção para uma máquina virtual de Hyper-V a replicar para o Azure com o System Center VMM para o cenário do Azure

1. Na **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. Na **desative a replicação**, selecione uma destas opções:

    - **Desativar a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação na máquina virtual no local é limpo e faturação do Site Recovery para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem for eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (a faturação é interrompida). Configuração de replicação na máquina virtual no local **não irá** ser limpos. 

    > [!NOTE]
    > Se tiver escolhido o **remover** opção, em seguida, tun os seguintes scripts para limpar as definições de replicação no VMM Server local.
3. Execute este script no servidor do VMM de origem, com o PowerShell (privilégios de administrador necessários) a partir da consola do VMM. Substitua o marcador de posição **SQLVM1** com o nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Os passos acima limpar as definições de replicação no servidor do VMM. Para parar a replicação para a máquina virtual em execução no servidor de anfitrião do Hyper-V, execute este script. Substitua SQLVM1 com o nome da máquina virtual e host01.contoso.com com o nome do servidor de anfitrião Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Desative a proteção para uma máquina virtual de Hyper-V a replicar para o servidor de VMM secundário com o System Center VMM para o cenário VMM

1. Na **itens protegidos** > **itens replicados**, clique com o botão direito a máquina > **desative a replicação**.
2. Na **desative a replicação**, selecione uma destas opções:

    - **Desativar a replicação e remover (recomendado)** - esta opção Remover o item replicado do Azure Site Recovery e a replicação para a máquina está parada. Configuração de replicação na máquina virtual no local é limpo e faturação do Site Recovery para este servidor protegido é interrompida.
    - **Remover** -esta opção deve ser utilizada apenas se o ambiente de origem for eliminada ou não está acessível (não ligado). Esta ação remove o item replicado do Azure Site Recovery (a faturação é interrompida). Configuração de replicação na máquina virtual no local **não irá** ser limpos. Execute o seguinte conjunto de scripts para limpar as máquinas de virtuais de locais de definições de replicação.
> [!NOTE]
> Se tiver escolhido o **remover** opção, em seguida, tun os seguintes scripts para limpar as definições de replicação no VMM Server local.

3. Execute este script no servidor do VMM de origem, com o PowerShell (privilégios de administrador necessários) a partir da consola do VMM. Substitua o marcador de posição **SQLVM1** com o nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. No servidor secundário do VMM, execute este script para limpar as definições para a máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. No servidor secundário do VMM, Atualize as máquinas virtuais no servidor de anfitrião de Hyper-V, para que a VM secundária obtém detetada novamente na consola do VMM.
6. Os passos acima limpar as definições de replicação no servidor do VMM. Se pretender parar a replicação para a máquina virtual, execute o seguinte script AH a VM principal e secundária. Substitua SQLVM1 com o nome da sua máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”




