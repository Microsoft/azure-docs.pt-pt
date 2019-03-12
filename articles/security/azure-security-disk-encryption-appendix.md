---
title: Apêndice - Azure Disk Encryption para IaaS VMs | Documentos da Microsoft
description: Este artigo é o anexo de Microsoft Azure disco encriptação para Windows e VMs de IaaS Linux.
author: mestew
ms.service: security
ms.topic: article
ms.author: mstewart
ms.date: 03/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6632647c7782411d0d124c325f9bf0afff7e699d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767794"
---
# <a name="appendix-for-azure-disk-encryption"></a>Apêndice para a encriptação de disco do Azure 

Este artigo é um apêndice ao [do Azure Disk Encryption para IaaS VMs](azure-security-disk-encryption-overview.md). Certifique-se de que o Azure Disk Encryption para IaaS VMs artigos primeiro para compreender o contexto de leitura. Este artigo descreve como preparar VHDs previamente encriptados e outras tarefas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Estabelecer a ligação à subscrição
Antes de começar, reveja os [pré-requisitos](azure-security-disk-encryption-prerequisites.md) artigo. Depois de todos os pré-requisitos tiverem sido cumpridos, ligar à sua subscrição ao executar os seguintes cmdlets:

### <a name="bkmk_ConnectPSH"></a> Ligar à sua subscrição com o PowerShell

1. Inicie uma sessão do PowerShell do Azure e iniciar sessão na sua conta do Azure com o seguinte comando:

     ```powershell
     Connect-AzAccount 
     ```
2. Se tiver várias subscrições e pretender especificar uma para utilizar, escreva o seguinte para ver as subscrições da sua conta:
     
     ```powershell
     Get-AzSubscription
     ```
3. Para especificar a subscrição que pretende utilizar, introduza:
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Para verificar se a subscrição configurada está correta, escreva:
     
     ```powershell
     Get-AzSubscription
     ```
