---
title: Remova os servidores e desative a proteção  Microsoft Docs
description: Este artigo descreve como desregistar servidores a partir de um cofre de recuperação de site, e desativar a proteção para máquinas virtuais e servidores físicos.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362853"
---
# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar proteção

Este artigo descreve como desregistar servidores a partir de um cofre dos Serviços de Recuperação e como desativar a proteção para máquinas protegidas pela Recuperação do Site.


## <a name="unregister-a--configuration-server"></a>Desmarque um servidor de configuração

Se replicar VMs VMware ou servidores físicos Windows/Linux para o Azure, pode desregistar um servidor de configuração não ligado a partir de um cofre da seguinte forma:

1. [Desativar a proteção das máquinas virtuais](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Desvincular ou excluir](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) políticas de replicação.
3. [Eliminar o servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Desregistre um servidor VMM

1. Pare de replicar máquinas virtuais em nuvens no servidor VMM que pretende remover.
2. Elimine quaisquer mapeamentos de rede utilizados por nuvens no servidor VMM que pretende eliminar. Na **infraestrutura** de recuperação do site > **para o Centro de Sistema VMM** > Mapeamento de **rede,** clique à direita no mapeamento da rede > **Delete**.
3. Observe a identificação do servidor VMM.
4. Desassocie as políticas de replicação a partir de nuvens no servidor VMM que pretende remover.  Na **Infraestrutura** de recuperação do site > Para o Centro de **SistemaS VMM** >  Políticas de **Replicação,** clique duas vezes na política associada. Clique à direita na nuvem > **Disassociate**.
5. Elimine o servidor VMM ou o nó ativo. Na **Infraestrutura** de recuperação do site > Para o **Centro de SistemaVMM** > **Servidores VMM,** clique à direita no servidor > **Delete**.
6. Se o seu servidor VMM estava em estado desligado, então faça o download e [execute](https://aka.ms/asr-cleanup-script-vmm) o script de limpeza no servidor VMM. Abra a PowerShell com a opção **Executar como Administrador,** para alterar a política de execução para o âmbito padrão (LocalMachine). No script, especifique a identificação do servidor VMM que pretende remover. O script remove informações de registo e emparelhamento em nuvem do servidor.
5. Execute o script de limpeza em qualquer servidor Secundário de VMM.
6. Execute o script de limpeza em quaisquer outros nós de cluster VMM passivos que tenham o Fornecedor instalado.
7. Desinstale manualmente o Fornecedor no servidor VMM. Se tiver um aglomerado, retire todos os nós.
8. Se as suas máquinas virtuais se replicavam para o Azure, teria de desinstalar o agente dos Serviços de Recuperação da Microsoft dos anfitriões Hyper-V nas nuvens apagadas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Desregistre um hospedeiro Hyper-V num site hiper-V

Os anfitriões hiper-V que não são geridos pela VMM estão reunidos num site hyper-V. Remova um hospedeiro num site hyper-V da seguinte forma:

1. Desative a replicação para VMs hiper-V localizados no hospedeiro.
2. Desvincular as políticas para o site Hyper-V. Na **infraestrutura** de recuperação do site > **para sites hiper-V** >  Políticas de **replicação,** clique duas vezes na política associada. Clique à direita no site > **Disassociate**.
3. Elimine os anfitriões hyper-V. Na **infraestrutura** de recuperação do site > **para sites hiper-V** > **anfitriões hiper-V,** clique à direita no servidor > **Delete**.
4. Elimine o site Hyper-V depois de todos os anfitriões terem sido removidos do mesmo. Na **infraestrutura** de recuperação do site > **para sites hiper-V** > **sites hiper-V,** clique à direita no site > **Delete**.
5. Se o seu anfitrião Hyper-V estava em estado **desligado,** então execute o seguinte script em cada hospedeiro Hyper-V que removeu. O script limpa as definições no servidor e desregista-as a partir do cofre.


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


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Desative a proteção para um VMware VM ou servidor físico (VMware para Azure)

1. Em **itens protegidos** > **itens replicados,** clique à direita na máquina > **Desative**a replicação .
2. Na página **de replicação de Desativar,** selecione uma destas opções:
    - **Desative a replicação e remova (recomendado)** - Esta opção remova o item replicado da Recuperação do Local Azure e a replicação para a máquina é interrompida. A configuração de replicação no Servidor de Configuração é limpa e a faturação de recuperação do site para este servidor protegido é interrompida. Note que esta opção só pode ser utilizada quando o Servidor de Configuração estiver em estado de ligação.
    - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for apagado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Sítio Azure (a faturação está parada). A configuração de replicação no Servidor de Configuração **não** será limpa. 

> [!NOTE]
> Em ambas as opções o serviço de mobilidade não será desinstalado a partir dos servidores protegidos, é necessário desinstalá-lo manualmente. Se planeia proteger o servidor novamente utilizando o mesmo servidor de Configuração, pode saltar desinstalar o serviço de mobilidade.

> [!NOTE]
> Se já falhou em relação a um VM e está a funcionar em Azure, note que a proteção contra desativação não remove/afeta o falhado sobre o VM.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Desativar a proteção para um VM Azure (Azure to Azure)

-  Em **itens protegidos** > **itens replicados,** clique à direita na máquina > **Desative**a replicação .
> [!NOTE]
> o serviço de mobilidade não será desinstalado a partir dos servidores protegidos, é necessário desinstalá-lo manualmente. Se planeia proteger novamente o servidor, pode não instalar o serviço de mobilidade.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Desativar a proteção para uma máquina virtual Hyper-V (Hyper-V a Azure)

> [!NOTE]
> Utilize este procedimento se estiver a replicar VMs Hiper-V para O Azure sem um servidor VMM. Se estiver a replicar as suas máquinas virtuais utilizando o cenário **VMM do System Center VMM para Azure,** siga as instruções Desativar a proteção para uma máquina virtual Hyper-V replicando utilizando o cenário VMM do Centro de Sistema para Azure

1. Em **itens protegidos** > **itens replicados,** clique à direita na máquina > **Desative**a replicação .
2. Na **replicação de Desativação,** pode selecionar as seguintes opções:
   - **Desative a replicação e remova (recomendado)** - Esta opção remova o item replicado da Recuperação do Local Azure e a replicação para a máquina é interrompida. A configuração de replicação na máquina virtual no local será limpa e a faturação de recuperação do site para este servidor protegido é interrompida.
   - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for apagado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Sítio Azure (a faturação está parada). A configuração de replicação na máquina virtual no local **não será** limpa. 

 > [!NOTE]
     > Se escolheu a opção **Remover,** então execute o seguinte conjunto de scripts para limpar as definições de replicação no hiper-V Server.

> [!NOTE]
> Se já falhou em relação a um VM e está a funcionar em Azure, note que a proteção contra desativação não remove/afeta o falhado sobre o VM.

1. No servidor de anfitriões Hyper-V de origem, para remover a replicação da máquina virtual. Substitua o SQLVM1 com o nome da sua máquina virtual e execute o script a partir de uma PowerShell administrativa

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Desative a proteção para uma máquina virtual Hiper-V replicando-se para Azure usando o cenário VMM do Centro de Sistema para Azure

1. Em **itens protegidos** > **itens replicados,** clique à direita na máquina > **Desative**a replicação .
2. Na **replicação de Desativação,** selecione uma destas opções:

   - **Desative a replicação e remova (recomendado)** - Esta opção remova o item replicado da Recuperação do Local Azure e a replicação para a máquina é interrompida. A configuração de replicação na máquina virtual no local é limpa e a faturação de recuperação do site para este servidor protegido é interrompida.
   - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for apagado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Sítio Azure (a faturação está parada). A configuração de replicação na máquina virtual no local **não será** limpa. 

     > [!NOTE]
     > Se escolheu a opção **Remover,** então inte os seguintes scripts para limpar as definições de replicação no servidor VMM no local.
3. Execute este script no servidor VMM de origem, utilizando o PowerShell (privilégios de administrador necessários) a partir da consola VMM. Substitua o espaço reservado **SQLVM1** com o nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Os passos acima limpam as definições de replicação no servidor VMM. Para parar a replicação da máquina virtual que funciona no servidor de anfitriões Hyper-V, execute este script. Substitua o SQLVM1 pelo nome da sua máquina virtual e host01.contoso.com pelo nome do servidor de anfitriões Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Desative a proteção para uma máquina virtual Hiper-V replicando-se para o servidor de VMM secundário usando o cenário VMM do Centro de Sistema para VMM

1. Em **itens protegidos** > **itens replicados,** clique à direita na máquina > **Desative**a replicação .
2. Na **replicação de Desativação,** selecione uma destas opções:

   - **Desative a replicação e remova (recomendado)** - Esta opção remova o item replicado da Recuperação do Local Azure e a replicação para a máquina é interrompida. A configuração de replicação na máquina virtual no local é limpa e a faturação de recuperação do site para este servidor protegido é interrompida.
   - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for apagado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Sítio Azure (a faturação está parada). A configuração de replicação na máquina virtual no local **não será** limpa. Executar o seguinte conjunto de scripts para limpar as definições de replicação nas máquinas virtuais no local.
     > [!NOTE]
     > Se escolheu a opção **Remover,** então inte os seguintes scripts para limpar as definições de replicação no servidor VMM no local.

3. Execute este script no servidor VMM de origem, utilizando o PowerShell (privilégios de administrador necessários) a partir da consola VMM. Substitua o espaço reservado **SQLVM1** com o nome da sua máquina virtual.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. No servidor VMM secundário, execute este script para limpar as definições para a máquina virtual secundária:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. No servidor VMM secundário, refresque as máquinas virtuais no servidor de anfitriões Hyper-V, para que o VM secundário seja novamente detetado na consola VMM.
6. Os passos acima limpam as definições de replicação no servidor VMM. Se quiser parar a replicação para a máquina virtual, execute o seguinte guião oh os VMs primários e secundários. Substitua o SQLVM1 pelo nome da sua máquina virtual.

        Remove-VMReplication –VMName “SQLVM1”




