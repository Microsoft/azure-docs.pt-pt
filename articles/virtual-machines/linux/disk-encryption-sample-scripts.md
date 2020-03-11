---
title: Scripts de amostra de encriptação de disco azure
description: Este artigo é o apêndice da Encriptação do Disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: c98da4b41da183f56d80fad1e8c01706d1cfcf23
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970517"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Scripts de amostra de encriptação de disco azure 

Este artigo fornece scripts de amostra para a preparação de VHDs pré-encriptados e outras tarefas.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Scripts do PowerShell de exemplo para o Azure Disk Encryption 

- **Listar todos os VMs encriptados na sua subscrição**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Liste todos os segredos de encriptação do disco usados para encriptar VMs num cofre chave** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Usar o script de encriptação do disco Azure pré-requisitos PowerShell
Se já está familiarizado com os pré-requisitos para a encriptação do disco Azure, pode utilizar o pré-requisito de encriptação do [disco Azure PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para um exemplo de utilização deste script PowerShell, consulte o [Encrypt a VM Quickstart](disk-encryption-powershell-quickstart.md). Pode remover os comentários de uma seção do script, começando na linha 211, para encriptar todos os discos de VMs existentes no grupo de recursos existente. 

A tabela seguinte mostra quais parâmetros podem ser utilizados no script do PowerShell: 


|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que o Cofre de chaves pertence.  Será criado um novo grupo de recursos com este nome, caso não exista.| Verdadeiro|
|$keyVaultName|Nome do Cofre de chaves na encriptação de que as chaves devem ser colocados. Será criado um novo cofre com este nome, caso não exista.| Verdadeiro|
|$location|Localização do Cofre de chaves. Certificar-se de que o Cofre de chaves e as VMs a encriptar estão na mesma localização. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da subscrição do Azure a ser utilizado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação do Azure AD que será utilizada para escrever segredos no Cofre de chaves. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existir, transmita o parâmetro de aadClientSecret ao script.|Falso|
|$aadClientSecret|Segredo do cliente de aplicação do Azure AD criado anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação de chave opcional no Cofre de chaves. Será criada uma nova chave com este nome, caso não exista.|Falso|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Encriptar ou desencriptar VMs sem uma aplicação do Azure AD

- [Ativar a encriptação do disco num VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Desative a encriptação num VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Encriptar ou desencriptar VMs com uma aplicação do Azure AD (versão anterior) 
 
- [Ativar a encriptação do disco num VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Desative a encriptação num VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A desativação da encriptação só é permitida em volumes de dados para VMs do Linux. 


- [Criar um novo disco gerido encriptado a partir de uma bolha vHD/armazenamento pré-encriptada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado fornecido de um VHD previamente encriptado e as definições de encriptação correspondente





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Encriptar uma unidade de SO num Linux VM em execução

### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para a encriptação de disco do SO

* O VM deve estar a utilizar uma distribuição compatível com encriptação de disco OS, tal como listado nos [sistemas operativos suportados pela Encriptação do Disco Azure.](disk-encryption-overview.md#supported-vm-sizes) 
* A VM tem de ser criada a partir da imagem do Marketplace no Azure Resource Manager.
* VM do Azure com, pelo menos, 4 GB de RAM (recomendado tamanho é de 7 GB).
* (Para RHEL e CentOS) Desative SELinux. Para desativar SELinux, consulte "4.4.2 criação. Desativando o SELinux" no [Guia do Utilizador e Administrador selinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) no VM.
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

4. Para encriptar o SISTEMA, especifique o volumeType como **All** ou **OS** quando ativa a encriptação.

   > [!NOTE]
   > Todos os processos de espaço de utilização que não estejam a funcionar como serviços `systemd` devem ser mortos com uma `SIGKILL`. Reinicie a VM. Quando ativa a encriptação de disco de SO numa VM em execução, planeje-se no período de indisponibilidade da VM.

5. Monitorize periodicamente o progresso da encriptação utilizando as instruções na [secção seguinte](#monitoring-os-encryption-progress).

6. Depois do Get-AzVmDiskEncryptionStatus mostrar "VMRestartPending", reiniciar o vM quer através da sessão, quer através do portal PowerShell, ou CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Antes de reiniciar, recomendamos que guarde [diagnósticos](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) de arranque do VM.

## <a name="monitoring-os-encryption-progress"></a>Monitorizar o progresso de criptografia do sistema operacional
Pode monitorizar o progresso de criptografia do sistema operacional de três formas:

* Utilize o `Get-AzVmDiskEncryptionStatus` cmdlet e inspecione o campo ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Depois que a VM atinge "Iniciada de encriptação de disco de SO", demora cerca de 40 a 50 minutos num armazenamento Premium de segurança VM.

  Devido [ao problema #388](https://github.com/Azure/WALinuxAgent/issues/388) no WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` aparecem como `Unknown` em algumas distribuições. Com WALinuxAgent versão 2.1.5 e mais tarde, este problema é resolvido automaticamente. Se vir `Unknown` na saída, pode verificar o estado de encriptação do disco utilizando o Explorador de Recursos Azure.

  Vá ao [Azure Resource Explorer,](https://resources.azure.com/)e depois expanda esta hierarquia no painel de seleção à esquerda:

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

  ![Vista de instância VM](./media/disk-encryption/vm-instanceview.png)

* Olhe para [os diagnósticos de botas.](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) As mensagens da extensão ADE devem ser pré-fixadas com `[AzureDiskEncryption]`.

* Inicie sessão para a VM através de SSH e obter o registo da extensão de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Recomendamos que não início de sessão para a VM enquanto a encriptação de sistema operacional está em curso. Copie os registos apenas quando os outros dois métodos falharam.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Prepare um Linux VHD pré-encriptado
A preparação para VHDs encriptadas pode variar consoante a distribuição. Exemplos sobre a preparação do Ubuntu 16, abre o SUSE 13.2 e o CentOS 7. 

### <a name="ubuntu-16"></a>Ubuntu 16
Configure a encriptação durante a instalação de distribuição, efetuando os seguintes passos:

1. **Selecione Configure volumes encriptados** quando dividir os discos.

   ![Ubuntu 16.04 configurar - configurar volumes encriptados](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de inicialização separado, que não tem de estar encriptada. Criptografe sua unidade de raiz.

   ![Configuração do Ubuntu 16.04 - selecione os dispositivos para encriptar](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase de acesso. Esta é a frase de acesso que carregou para o Cofre de chaves.

   ![Ubuntu 16.04 configurar - fornecer a frase de acesso](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Conclua a criação de partições.

   ![Ubuntu 16.04 configurar - concluir a criação de partições](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando iniciar a VM e são-lhe pedido para uma frase de acesso, utilize a frase de acesso que indicou no passo 3.

   ![Ubuntu 16.04 configurar - forneça a frase de acesso no arranque](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare o VM para o upload para O Azure utilizando [estas instruções](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não execute o último passo (desaprovisionar a VM) ainda.

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

2. Mude o config da cripta em */etc/cripto .* Deve ter o seguinte aspeto:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Adicione permissões executáveis para o script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Editar */etc/initramfs-tools/módulos* por linhas de aparação:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Faça `update-initramfs -u -k all` para atualizar os initramfs para que o `keyscript` faça efeito.

7. Agora pode desaprovisionar a VM.

   ![Configuração do Ubuntu 16.04 - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continuar para o passo seguinte e carregar o seu VHD para o Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Para configurar a encriptação durante a instalação de distribuição, siga os passos abaixo:
1. Quando dividir os discos, selecione **Encrypt Volume Group**e, em seguida, introduza uma palavra-passe. Esta é a palavra-passe que irá carregar para o seu Cofre de chaves.

   ![openSUSE 13.2 configuração - criptografar o Volume de grupo](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. A VM com a sua palavra-passe de arranque.

   ![openSUSE 13.2 configurar - forneça a frase de acesso no arranque](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare o VM para o upload para O Azure seguindo as instruções em Preparar uma SLES ou abra uma [máquina virtual SUSE para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não execute o último passo (desaprovisionar a VM) ainda.

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

### <a name="centos-7-and-rhel-81"></a>CentOS 7 e RHEL 8.1

Para configurar a encriptação durante a instalação de distribuição, siga os passos abaixo:
1. Selecione **Encriptar os meus dados** quando estiver a dividir discos.

   ![Configurar o centOS 7 - destino de instalação](./media/disk-encryption/centos-encrypt-fig1.png)

2. Certifique-se de que **o Encriptar** está selecionado para a partilha de raízes.

   ![Configurar o centOS 7 - selecione criptografar para a partição de raiz](./media/disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase de acesso. Esta é a frase de acesso que deverá carregar para o seu Cofre de chaves.

   ![Configuração do centOS 7 - fornecer a frase de acesso](./media/disk-encryption/centos-encrypt-fig3.png)

4. Quando iniciar a VM e são-lhe pedido para uma frase de acesso, utilize a frase de acesso que indicou no passo 3.

   ![CentOS 7 configurar - introduza a frase de acesso na inicialização](./media/disk-encryption/centos-encrypt-fig4.png)

5. Prepare o VM para o upload para O Azure utilizando as instruções "CentOS 7.0+" em [Preparar uma máquina virtual baseada em CentOS para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não execute o último passo (desaprovisionar a VM) ainda.

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

    ![Configuração de centOS 7 - executar /usr/sbin/dracut -f - v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Faça upload de VHD encriptado para uma conta de armazenamento Azure
Depois de ativada a encriptação DM-Crypt, o VHD encriptado local precisa de ser enviado para a sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Faça upload do segredo para o VM pré-encriptado para o seu cofre chave
Ao encriptar a utilizar uma aplicação do Azure AD (versão anterior), o segredo de encriptação de disco que obteve anteriormente tem de ser carregado como um segredo no Cofre de chaves. O Cofre de chaves tem de ter a encriptação de disco e permissões ativadas para o cliente do Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de encriptação de disco não encriptado com um KEK
Para configurar o segredo no seu cofre de chaves, use [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase de passe é codificada como uma corda base64 e depois enviada para o cofre da chave. Além disso, certifique-se de que as etiquetas seguintes estão definidas quando cria o segredo no Cofre de chaves.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Utilize o `$secretUrl` no próximo passo para [fixar o disco OS sem utilizar](#without-using-a-kek)o KEK .

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com um KEK
Antes de carregar o segredo ao Cofre de chaves, opcionalmente, poderá criptografá-los com uma chave de encriptação de chave. Utilize a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de embrulho para encriptar primeiro o segredo utilizando a chave de encriptação da chave. A saída desta operação de embrulho é uma cadeia codificada por URL base64, que pode então carregar como segredo utilizando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

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

Utilize `$KeyEncryptionKey` e `$secretUrl` no próximo passo para [a fixação do disco OS utilizando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco OS

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto está a anexar o disco de solo, tem de passar `$secretUrl`. O URL foi gerado na secção "segredo de encriptação de disco não criptografado com uma KEK".
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
### <a name="using-a-kek"></a>Usando um KEK
Quando fixar o disco OS, passe `$KeyEncryptionKey` e `$secretUrl`. O URL foi gerado na secção "Segredo de encriptação de disco encriptado com uma KEK".
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
