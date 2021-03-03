---
title: Remova servidores e desative a proteção | Microsoft Docs
description: Este artigo descreve como não registar servidores a partir de um cofre de recuperação de locais e desativar a proteção para máquinas virtuais e servidores físicos.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: 1356deabd13db1dd2f29ac1b1f088db2120353fe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738859"
---
# <a name="remove-servers-and-disable-protection"></a>Remover servidores e desativar proteção

Este artigo descreve como não registar servidores a partir de um cofre dos Serviços de Recuperação e como desativar a proteção das máquinas protegidas pela Recuperação do Local.


## <a name="unregister-a--configuration-server"></a>Desagregar um servidor de configuração

Se replicar VMware VMs ou servidores físicos Windows/Linux para Azure, pode não registar um servidor de configuração não conectado a partir de um cofre da seguinte forma:

1. [Desativar a proteção das máquinas virtuais.](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
2. [Desassociar ou eliminar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) políticas de replicação.
3. [Eliminar o servidor de configuração](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Desconserte um servidor VMM

1. Pare de replicar máquinas virtuais em nuvens no servidor VMM que pretende remover.
2. Elimine quaisquer mapeamentos de rede utilizados por nuvens no servidor VMM que pretenda eliminar. Na **infraestrutura de recuperação do local** para o  >  **mapeamento da rede VMM do Centro de Sistema,** clique com o  >  botão direito para o mapeamento da rede > **Eliminar**.
3. Note o ID do servidor VMM.
4. Desassociar as políticas de replicação das nuvens no servidor VMM que pretende remover.  Na **Infraestrutura de Recuperação de Sítios**  >  para Políticas de Replicação do Centro de **Sistemas VMM,** clique  >   duas vezes na política associada. Clique com > **nuvem.**
5. Elimine o servidor VMM ou o nó ativo. Na **infraestrutura de recuperação do local** para  >  servidores **VMM do Centro de Sistema**  >  **VMM,** clique com o botão direito no servidor > **Eliminar**.
6. Se o seu servidor VMM estava num estado desligado, em seguida, descarregue e execute o [script de limpeza](/samples/browse/?redirectedfrom=TechNet-Gallery) no servidor VMM. Abrir PowerShell com a opção **Executar como Administrador,** para alterar a política de execução para o âmbito padrão (LocalMachine). No script, especifique o ID do servidor VMM que pretende remover. O script remove as informações de registo e emparelhamento em nuvem do servidor.
5. Execute o script de limpeza em qualquer servidor de VMM secundário.
6. Executar o script de limpeza em quaisquer outros nós de cluster VMM passivos que tenham o Fornecedor instalado.
7. Desinstale manualmente o Fornecedor no servidor VMM. Se tiver um aglomerado, retire de todos os nós.
8. Se as suas máquinas virtuais estavam a replicar-se no Azure, precisa de desinstalar o agente dos Serviços de Recuperação da Microsoft dos anfitriões Hyper-V nas nuvens apagadas.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Desagregar um anfitrião Hiper-V num Site Hiper-V

Os anfitriões hiper-V que não são geridos pelo VMM são recolhidos num site de Hiper-V. Remova um hospedeiro num site de Hiper-V da seguinte forma:

1. Desative a replicação de Hiper-VMs localizados no hospedeiro.
2. Desassociar as políticas para o site Hiper-V. Na **Infraestrutura de Recuperação de Sítios**  >  Para Políticas de Replicação de Sites **Hiper-V,** clique  >   duas vezes na política associada. Clique com o botão direito no site > **Disassociate**.
3. Apague os anfitriões Hiper-V. Na **infraestrutura de recuperação do local** para  >    >  **anfitriões hiper-V,** clique com o botão direito no servidor > **eliminar**.
4. Elimine o site Hyper-V depois de todos os anfitriões terem sido removidos do mesmo. Na **infraestrutura de recuperação do local** para sites  >  **hiper-V,** clique com o  >  botão direito no site > **Eliminar**.
5. Se o seu anfitrião Hyper-V estava num estado **desligado,** então execute o seguinte script em cada anfitrião Hiper-V que removeu. O script limpa as definições no servidor e não o registra do cofre.


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


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Desativar a proteção para um VMware VM ou servidor físico (VMware to Azure)

1. Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina > **Desativar a replicação**.
2. Na página **de replicação de desativação,** selecione uma destas opções:
    - **Desativar a replicação e remover (recomendado)** - Esta opção remove o item replicado da Recuperação do Local de Azure e a replicação da máquina é interrompida. A configuração de replicação no Servidor de Configuração é limpa e a faturação de recuperação do site para este servidor protegido é interrompida. Note que esta opção só pode ser utilizada quando o Servidor de Configuração estiver em estado de ligação.
    - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for eliminado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Local de Azure (a faturação está interrompida). A configuração de replicação no Servidor de Configuração **não será** limpa. 

> [!NOTE]
> Em ambas as opções o serviço de mobilidade não será desinstalado a partir dos servidores protegidos, é necessário desinstalá-lo manualmente. Se planeia proteger novamente o servidor utilizando o mesmo servidor de Configuração, pode não conseguir desinstalar o serviço de mobilidade.

> [!NOTE]
> Se já falhou sobre um VM e está a funcionar em Azure, note que a proteção para desativar não remove/afeta a falha sobre vM.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Desativação da proteção para um Azure VM (Azure to Azure)

-  Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina > **Desativar a replicação**.
> [!NOTE]
> o serviço de mobilidade não será desinstalado dos servidores protegidos, é necessário desinstalá-lo manualmente. Se planeia proteger novamente o servidor, pode não conseguir desinstalar o serviço de mobilidade.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Desativar a proteção para uma máquina virtual Hiper-V (Hyper-V a Azure)

> [!NOTE]
> Utilize este procedimento se estiver a replicar VMs Hiper-V para Azure sem um servidor VMM. Se estiver a replicar as suas máquinas virtuais utilizando o cenário **VMM do Centro de Sistema para Azure,** siga as instruções Desative a proteção para uma máquina virtual Hiper-V que replica usando o cenário VMM do System Center para Azure

1. Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina > **Desativar a replicação**.
2. Na **replicação de desativação,** pode selecionar as seguintes opções:
   - **Desativar a replicação e remover (recomendado)** - Esta opção remove o item replicado da Recuperação do Local de Azure e a replicação da máquina é interrompida. A configuração de replicação na máquina virtual no local será limpa e a faturação de recuperação do local para este servidor protegido é interrompida.
   - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for eliminado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Local de Azure (a faturação está interrompida). A configuração de replicação na máquina virtual no local **não será** limpa. 

    > [!NOTE]
    > Se escolheu a opção **Remover,** então execute o seguinte conjunto de scripts para limpar as definições de replicação no servidor Hiper-V no local.

    > [!NOTE]
    > Se já falhou sobre um VM e está a funcionar em Azure, note que a proteção para desativar não remove/afeta a falha sobre vM.

1. No servidor anfitrião Hyper-V de origem, para remover a replicação para a máquina virtual. Substitua o SQLVM1 pelo nome da sua máquina virtual e execute o script a partir de um PowerShell administrativo

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Desative a proteção para uma máquina virtual Hiper-V que se replica no Azure utilizando o cenário VMM do System Center para Azure

1. Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina > **Desativar a replicação**.
2. Na **replicação de desativação,** selecione uma destas opções:

   - **Desativar a replicação e remover (recomendado)** - Esta opção remove o item replicado da Recuperação do Local de Azure e a replicação da máquina é interrompida. A configuração de replicação na máquina virtual no local é limpa e a faturação de recuperação do local para este servidor protegido é interrompida.
   - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for eliminado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Local de Azure (a faturação está interrompida). A configuração de replicação na máquina virtual no local **não será** limpa. 

     > [!NOTE]
     > Se escolheu a opção **Remover,** em seguida, afinar os seguintes scripts para limpar as definições de replicação no local VMM Server.
3. Execute este script no servidor VMM de origem, utilizando o PowerShell (privilégios de administrador necessários) a partir da consola VMM. Substitua o espaço reservado **SQLVM1** pelo nome da sua máquina virtual.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Os passos acima limpam as definições de replicação no servidor VMM. Para parar a replicação da máquina virtual em funcionamento no servidor anfitrião Hyper-V, executa este script. Substitua o SQLVM1 pelo nome da sua máquina virtual e host01.contoso.com com o nome do servidor anfitrião Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Desativar a proteção de uma máquina virtual Hiper-V que se replica no servidor VMM secundário utilizando o cenário VMM do System Center para VMM

1. Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina > **Desativar a replicação**.
2. Na **replicação de desativação,** selecione uma destas opções:

   - **Desativar a replicação e remover (recomendado)** - Esta opção remove o item replicado da Recuperação do Local de Azure e a replicação da máquina é interrompida. A configuração de replicação na máquina virtual no local é limpa e a faturação de recuperação do local para este servidor protegido é interrompida.
   - **Remover** - Esta opção só deve ser utilizada se o ambiente de origem for eliminado ou não estiver acessível (não ligado). Isto remove o item replicado da Recuperação do Local de Azure (a faturação está interrompida). A configuração de replicação na máquina virtual no local **não será** limpa. Execute o seguinte conjunto de scripts para limpar as definições de replicação nas máquinas virtuais no local.
     > [!NOTE]
     > Se escolheu a opção **Remover,** em seguida, afinar os seguintes scripts para limpar as definições de replicação no local VMM Server.

3. Execute este script no servidor VMM de origem, utilizando o PowerShell (privilégios de administrador necessários) a partir da consola VMM. Substitua o espaço reservado **SQLVM1** pelo nome da sua máquina virtual.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. No servidor VMM secundário, execute este script para limpar as definições para a máquina virtual secundária:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. No servidor VMM secundário, refresque as máquinas virtuais no servidor anfitrião Hiper-V, de modo a que o VM secundário seja novamente detetado na consola VMM.
6. Os passos acima limpam as definições de replicação no servidor VMM. Se quiser parar a replicação para a máquina virtual, execute o seguinte script oh os VMs primários e secundários. Substitua o SQLVM1 pelo nome da sua máquina virtual.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```