5. Se for necessário, ligar ao Azure AD com [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Para confirmar que os cmdlets do Azure Disk Encryption são instalados, escreva:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Revisão [introdução ao Azure PowerShell](/powershell/azure/get-started-azureps) e [AzureAD](/powershell/module/azuread), se for necessário.

### <a name="bkmk_ConnectCLI"></a> Ligar à sua subscrição com a CLI do Azure

1. Inicie sessão no Azure com [início de sessão az](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Se pretender selecionar um inquilino para iniciar sessão em, utilize:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Se tiver várias subscrições e pretender especificar uma subscrição, obter a lista de subscrições com [lista de contas de az](/cli/azure/account#az-account-list) e especifique com [conjunto de conta de az](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Certifique-se a versão instalada.
     
     ```azurecli
        az --version
     ``` 

5. Revisão [introdução à CLI 2.0 do Azure](/cli/azure/get-started-with-azure-cli) se for necessário. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Scripts do PowerShell de exemplo para o Azure Disk Encryption 

- **Lista todas as VMs encriptadas na sua subscrição**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Lista de todos os segredos de encriptação de disco utilizados para encriptar as VMs num cofre de chaves** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Usando o script de PowerShell de pré-requisitos do Azure Disk Encryption
Se já estiver familiarizado com os pré-requisitos para a encriptação de disco do Azure, pode utilizar o [script do PowerShell de pré-requisitos de Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter um exemplo de usar este script do PowerShell, consulte a [encriptar um início rápido de VM](quick-encrypt-vm-powershell.md). Pode remover os comentários de uma seção do script, começando na linha 211, para encriptar todos os discos de VMs existentes no grupo de recursos existente. 

A tabela seguinte mostra quais parâmetros podem ser utilizados no script do PowerShell: 


|Parâmetro|Descrição|É obrigatório|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que o Cofre de chaves pertence.  Será criado um novo grupo de recursos com este nome, caso não exista.| Verdadeiro|
|$keyVaultName|Nome do Cofre de chaves na encriptação de que as chaves devem ser colocados. Será criado um novo cofre com este nome, caso não exista.| Verdadeiro|
|$location|Localização do Cofre de chaves. Certificar-se de que o Cofre de chaves e as VMs a encriptar estão na mesma localização. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da subscrição do Azure a ser utilizado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação do Azure AD que será utilizada para escrever segredos no Cofre de chaves. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existir, transmita o parâmetro de aadClientSecret ao script.|Falso|
|$aadClientSecret|Segredo do cliente de aplicação do Azure AD criado anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação de chave opcional no Cofre de chaves. Será criada uma nova chave com este nome, caso não exista.|Falso|


## <a name="resource-manager-templates"></a>Modelos do Resource Manager

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Encriptar ou desencriptar VMs sem uma aplicação do Azure AD


- [Ativar a encriptação de disco em existente ou executar VMs do Windows de IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Desative a encriptação de disco em existente ou executar VMs do Windows de IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Ativar a encriptação de disco numa VM do Linux IaaS existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
 -  [Desativar a encriptação numa VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.  

### <a name="encrypt-or-decrypt-vm-scale-sets"></a>Encriptar ou desencriptar conjuntos de dimensionamento VM

- [Ativar a encriptação de disco num conjunto de dimensionamento de máquinas virtuais do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Ativar a encriptação de disco num conjunto de dimensionamento de máquinas virtuais do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

 - [Implementar uma VM de dimensionamento definido de VMs do Linux com uma jumpbox e ativa a encriptação no VMSS do Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

 - [Implementar uma VM de dimensionamento definido de Windows VMs com uma jumpbox e ativa a encriptação no VMSS do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Desative a encriptação de disco num conjunto de dimensionamento de máquinas virtuais do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Desative a encriptação de disco num conjunto de dimensionamento de máquinas virtuais do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Encriptar ou desencriptar VMs com uma aplicação do Azure AD (versão anterior) 
 
- [Ativar a encriptação de disco em existente ou executar VMs do Windows de IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Ativar a encriptação de disco numa VM do Linux IaaS existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Desativar a encriptação de disco sobre a execução de IaaS do Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Desativar a encriptação numa VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A desativação da encriptação só é permitida em volumes de dados para VMs do Linux. 

- [Ativar a encriptação de disco em nova VM IaaS do Windows no Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Este modelo cria uma nova VM do Windows criptografado que utiliza a imagem de galeria do Windows Server 2012.

- [Criar um novo Windows IaaS geridos disco VM encriptada da imagem da Galeria](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Este modelo cria uma nova VM do Windows criptografado com discos geridos com a imagem de galeria do Windows Server 2012.

- [Criar um novo disco gerido encriptado a partir de um blob VHD/armazenamento encriptado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado fornecido de um VHD previamente encriptado e as definições de encriptação correspondente

- [Ativar a encriptação de disco numa VM em execução do Windows usando um thumbprint de certificado de cliente do Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Preparar um VHD do Windows encriptadas
As secções que se seguem são necessárias para preparar um VHD do Windows encriptadas para a implementação como um VHD encriptado no IaaS do Azure. Utilize as informações para preparar e arranque de uma VM de Windows atualizados (VHD) no Azure Site Recovery ou no Azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [carregar um VHD generalizado e utilizá-lo para criar novas VMs no Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar política de grupo para permitir não-TPM para proteção do sistema operacional
Configurar a definição de política de grupo do BitLocker **BitLocker Drive Encryption**, que encontrará sob **política de computador Local** > **configuração do computador**  >  **Modelos administrativos** > **componentes do Windows**. Alterar esta definição para **unidades de sistema operativo** > **exigir autenticação adicional no arranque** > **permitir BitLocker sem um TPM compatível**, conforme mostrado na figura a seguir:

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de funcionalidades do BitLocker
Para o Windows Server 2012 e posterior, utilize o seguinte comando:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Para o Windows Server 2008 R2, utilize o seguinte comando:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparar o volume do SO do BitLocker utilizando `bdehdcfg`
Para compactar a partição do sistema operacional e preparar o computador para o BitLocker, execute o [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) se for necessário:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume do sistema operacional usando o BitLocker
Utilize o [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) comando para ativar a encriptação em volume de arranque com um protetor de chave externo. Também coloca a chave externa (ficheiro .bek) no volume ou unidade externa. A encriptação está ativada no volume de arranque do sistema após o reinício seguinte.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Prepare a VM com um dados/recurso separado VHD para obter a chave externa usando o BitLocker.

## <a name="bkmk_LinuxRunning"></a> Encriptar uma unidade de sistema operacional numa VM do Linux em execução

### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para a encriptação de disco do SO

* A VM tem de utilizar uma distribuição compatível com a encriptação de disco do sistema operacional conforme listado no [FAQ de encriptação de disco do Azure](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* A VM tem de ser criada a partir da imagem do Marketplace no Azure Resource Manager.
* VM do Azure com, pelo menos, 4 GB de RAM (recomendado tamanho é de 7 GB).
* (Para RHEL e CentOS) Desative SELinux. Para desativar SELinux, consulte "4.4.2 criação. Desativar o SELinux"a [Guia do administrador e usuário SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na VM.
* Depois de desativar SELinux, reinicie a VM, pelo menos, uma vez.

### <a name="steps"></a>Passos
1. Crie uma VM ao utilizar uma das distribuições especificadas anteriormente.

 CentOS 7.2, encriptação de disco do SO é suportada por meio de uma imagem especial. Para utilizar esta imagem, especifica "7.2n" como o SKU ao criar a VM:

 ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Configure a VM, de acordo com suas necessidades. Se vai encriptar todos os (SO + dados), unidades, as unidades de dados tem de ser especificado e montável de /etc/fstab.

 > [!NOTE]
 > Utilize o UUID =... para especificar a unidades de dados no/etc/fstab em vez de especificar o nome do dispositivo de blocos (por exemplo, / desenvolvimento/sdb1). Durante a encriptação, a ordem das alterações de unidades na VM. Se a VM se baseia numa ordem específica de dispositivos de bloco, ele falhará montá-los depois da encriptação.

3. Termine as sessões SSH.

4. Para encriptar o sistema operacional, especifique volumeType como **todos os** ou **SO** quando ativar a encriptação.

 > [!NOTE]
 > Todos os processos de espaço do utilizador que não estiverem executando `systemd` serviços devem ser eliminados com um `SIGKILL`. Reinicie a VM. Quando ativa a encriptação de disco de SO numa VM em execução, planeje-se no período de indisponibilidade da VM.

5. Periodicamente, monitorizar o progresso de criptografia utilizando as instruções no [próxima seção](#monitoring-os-encryption-progress).

6. Depois de Get-AzVmDiskEncryptionStatus mostra "VMRestartPending", reinicie a VM ao iniciar sessão para o mesmo ou ao utilizar o portal, o PowerShell ou a CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Antes de reiniciar, recomendamos que guarde [diagnósticos de arranque](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) da VM.

## <a name="monitoring-os-encryption-progress"></a>Monitorizar o progresso de criptografia do sistema operacional
Pode monitorizar o progresso de criptografia do sistema operacional de três formas:

* Utilize o `Get-AzVmDiskEncryptionStatus` cmdlet e inspecionar o campo de ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Depois que a VM atinge "Iniciada de encriptação de disco de SO", demora cerca de 40 a 50 minutos num armazenamento Premium de segurança VM.

 Devido [emitir #388](https://github.com/Azure/WALinuxAgent/issues/388) no WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` aparecem como `Unknown` em algumas distribuições. Com WALinuxAgent versão 2.1.5 e mais tarde, este problema é resolvido automaticamente. Se vir `Unknown` na saída, pode verificar o estado de encriptação de disco utilizando o Explorador de recursos do Azure.

 Aceda a [Explorador de recursos do Azure](https://resources.azure.com/)e, em seguida, expanda esta hierarquia no painel de seleção no lado esquerdo:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 No InstanceView, role para baixo para ver o estado de encriptação das suas unidades.

 ![Vista de instância VM](./media/azure-security-disk-encryption/vm-instanceview.png)

* Examinar [diagnósticos de arranque](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Mensagens a partir da extensão de ADE devem ter o prefixo `[AzureDiskEncryption]`.

* Inicie sessão para a VM através de SSH e obter o registo da extensão de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Recomendamos que não início de sessão para a VM enquanto a encriptação de sistema operacional está em curso. Copie os registos apenas quando os outros dois métodos falharam.

## <a name="bkmk_preLinux"></a> Preparar um VHD do Linux encriptadas
A preparação para VHDs encriptadas pode variar consoante a distribuição. Exemplos sobre como preparar [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE), e [CentOS 7](#bkmk_CentOS) estão disponíveis. 

### <a name="bkmk_Ubuntu"></a> 16 de Ubuntu
Configure a encriptação durante a instalação de distribuição, efetuando os seguintes passos:

1. Selecione **configurar volumes criptografados** quando dividir os discos.

 ![Ubuntu 16.04 configurar - configurar volumes encriptados](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de inicialização separado, que não tem de estar encriptada. Criptografe sua unidade de raiz.

 ![Configuração do Ubuntu 16.04 - selecione os dispositivos para encriptar](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase de acesso. Esta é a frase de acesso que carregou para o Cofre de chaves.

 ![Ubuntu 16.04 configurar - fornecer a frase de acesso](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Conclua a criação de partições.

 ![Ubuntu 16.04 configurar - concluir a criação de partições](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando iniciar a VM e são-lhe pedido para uma frase de acesso, utilize a frase de acesso que indicou no passo 3.

 ![Ubuntu 16.04 configurar - forneça a frase de acesso no arranque](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Preparar a VM para carregando-a no Azure com [estas instruções](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não execute o último passo (desaprovisionar a VM) ainda.

Configure a encriptação funcione com o Azure, efetuando os seguintes passos:

1. Crie um ficheiro em /usr/local/sbin/azure_crypt_key.sh, com o conteúdo no seguinte script. Preste atenção às KeyFileName, porque é o nome de ficheiro da frase de acesso utilizado pelo Azure.

    ```bash
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Alterar a configuração dos crypt no *nomedeanfitrião crypttab*. Deve ter o seguinte aspeto:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Se está editando *azure_crypt_key.sh* no Windows e é copiado para o Linux, execute `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Adicione permissões executáveis para o script:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Editar */etc/initramfs-tools/modules* ao acrescentar linhas:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Execute `update-initramfs -u -k all` para atualizar o initramfs para tornar o `keyscript` entrem em vigor.

7. Agora pode desaprovisionar a VM.

 ![Configuração do Ubuntu 16.04 - update-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continuar para o passo seguinte e carregar o seu VHD para o Azure.

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
Para configurar a encriptação durante a instalação de distribuição, siga os passos abaixo:
1. Quando a partição dos discos, selecione **criptografar o Volume grupo**e, em seguida, introduza uma palavra-passe. Esta é a palavra-passe que irá carregar para o seu Cofre de chaves.

 ![openSUSE 13.2 configuração - criptografar o Volume de grupo](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. A VM com a sua palavra-passe de arranque.

 ![openSUSE 13.2 configurar - forneça a frase de acesso no arranque](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Preparar a VM para carregar para o Azure ao seguir as instruções em [preparar uma máquina virtual SLES ou openSUSE para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não execute o último passo (desaprovisionar a VM) ainda.

Para configurar a encriptação funcione com o Azure, siga os passos abaixo:
1. Edite o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Comente estas linhas ao final do /usr/lib/dracut/modules.d/90crypt/module-setup.sh ficheiro:
 ```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Acrescente a seguinte linha no início do /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ficheiro:
 ```bash
    DRACUT_SYSTEMD=0
 ```
E altere todas as ocorrências de:
 ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
para:
```bash
    if [ 1 ]; then
```
4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescentá-lo a "# LUKS aberto a dispositivo":

    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Executar `/usr/sbin/dracut -f -v` para atualizar o initrd.

6. Agora pode desaprovisionar a VM e carregar o seu VHD para o Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Para configurar a encriptação durante a instalação de distribuição, siga os passos abaixo:
1. Selecione **criptografar meus dados** quando dividir os discos.

 ![Configurar o centOS 7 - destino de instalação](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Certifique-se **Encrypt** está selecionada para a partição de raiz.

 ![Configurar o centOS 7 - selecione criptografar para a partição de raiz](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase de acesso. Esta é a frase de acesso que deverá carregar para o seu Cofre de chaves.

 ![Configuração do centOS 7 - fornecer a frase de acesso](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Quando iniciar a VM e são-lhe pedido para uma frase de acesso, utilize a frase de acesso que indicou no passo 3.

 ![CentOS 7 configurar - introduza a frase de acesso na inicialização](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Preparar a VM para carregar para o Azure ao utilizar as instruções de "CentOS 7.0 +" na [preparar uma máquina de virtual baseada em CentOS para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não execute o último passo (desaprovisionar a VM) ainda.

6. Agora pode desaprovisionar a VM e carregar o seu VHD para o Azure.

Para configurar a encriptação funcione com o Azure, siga os passos abaixo:

1. Edite o /etc/dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Comente estas linhas ao final do /usr/lib/dracut/modules.d/90crypt/module-setup.sh ficheiro:
```bash
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Acrescente a seguinte linha no início do /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh ficheiro:
```bash
    DRACUT_SYSTEMD=0
```
E altere todas as ocorrências de:
```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
para
```bash
    if [ 1 ]; then
```
4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e anexe o seguinte depois de dispositivo"LUKS aberto de #":
    ```bash
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Executar o "/ usr/sbin/dracut - f - v" para atualizar o initrd.

![Configuração de centOS 7 - executar /usr/sbin/dracut -f - v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Carregar o VHD encriptado para uma conta de armazenamento do Azure
Depois de é ativada a encriptação do BitLocker ou encriptação de DM-Crypt, o VHD encriptado local tem de ser carregado para a sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Carregar o segredo para a VM encriptada para o seu Cofre de chaves
Ao encriptar a utilizar uma aplicação do Azure AD (versão anterior), o segredo de encriptação de disco que obteve anteriormente tem de ser carregado como um segredo no Cofre de chaves. O Cofre de chaves tem de ter a encriptação de disco e permissões ativadas para o cliente do Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Segredo de encriptação de disco não criptografado com uma KEK
Para configurar o segredo no Cofre de chaves, utilize [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Se tiver uma máquina virtual do Windows, o ficheiro de bek é codificado como uma cadeia base64 e, em seguida, é carregado para o seu Cofre de chaves com o `Set-AzureKeyVaultSecret` cmdlet. Para o Linux, a frase de acesso é codificado como uma cadeia base64 e, em seguida, é carregado para o Cofre de chaves. Além disso, certifique-se de que as etiquetas seguintes estão definidas quando cria o segredo no Cofre de chaves.

#### <a name="windows-bek-file"></a>Ficheiro do Windows BEK
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzureKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Utilize o `$secretUrl` no próximo passo para [anexar o disco do SO sem utilizar KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Segredo de encriptação de disco encriptado com uma KEK
Antes de carregar o segredo ao Cofre de chaves, opcionalmente, poderá criptografá-los com uma chave de encriptação de chave. Utilizar a moldagem [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) primeiro criptografar o segredo usando a chave de encriptação de chave. O resultado desta operação de moldagem é uma cadeia de URL codificado em base64 que, em seguida, pode carregar como um segredo ao utilizar o [ `Set-AzKeyVaultSecret` ](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Uso `$KeyEncryptionKey` e `$secretUrl` no passo seguinte para [anexar o disco do SO usando KEK](#BKMK_URLKEK).

##  <a name="bkmk_SecretURL"></a> Especifique um URL de segredo quando anexa um disco de SO

###  <a name="bkmk_URLnoKEK"></a>Sem utilizar um KEK
Enquanto está anexando o disco do SO, precisa passar `$secretUrl`. O URL foi gerado na secção "segredo de encriptação de disco não criptografado com uma KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Usando um KEK
Ao anexar o disco do SO, passa `$KeyEncryptionKey` e `$secretUrl`. O URL foi gerado na secção "Segredo de encriptação de disco encriptado com uma KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
