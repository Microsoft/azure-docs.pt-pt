---
title: Remover servidores e desabilitar a proteção | Microsoft Docs
description: Este artigo descreve como cancelar o registro de servidores de um cofre Site Recovery e desabilitar a proteção para máquinas virtuais e servidores físicos.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875803"
---
# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar proteção

Este artigo descreve como cancelar o registro de servidores de um cofre dos serviços de recuperação e como desabilitar a proteção para computadores protegidos pelo Site Recovery.


## <a name="unregister-a--configuration-server"></a>Cancelar o registro de um servidor de configuração

Se você replicar VMs VMware ou servidores físicos Windows/Linux para o Azure, poderá cancelar o registro de um servidor de configuração não conectado de um cofre da seguinte maneira:

1. [Desabilitar a proteção de máquinas virtuais](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Desassocie ou exclua políticas de](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) replicação.
3. [Excluir o servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Cancelar o registro de um servidor VMM

1. Pare de replicar máquinas virtuais em nuvens no servidor do VMM que você deseja remover.
2. Exclua todos os mapeamentos de rede usados pelas nuvens no servidor do VMM que você deseja excluir. Em **infraestrutura** > **de site Recovery para o mapeamento de rede do VMM** > do System Center, clique com o botão direito do mouse no mapeamento de rede > **excluir**.
3. Anote a ID do servidor do VMM.
4. Desassocie as políticas de replicação de nuvens no servidor do VMM que você deseja remover.  Em **infraestrutura** > de site Recovery para**políticas de replicação** **do System Center VMM** >  , clique duas vezes na política associada. Clique com o botão direito do mouse na nuvem > desassociar.
5. Exclua o servidor do VMM ou o nó ativo. Em **site Recovery infraestrutura** > para**servidores VMM** **do System Center** > VMM, clique com o botão direito do mouse no servidor > **excluir**.
6. Se o servidor do VMM estava em um estado desconectado, baixe e execute o [script de limpeza](https://aka.ms/asr-cleanup-script-vmm) no servidor do VMM. Abra o PowerShell com a opção **Executar como administrador** para alterar a política de execução para o escopo padrão (LocalMachine). No script, especifique a ID do servidor do VMM que você deseja remover. O script remove o registro e as informações de emparelhamento de nuvem do servidor.
5. Execute o script de limpeza em qualquer servidor do VMM secundário.
6. Execute o script de limpeza em qualquer outro nó de cluster do VMM passivo que tenha o provedor instalado.
7. Desinstale o provedor manualmente no servidor do VMM. Se você tiver um cluster, remova de todos os nós.
8. Se suas máquinas virtuais estivessem replicando para o Azure, você precisa desinstalar o agente de serviços de recuperação da Microsoft dos hosts Hyper-V nas nuvens excluídas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Cancelar o registro de um host Hyper-V em um site do Hyper-V

Os hosts do Hyper-V que não são gerenciados pelo VMM são coletados em um site do Hyper-V. Remova um host em um site do Hyper-V da seguinte maneira:

1. Desabilite a replicação para VMs do Hyper-V localizadas no host.
2. Desassocie políticas para o site do Hyper-V. Em **infraestrutura** > de site Recovery para**políticas de replicação**de >  **sites do Hyper-V**, clique duas vezes na política associada. Clique com o botão direito do mouse no site > desassociar.
3. Exclua os hosts Hyper-V. Em **site Recovery infraestrutura** >  > **para sites Hyper-v** **hosts Hyper-v**, clique com o botão direito do mouse no servidor > **excluir**.
4. Exclua o site do Hyper-V depois que todos os hosts tiverem sido removidos dele. Em **site Recovery infraestrutura** > **para sites**Hyper-vsitesHyper-v,cliquecomobotãodireitodomousenosite> >  **excluir**.
5. Se o host do Hyper-V estava em  um estado desconectado, execute o script a seguir em cada host do Hyper-v que você removeu. O script limpa as configurações no servidor e cancela o registro do cofre.


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


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Desabilitar a proteção para uma VM VMware ou servidor físico (VMware para Azure)

1. Em **itens** > protegidos itens replicados, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
2. Na página **desabilitar replicação** , selecione uma destas opções:
    - **Desabilitar a replicação e remover (recomendado)** – essa opção remove o item replicado de Azure site Recovery e a replicação do computador é interrompida. A configuração de replicação no servidor de configuração é limpa e Site Recovery cobrança para este servidor protegido é interrompida. Observe que essa opção só pode ser usada quando o servidor de configuração está no estado conectado.
    - **Remover** -esta opção deve ser usada somente se o ambiente de origem for excluído ou não estiver acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação no servidor de configuração **não será** limpa. 

> [!NOTE]
> Em ambas as opções, o serviço de mobilidade não será desinstalado dos servidores protegidos, você precisará desinstalá-lo manualmente. Se você planeja proteger o servidor novamente usando o mesmo servidor de configuração, você pode ignorar a desinstalação do serviço de mobilidade.

> [!NOTE]
> Se você já tiver feito failover de uma VM e ela estiver em execução no Azure, observe que a desabilitação da proteção não remove/afeta a VM com failover.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Desabilitar a proteção para uma VM do Azure (Azure para Azure)

-  Em **itens** > protegidos itens replicados, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
> [!NOTE]
> o serviço de mobilidade não será desinstalado dos servidores protegidos, você precisará desinstalá-lo manualmente. Se você planeja proteger o servidor novamente, poderá ignorar a desinstalação do serviço de mobilidade.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Desabilitar a proteção para uma máquina virtual do Hyper-V (Hyper-V para Azure)

> [!NOTE]
> Use este procedimento se você estiver replicando VMs do Hyper-V para o Azure sem um servidor do VMM. Se você estiver replicando suas máquinas virtuais usando o **System Center VMM para o cenário do Azure** , siga as instruções desabilitar a proteção para uma máquina virtual do Hyper-V replicando usando o cenário do System Center VMM para o Azure

1. Em **itens** > protegidos itens replicados, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
2. Em **desabilitar replicação**, você pode selecionar as seguintes opções:
   - **Desabilitar a replicação e remover (recomendado)** – essa opção remove o item replicado de Azure site Recovery e a replicação do computador é interrompida. A configuração de replicação na máquina virtual local será limpa e Site Recovery cobrança para esse servidor protegido será interrompida.
   - **Remover** -esta opção deve ser usada somente se o ambiente de origem for excluído ou não estiver acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. 

 > [!NOTE]
     > Se você escolher a opção **remover** , execute o seguinte conjunto de scripts para limpar as configurações de replicação do servidor Hyper-V local.

> [!NOTE]
> Se você já tiver feito failover de uma VM e ela estiver em execução no Azure, observe que a desabilitação da proteção não remove/afeta a VM com failover.

1. No servidor de host do Hyper-V de origem, para remover a replicação da máquina virtual. Substitua SQLVM1 pelo nome da sua máquina virtual e execute o script de um PowerShell administrativo

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Desabilitar a proteção para uma máquina virtual do Hyper-V replicando para o Azure usando o cenário do System Center VMM para o Azure

1. Em **itens** > protegidos itens replicados, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
2. Em **desabilitar replicação**, selecione uma destas opções:

   - **Desabilitar a replicação e remover (recomendado)** – essa opção remove o item replicado de Azure site Recovery e a replicação do computador é interrompida. A configuração de replicação na máquina virtual local é limpa e Site Recovery cobrança para esse servidor protegido é interrompida.
   - **Remover** -esta opção deve ser usada somente se o ambiente de origem for excluído ou não estiver acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. 

     > [!NOTE]
     > Se você escolheu a opção **remover** , execute os scripts a seguir para limpar as configurações de replicação do servidor VMM local.
3. Execute esse script no servidor VMM de origem, usando o PowerShell (privilégios de administrador necessários) no console do VMM. Substitua o espaço reservado **SQLVM1** pelo nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. As etapas acima limpam as configurações de replicação no servidor do VMM. Para interromper a replicação da máquina virtual em execução no servidor de host do Hyper-V, execute este script. Substitua SQLVM1 pelo nome da sua máquina virtual e host01.contoso.com pelo nome do servidor de host Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Desabilitar a proteção para uma máquina virtual Hyper-V replicando para o servidor VMM secundário usando o cenário VMM do System Center para VMM

1. Em **itens** > protegidos itens replicados, clique com o botão direito do mouse no computador > **desabilitar a replicação**.
2. Em **desabilitar replicação**, selecione uma destas opções:

   - **Desabilitar a replicação e remover (recomendado)** – essa opção remove o item replicado de Azure site Recovery e a replicação do computador é interrompida. A configuração de replicação na máquina virtual local é limpa e Site Recovery cobrança para esse servidor protegido é interrompida.
   - **Remover** -esta opção deve ser usada somente se o ambiente de origem for excluído ou não estiver acessível (não conectado). Isso remove o item replicado do Azure Site Recovery (a cobrança é interrompida). A configuração de replicação na máquina virtual local **não será** limpa. Execute o seguinte conjunto de scripts para limpar as configurações de replicação das máquinas virtuais locais.
     > [!NOTE]
     > Se você escolheu a opção **remover** , execute os scripts a seguir para limpar as configurações de replicação do servidor VMM local.

3. Execute esse script no servidor VMM de origem, usando o PowerShell (privilégios de administrador necessários) no console do VMM. Substitua o espaço reservado **SQLVM1** pelo nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. No servidor do VMM secundário, execute este script para limpar as configurações da máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. No servidor do VMM secundário, atualize as máquinas virtuais no servidor de host do Hyper-V para que a VM secundária seja detectada novamente no console do VMM.
6. As etapas acima limpam as configurações de replicação no servidor do VMM. Se você quiser interromper a replicação para a máquina virtual, execute o seguinte script de Oh as VMs primárias e secundárias. Substitua SQLVM1 pelo nome da sua máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”




