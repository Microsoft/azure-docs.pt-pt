---
title: Scripts de exemplo do Azure Disk Encryption
description: Este artigo é o apêndice da Encriptação do Disco Microsoft Azure para VMs Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b54f9f3466fe5f7e2da622077f53575d6f43f72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585963"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Scripts de exemplo do Azure Disk Encryption 

Este artigo fornece scripts de amostra para a preparação de VHDs pré-encriptados e outras tarefas.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Scripts De Sample PowerShell para encriptação de disco azure 

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
Se já está familiarizado com os pré-requisitos para a encriptação do disco Azure, pode utilizar o pré-requisito de encriptação do [disco Azure PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para um exemplo de utilização deste script PowerShell, consulte o [Encrypt a VM Quickstart](disk-encryption-powershell-quickstart.md). Pode remover os comentários de uma secção do script, a partir da linha 211, para encriptar todos os discos para VMs existentes num grupo de recursos existente. 

A tabela que se segue mostra quais os parâmetros que podem ser utilizados no script PowerShell: 


|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos a que pertence o KeyVault.  Um novo grupo de recursos com este nome será criado se não existir.| Verdadeiro|
|$keyVaultName|Nome do KeyVault em que as chaves de encriptação devem ser colocadas. Um novo cofre com este nome será criado se não existir.| Verdadeiro|
|$location|Localização do KeyVault. Certifique-se de que os KeyVault e VMs a encriptar estão no mesmo local. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da assinatura Azure a utilizar.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome da aplicação Azure AD que será usada para escrever segredos para keyVault. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esta aplicação já existir, passe o parâmetro AadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo do cliente da aplicação Azure AD que foi criada anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de encriptação opcional no KeyVault. Uma nova chave com este nome será criada se não existir.|Falso|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografe ou desencriptar VMs sem uma aplicação Azure AD

- [Ativar a encriptação do disco num VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Desative a encriptação num VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A encriptação incapacitante só é permitida em volumes de dados para VMs Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografe ou desencriptar VMs com uma aplicação Azure AD (versão anterior) 
 
- [Ativar a encriptação do disco num VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Desative a encriptação num VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A encriptação incapacitante só é permitida em volumes de dados para VMs Linux. 


- [Criar um novo disco gerido encriptado a partir de uma bolha vHD/armazenamento pré-encriptada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerido encriptado fornecido a um VHD pré-encriptado e às respetivas definições de encriptação





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Encriptar uma unidade de SO num Linux VM em execução

### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para encriptação do disco OS

* O VM deve estar a utilizar uma distribuição compatível com encriptação de disco OS, tal como listado nos [sistemas operativos suportados pela Encriptação do Disco Azure.](disk-encryption-overview.md#supported-vms) 
* O VM deve ser criado a partir da imagem do Marketplace no Azure Resource Manager.
* VM azure com pelo menos 4 GB de RAM (o tamanho recomendado é de 7 GB).
* (Para RHEL e CentOS) Desativar o SELinux. Para desativar o SELinux, consulte "4.4.2. Desativando o SELinux" no [Guia do Utilizador e Administrador selinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) no VM.
* Depois de desativar o SELinux, reinicie o VM pelo menos uma vez.

### <a name="steps"></a>Passos
1. Crie um VM utilizando uma das distribuições especificadas anteriormente.

   Para o CentOS 7.2, a encriptação do disco OS é suportada através de uma imagem especial. Para utilizar esta imagem, especifique "7.2n" como O SKU quando criar o VM:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configure o VM de acordo com as suas necessidades. Se vai encriptar todas as unidades (OS + dados), as unidades de dados precisam de ser especificadas e montáveis a partir de /etc/fstab.

   > [!NOTE]
   > Use UUID=... para especificar unidades de dados em /etc/fstab em vez de especificar o nome do dispositivo de bloco (por exemplo, /dev/sdb1). Durante a encriptação, a ordem de unidades muda no VM. Se o seu VM depender de uma ordem específica de dispositivos de bloco, não os montará após a encriptação.

3. Assine as sessões de SSH.

4. Para encriptar o SISTEMA, especifique o volumeType como **All** ou **OS** quando ativa a encriptação.

   > [!NOTE]
   > Todos os processos de espaço-utilizador que não estejam a funcionar como `systemd` serviços devem ser mortos com um `SIGKILL`. Reiniciar o VM. Quando ativar a encriptação do disco OS num VM em execução, planeie o tempo de inatividade do VM.

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

## <a name="monitoring-os-encryption-progress"></a>Monitorização do progresso da encriptação do OS
Pode monitorizar o progresso da encriptação do OS de três formas:

* Utilize `Get-AzVmDiskEncryptionStatus` o cmdlet e inspecione o campo ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Depois de o VM chegar a "A encriptação do disco OS começou", leva cerca de 40 a 50 minutos num VM apoiado por armazenamento Premium.

  Por causa da [questão](https://github.com/Azure/WALinuxAgent/issues/388) #388 `OsVolumeEncrypted` `DataVolumesEncrypted` no WALinuxAgent, e aparecem como `Unknown` em algumas distribuições. Com a versão 2.1.5 do WALinuxAgent e posteriormente, este problema é corrigido automaticamente. Se vir `Unknown` na saída, pode verificar o estado de encriptação do disco utilizando o Explorador de Recursos Azure.

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

  No InstanceView, desça para ver o estado de encriptação das suas unidades.

  ![Visão de instância VM](./media/disk-encryption/vm-instanceview.png)

* Olhe para [os diagnósticos de botas.](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) As mensagens da extensão ADE devem ser pré-fixadas com `[AzureDiskEncryption]`.

* Inicie sessão no VM via SSH e obtenha o registo de extensão a partir de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskCryptonForLinux

  Recomendamos que não inscreva-se no VM enquanto a encriptação do OS está em andamento. Copie os registos apenas quando os outros dois métodos falharem.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Prepare um Linux VHD pré-encriptado
A preparação para VHDs pré-encriptados pode variar dependendo da distribuição. Exemplos sobre a preparação do Ubuntu 16, abre o SUSE 13.2 e o CentOS 7. 

### <a name="ubuntu-16"></a>Ubuntu 16
Configure a encriptação durante a instalação de distribuição fazendo os seguintes passos:

1. **Selecione Configure volumes encriptados** quando dividir os discos.

   ![Configuração Ubuntu 16.04 - Configure volumes encriptados](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de arranque separada, que não deve ser encriptada. Criptografe a sua unidade de raiz.

   ![Configuração Ubuntu 16.04 - Selecione dispositivos para encriptar](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase de passe. Esta é a frase que enviaste para o cofre da chave.

   ![Ubuntu 16.04 Configuração - Forneça a frase de passe](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Termine a divisão.

   ![Ubuntu 16.04 Configuração - Terminar a partição](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando iniciar o VM e lhe pedir em palavra uma frase de passe, use a frase de passe que forneceu no passo 3.

   ![Ubuntu 16.04 Configuração - Forneça a palavra-passe na bota](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare o VM para o upload para O Azure utilizando [estas instruções](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não corra o último passo (desprovisionando o VM) ainda.

Configure a encriptação para trabalhar com o Azure fazendo os seguintes passos:

1. Crie um ficheiro em /usr/local/sbin/azure_crypt_key.sh, com o conteúdo no seguinte script. Preste atenção ao Nome do Ficheiro chave, porque é o nome de ficheiro de palavra-passe usado pelo Azure.

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

4. Adicione permissões executáveis ao script:
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
6. Corra `update-initramfs -u -k all` para atualizar os initramfs para fazer o `keyscript` efeito.

7. Agora pode desprovisioná-lo.

   ![Ubuntu 16.04 Configuração - atualização-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continue até ao próximo passo e carregue o seu VHD para O Azure.

### <a name="opensuse-132"></a>abreSUSE 13.2
Para configurar a encriptação durante a instalação de distribuição, faça os seguintes passos:
1. Quando dividir os discos, selecione **Encrypt Volume Group**e, em seguida, introduza uma palavra-passe. Esta é a senha que vais enviar para o teu cofre.

   ![aberturaSUSE 13.2 Configuração - Encrypt Volume Group](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Arranque o VM usando a sua palavra-passe.

   ![aberturaSUSE 13.2 Configuração - Forneça a palavra-passe na bota](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare o VM para o upload para O Azure seguindo as instruções em Preparar uma SLES ou abra uma [máquina virtual SUSE para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não corra o último passo (desprovisionando o VM) ainda.

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

3. Anexar a seguinte linha no início do ficheiro /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
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
4. Editar/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acomodá-lo para "# Open LUKS device":

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

6. Agora pode desfornecer o VM e enviar o seu VHD para o Azure.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 e RHEL 8.1

Para configurar a encriptação durante a instalação de distribuição, faça os seguintes passos:
1. Selecione **Encriptar os meus dados** quando estiver a dividir discos.

   ![CentOS 7 Configuração -Destino de instalação](./media/disk-encryption/centos-encrypt-fig1.png)

2. Certifique-se de que **o Encriptar** está selecionado para a partilha de raízes.

   ![Configuração CentOS 7 -Selecione encriptar para partição de raiz](./media/disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase de passe. Esta é a frase que vais enviar para o teu cofre.

   ![Configuração CentOS 7 - forneça a frase-passe](./media/disk-encryption/centos-encrypt-fig3.png)

4. Quando iniciar o VM e lhe pedir em palavra uma frase de passe, use a frase de passe que forneceu no passo 3.

   ![Configuração CentOS 7 - Introduza a palavra-passe no bootup](./media/disk-encryption/centos-encrypt-fig4.png)

5. Prepare o VM para o upload para O Azure utilizando as instruções "CentOS 7.0+" em [Preparar uma máquina virtual baseada em CentOS para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não corra o último passo (desprovisionando o VM) ainda.

6. Agora pode desfornecer o VM e enviar o seu VHD para o Azure.

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

3. Anexar a seguinte linha no início do ficheiro /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
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
4. Editar/usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e anexar o seguinte após o "# Dispositivo LUKS aberto":
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

    ![Configuração CentOS 7 - executar /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Faça upload de VHD encriptado para uma conta de armazenamento Azure
Depois de ativada a encriptação DM-Crypt, o VHD encriptado local precisa de ser enviado para a sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Faça upload do segredo para o VM pré-encriptado para o seu cofre chave
Ao encriptar usando uma aplicação Azure AD (versão anterior), o segredo de encriptação de discos que obteve anteriormente deve ser carregado como um segredo no seu cofre de chaves. O cofre chave precisa de ter encriptação em disco e permissões ativadas para o seu cliente Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de encriptação de disco não encriptado com um KEK
Para configurar o segredo no seu cofre de chaves, use [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase de passe é codificada como uma corda base64 e depois enviada para o cofre da chave. Além disso, certifique-se de que as seguintes etiquetas são definidas quando criar o segredo no cofre da chave.

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


Utilize `$secretUrl` o passo seguinte para [fixar o disco OS sem utilizar](#without-using-a-kek)o KEK .

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de encriptação de disco encriptado com um KEK
Antes de enviar o segredo para o cofre da chave, pode encriptar opcionalmente utilizando uma chave de encriptação. Utilize a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de embrulho para encriptar primeiro o segredo utilizando a chave de encriptação da chave. A saída desta operação de embrulho é uma cadeia codificada por URL base64, que pode então carregar como segredo utilizando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

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

Utilize `$KeyEncryptionKey` `$secretUrl` e no próximo passo para [a fixação do disco OS utilizando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especifique um URL secreto quando anexar um disco OS

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto está a prender o disco de `$secretUrl`solo, tem de passar. O URL foi gerado na secção "Segredo de encriptação de disco não encriptado com um KEK".
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
Quando anexar o disco `$KeyEncryptionKey` `$secretUrl`OS, passe e . O URL foi gerado na secção "Segredo de encriptação do disco encriptado com um KEK".
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
