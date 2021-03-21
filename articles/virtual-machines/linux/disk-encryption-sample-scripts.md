---
title: Scripts de exemplo do Azure Disk Encryption
description: Este artigo é o apêndice da Encriptação do Disco Azure da Microsoft para Os VMs Linux.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: f11677d9ebc31f1c1f7cc6332b07b69f8e35ad52
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561182"
---
# <a name="azure-disk-encryption-sample-scripts-for-linux-vms"></a>Scripts de encriptação de disco Azure para VMs Linux

Este artigo fornece scripts de amostra para preparar VHDs pré-encriptados e outras tarefas.  

> [!NOTE]
> Todos os scripts referem-se à versão mais recente, não-AAD da ADE, exceto quando anotado.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Scripts PowerShell de amostra para encriptação do disco Azure 

- **Listar todos os VMs encriptados na sua subscrição**
  
  Pode encontrar todos os VMs encriptados a ADE e a versão de extensão, em todos os grupos de recursos presentes numa subscrição, utilizando [este script PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Em alternativa, estes cmdlets mostrarão todos os VMs encriptados a ADE (mas não a versão de extensão):

   ```azurepowershell-interactive
   $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
   $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
   Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
   ```

- **Listar todas as instâncias VMSS encriptadas na sua subscrição**
    
    Pode encontrar todas as instâncias VMSS encriptadas pela ADE e a versão de extensão, em todos os grupos de recursos presentes numa subscrição, utilizando [este script PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).

- **Lista todos os segredos de encriptação do disco usados para encriptar VMs em um cofre chave** 

   ```azurepowershell-interactive
   Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
   ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Utilizando o encriptação do disco Azure pré-requisitos powerShell script
Se já estiver familiarizado com os pré-requisitos para encriptação do disco Azure, pode utilizar o [script Azure Disk Encryption pré-requisitos powerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para um exemplo de utilização deste script PowerShell, consulte o [Crycrim a VM Quickstart](disk-encryption-powershell-quickstart.md). Pode remover os comentários de uma secção do script, a partir da linha 211, para encriptar todos os discos para VM existentes num grupo de recursos existente. 

A tabela a seguir mostra quais os parâmetros que podem ser utilizados no script PowerShell: 


|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que pertence o KeyVault.  Um novo grupo de recursos com este nome será criado se não existir.| Verdadeiro|
|$keyVaultName|Nome do KeyVault em que as chaves de encriptação devem ser colocadas. Um novo cofre com este nome será criado se um não existir.| Verdadeiro|
|$location|Localização do KeyVault. Certifique-se de que os KeyVault e VMs a encriptar estão no mesmo local. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da assinatura Azure a ser usado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação AD AZure que será usada para escrever segredos ao KeyVault. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existe, passe o parâmetro aadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo de cliente da aplicação AZure AD que foi criada anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação opcional em KeyVault. Uma nova chave com este nome será criada se não existir.|Falso|

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografe ou desencriptar VMs sem uma aplicação AD AZure

- [Ativar a encriptação do disco num VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Desative a encriptação num Linux VM em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A encriptação incapacitante só é permitida em volumes de dados para VMs Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografe ou desencriptar VMs com uma aplicação AD Azure (versão anterior)
 
- [Ativar a encriptação do disco num VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Desative a encriptação num Linux VM em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A encriptação incapacitante só é permitida em volumes de dados para VMs Linux. 


- [Criar um novo disco gerido encriptado a partir de uma bolha VHD/armazenamento pré-encriptado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado desde um VHD pré-encriptado e as suas definições de encriptação correspondentes

## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Encriptando uma unidade de SO em um Linux VM em execução

### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para encriptação do disco oss

* O VM deve estar a utilizar uma distribuição compatível com encriptação do disco OS, tal como listada nos [sistemas operativos suportados por encriptação de disco Azure](disk-encryption-overview.md#supported-vms) 
* O VM deve ser criado a partir da imagem marketplace em Azure Resource Manager.
* Azure VM com pelo menos 4 GB de RAM (tamanho recomendado é de 7 GB).
* (Para RHEL e CentOS) Desative o SELinux. Para desativar o SELinux, consulte "4.4.2. Desativar o SELinux" no [Guia do Utilizador e Administrador da SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) sobre o VM.
* Depois de desativar o SELinux, reinicie o VM pelo menos uma vez.

### <a name="steps"></a>Passos
1. Crie um VM utilizando uma das distribuições especificadas anteriormente.

   Para o CentOS 7.2, a encriptação do disco OS é suportada através de uma imagem especial. Para utilizar esta imagem, especifique "7.2n" como o SKU quando criar o VM:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configure o VM de acordo com as suas necessidades. Se pretender encriptar todas as unidades (OS + dados), as unidades de dados precisam de ser especificadas e montáveis a partir de /etc/fstab.

   > [!NOTE]
   > Use UUID=... especificar as unidades de dados em /etc/fstab em vez de especificar o nome do dispositivo de bloqueio (por exemplo, /dev/sdb1). Durante a encriptação, a ordem de acionamentos muda no VM. Se o seu VM depender de uma ordem específica de dispositivos de bloqueio, não os montará após a encriptação.

3. Assine as sessões do SSH.

4. Para encriptar o SO, especifique o volumeType como **All** ou **OS** quando ativar a encriptação.

   > [!NOTE]
   > Todos os processos de espaço de utilização que não estejam a funcionar como `systemd` serviços devem ser mortos com um `SIGKILL` . Reinicie o VM. Quando ativar a encriptação do disco DE num VM em execução, planeie o tempo de inatividade em VM.

5. Monitorize periodicamente o progresso da encriptação utilizando as instruções na [secção seguinte](#monitoring-os-encryption-progress).

6. Depois de Get-AzVmDiskEncryptionStatus mostra "VMRestartPending", reinicie o seu VM, insinuá-lo ou utilizando o portal, PowerShell ou CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Antes de reiniciar, recomendamos que guarde os [diagnósticos](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) de arranque do VM.

## <a name="monitoring-os-encryption-progress"></a>Monitorização do progresso da encriptação do OS
Pode monitorizar o progresso da encriptação do SO de três formas:

* Utilize o `Get-AzVmDiskEncryptionStatus` cmdlet e inspecione o campo ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Depois de o VM chegar à "encriptação do disco OS iniciada", demora cerca de 40 a 50 minutos num VM apoiado por armazenamento Premium.

  Por causa da [questão #388](https://github.com/Azure/WALinuxAgent/issues/388) na WALinuxAgent, `OsVolumeEncrypted` e aparecer como em `DataVolumesEncrypted` `Unknown` algumas distribuições. Com a versão WALinuxAgent 2.1.5 e posteriormente, este problema é corrigido automaticamente. Se vir `Unknown` na saída, pode verificar o estado de encriptação do disco utilizando o Explorador de Recursos Azure.

  Vá ao [Azure Resource Explorer](https://resources.azure.com/)e, em seguida, expanda esta hierarquia no painel de seleção à esquerda:

  ```
  |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
  ```                

  No InstanceView, desloque-se para baixo para ver o estado de encriptação das suas unidades.

  ![Vista de exemplo vm](./media/disk-encryption/vm-instanceview.png)

* Veja os [diagnósticos de botas.](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) As mensagens da extensão ADE devem ser pré-fixas com `[AzureDiskEncryption]` .

* Inicie sessão no VM via SSH e obtenha o registo de extensão a partir de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Recomendamos que não faça sessão no VM enquanto a encriptação do SO estiver em andamento. Copie os registos apenas quando os outros dois métodos falharem.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparar um Linux VHD pré-encriptado
A preparação para VHDs pré-encriptados pode variar dependendo da distribuição. Estão disponíveis exemplos sobre a preparação do Ubuntu 16, o openSUSE 13.2 e o CentOS 7. 

### <a name="ubuntu-16"></a>Ubuntu 16
Configure a encriptação durante a instalação de distribuição, fazendo os seguintes passos:

1. Selecione **Configurar volumes encriptados** quando dividir os discos.

   ![Configuração Ubuntu 16.04 - Configuração de configuração](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de arranque separada, que não deve ser encriptada. Criptografe o seu impulso de raiz.

   ![Configuração Ubuntu 16.04 - Selecione dispositivos para encriptar](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase-passe. Esta é a frase que enviaste para o cofre das chaves.

   ![Configuração Ubuntu 16.04 - Fornecer palavras-passe](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Termine a partição.

   ![Configuração Ubuntu 16.04 - Terminar a partição](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando iniciar o VM e lhe pedirem uma palavra-passe, utilize a frase de passe que forneceu no passo 3.

   ![Configuração Ubuntu 16.04 - Forneça a palavra-passe no arranque](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare o VM para o upload para Azure utilizando [estas instruções](./create-upload-ubuntu.md?toc=/azure/virtual-machines/linux/toc.json). Não corra o último passo (desprovisionando o VM) ainda.

Configure a encriptação para trabalhar com o Azure, fazendo os seguintes passos:

1. Crie um ficheiro em /usr/local/sbin/azure_crypt_key.sh, com o conteúdo no seguinte script. Preste atenção ao KeyFileName, porque é o nome de ficheiro de palavras-passe usado pelo Azure.

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

2. Altere a cripta config em */etc/crypttab*. Deverá ter o seguinte aspeto:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Adicione permissões executáveis ao script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Editar */etc/initramfs-tools/módulos* por linhas de fixação:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Corra `update-initramfs -u -k all` para atualizar os initramfs para fazer o `keyscript` efeito.

7. Agora pode desprovisionar o VM.

   ![Configuração Ubuntu 16.04 - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continue até ao próximo passo e carreque o seu VHD em Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Para configurar a encriptação durante a instalação de distribuição, faça os seguintes passos:
1. Ao dividir os discos, **selecione Encrypt Volume Group** e introduza uma palavra-passe. Esta é a senha que vai enviar para o cofre da chave.

   ![openSUSE 13.2 Configuração - Grupo de Volume encriptado](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Arranque o VM usando a sua senha.

   ![openSUSE 13.2 Configuração - Forneça a palavra-passe no arranque](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare o VM para o upload para Azure seguindo as instruções em [Preparar uma máquina virtual SLES ou abrir Azure](./suse-create-upload-vhd.md?toc=/azure/virtual-machines/linux/toc.json#prepare-opensuse-131). Não corra o último passo (desprovisionando o VM) ainda.

Para configurar a encriptação para trabalhar com o Azure, faça os seguintes passos:
1. Editar o /etc/dracut.conf e adicionar a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Comente estas linhas até ao final do ficheiro /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. Apender a seguinte linha no início do ficheiro /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   E alterar todas as ocorrências de:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   para:
   ```bash
    if [ 1 ]; then
   ```
4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e apdicê-lo ao "# Dispositivo LUKS aberto":

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
5. Corra `/usr/sbin/dracut -f -v` para atualizar o initrd.

6. Agora pode desprovisionar o VM e enviar o seu VHD para OZure.

### <a name="centos-7-and-rhel-7"></a>CentOS 7 e RHEL 7

Para configurar a encriptação durante a instalação de distribuição, faça os seguintes passos:
1. Selecione **Criptografar os meus dados** quando tiver discos de partição.

   ![CentOS 7 Configuração -Destino de instalação](./media/disk-encryption/centos-encrypt-fig1.png)

2. Certifique-se de que **o Crispto está** selecionado para a partição de raiz.

   ![Configuração CentOS 7 -Selecione encriptar para partição de raiz](./media/disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase-passe. Esta é a frase que vais enviar para o teu cofre.

   ![Configuração CentOS 7 - forneça a palavra-passe](./media/disk-encryption/centos-encrypt-fig3.png)

4. Quando iniciar o VM e lhe pedirem uma palavra-passe, utilize a frase de passe que forneceu no passo 3.

   ![Configuração CentOS 7 - Introduza a frase de passe no arranque](./media/disk-encryption/centos-encrypt-fig4.png)

5. Prepare o VM para o upload para O Azure utilizando as instruções "CentOS 7.0+" em [Preparar uma máquina virtual baseada em CentOS para o Azure](./create-upload-centos.md?toc=/azure/virtual-machines/linux/toc.json#centos-70). Não corra o último passo (desprovisionando o VM) ainda.

6. Agora pode desprovisionar o VM e enviar o seu VHD para OZure.

Para configurar a encriptação para trabalhar com o Azure, faça os seguintes passos:

1. Editar o /etc/dracut.conf e adicionar a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Comente estas linhas até ao final do ficheiro /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. Apender a seguinte linha no início do ficheiro /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   E alterar todas as ocorrências de:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   para
   ```bash
    if [ 1 ]; then
   ```
4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e anexar o seguinte após o "# Dispositivo ABERTO LUKS":
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
5. Executar o "/usr/sbin/dracut -f-v" para atualizar o initrd.

    ![CentOS 7 Configuração - executar /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Faça upload de VHD encriptado para uma conta de armazenamento Azure
Após DM-Crypt encriptação está ativada, o VHD encriptado local precisa de ser carregado para a sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Faça o upload do segredo para o VM pré-encriptado para o seu cofre de chaves
Ao encriptar usando uma aplicação AD Azure (versão anterior), o segredo de encriptação de disco que obteve anteriormente deve ser carregado como um segredo no seu cofre chave. O cofre-chave precisa de ter encriptação de disco e permissões ativadas para o seu cliente AZure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de encriptação de disco não encriptado com um KEK
Para configurar o segredo no seu cofre de chaves, utilize [o Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A palavra-passe é codificada como uma corda base64 e depois enviada para o cofre da chave. Além disso, certifique-se de que as seguintes etiquetas são definidas quando criar o segredo no cofre da chave.

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


Utilize o `$secretUrl` passo seguinte para fixar o disco DE SEM utilizar [KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com um KEK
Antes de enviar o segredo para o cofre da chave, pode criptografá-lo opcionalmente usando uma chave de encriptação. Utilize a [API](/rest/api/keyvault/wrapkey) de embrulho para encriptar primeiro o segredo utilizando a chave de encriptação. A saída desta operação de embrulho é uma cadeia codificada de URL base64, que pode então carregar como um segredo usando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

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

Utilize `$KeyEncryptionKey` e no passo seguinte para a `$secretUrl` [fixação do disco de sos utilizando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco DE

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto está a prender o disco de so, tem de `$secretUrl` passar. O URL foi gerado no "Segredo de encriptação de disco não encriptado com uma secção KEK".
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
Quando ligar o disco DE, passe `$KeyEncryptionKey` e `$secretUrl` . O URL foi gerado no "Segredo de encriptação do disco encriptado com uma secção KEK".
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
