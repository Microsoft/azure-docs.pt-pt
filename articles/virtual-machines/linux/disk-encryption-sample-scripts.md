---
title: Azure Disk Encryption scripts de exemplo
description: Este artigo é o apêndice para Microsoft Azure a criptografia de disco para VMs do Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b034bad8661e93cbf5797c93739f1db3a64626f0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748907"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption scripts de exemplo 

Este artigo fornece scripts de exemplo para a preparação de VHDs e outras tarefas criptografados previamente.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Scripts do PowerShell de exemplo para Azure Disk Encryption 

- **Listar todas as VMs criptografadas em sua assinatura**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Listar todos os segredos de criptografia de disco usados para criptografar VMs em um cofre de chaves** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Usando o script do PowerShell Azure Disk Encryption pré-requisitos
Se você já estiver familiarizado com os pré-requisitos para Azure Disk Encryption, poderá usar o script do [PowerShell Azure Disk Encryption pré-requisitos](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter um exemplo de como usar esse script do PowerShell, consulte o guia de [início rápido criptografar uma VM](disk-encryption-powershell-quickstart.md). Você pode remover os comentários de uma seção do script, começando na linha 211, para criptografar todos os discos para VMs existentes em um grupo de recursos existente. 

A tabela a seguir mostra quais parâmetros podem ser usados no script do PowerShell: 


|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos ao qual o keyvault pertence.  Um novo grupo de recursos com esse nome será criado se um não existir.| Verdadeiro|
|$keyVaultName|Nome do keyvault no qual as chaves de criptografia devem ser colocadas. Um novo cofre com esse nome será criado se um não existir.| Verdadeiro|
|$location|Local do keyvault. Verifique se o keyvault e as VMs a serem criptografadas estão no mesmo local. Obtenha uma lista de localizações com `Get-AzLocation`.|Verdadeiro|
|$subscriptionId|Identificador da assinatura do Azure a ser usado.  Pode obter o seu ID de subscrição com `Get-AzSubscription`.|Verdadeiro|
|$aadAppName|Nome do aplicativo do Azure AD que será usado para gravar segredos no keyvault. Será criada uma nova aplicação com este nome, caso ainda não exista. Se esse aplicativo já existir, passe o parâmetro aadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo do cliente do aplicativo do Azure AD que foi criado anteriormente.|Falso|
|$keyEncryptionKeyName|Nome da chave de criptografia de chave opcional no keyvault. Uma nova chave com esse nome será criada se não existir uma.|Falso|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografar ou descriptografar VMs sem um aplicativo do Azure AD

- [Habilitar a criptografia de disco em uma VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Desabilitar a criptografia em uma VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - A desabilitação da criptografia só é permitida em volumes de dados para VMs do Linux.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografar ou descriptografar VMs com um aplicativo do Azure AD (versão anterior) 
 
- [Habilitar a criptografia de disco em uma VM Linux existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Desabilitar a criptografia em uma VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - A desabilitação da criptografia só é permitida em volumes de dados para VMs do Linux. 


- [Criar um novo disco gerenciado criptografado de um blob de armazenamento/VHD previamente criptografado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um novo disco gerenciado criptografado que fornecia um VHD previamente criptografado e suas configurações de criptografia correspondentes





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Criptografando uma unidade do sistema operacional em uma VM Linux em execução

### <a name="prerequisites-for-os-disk-encryption"></a>Pré-requisitos para a criptografia de disco do so

* A VM deve usar uma distribuição compatível com a criptografia de disco do sistema operacional, conforme listado no [Azure Disk Encryption sistemas operacionais com suporte](disk-encryption-overview.md#supported-vm-sizes) 
* A VM deve ser criada a partir da imagem do Marketplace no Azure Resource Manager.
* A VM do Azure com pelo menos 4 GB de RAM (o tamanho recomendado é de 7 GB).
* (Para RHEL e CentOS) Desabilite o SELinux. Para desabilitar o SELinux, consulte "4.4.2. Desabilitando o SELinux "no [Guia do usuário e do administrador do SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na VM.
* Depois de desabilitar o SELinux, reinicialize a VM pelo menos uma vez.

### <a name="steps"></a>Passos
1. Crie uma VM usando uma das distribuições especificadas anteriormente.

   Para o CentOS 7,2, a criptografia de disco do so tem suporte por meio de uma imagem especial. Para usar essa imagem, especifique "7.2 n" como a SKU ao criar a VM:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Configure a VM de acordo com suas necessidades. Se você for criptografar todas as unidades (sistema operacional + dados), as unidades de dados precisarão ser especificadas e poderão ser montadas em/etc/fstab.

   > [!NOTE]
   > Usar UUID =... para especificar unidades de dados em/etc/fstab em vez de especificar o nome do dispositivo de bloqueio (por exemplo,/dev/sdb1). Durante a criptografia, a ordem das unidades é alterada na VM. Se sua VM depender de uma ordem específica de dispositivos de bloco, ela não conseguirá montá-los após a criptografia.

3. Saia das sessões SSH.

4. Para criptografar o sistema operacional, especifique o volumetype como **todos** ou o **sistema operacional** ao habilitar a criptografia.

   > [!NOTE]
   > Todos os processos de espaço de usuário que não estão sendo executados como `systemd` serviços devem ser encerrados com um `SIGKILL`. Reinicialize a VM. Ao habilitar a criptografia de disco do sistema operacional em uma VM em execução, planeje o tempo de inatividade da VM.

5. Monitore periodicamente o progresso da criptografia usando as instruções na [próxima seção](#monitoring-os-encryption-progress).

6. Depois que o Get-AzVmDiskEncryptionStatus Mostrar "VMRestartPending", reinicie a VM conectando-se a ela ou usando o portal, o PowerShell ou a CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Antes de reinicializar, recomendamos que você salve o [diagnóstico de inicialização](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) da VM.

## <a name="monitoring-os-encryption-progress"></a>Progresso da criptografia do sistema operacional de monitoramento
Você pode monitorar o progresso de criptografia do sistema operacional de três maneiras:

* Use o cmdlet `Get-AzVmDiskEncryptionStatus` e inspecione o campo ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Depois que a VM atingir "criptografia de disco do so iniciada", levará cerca de 40 a 50 minutos em uma VM com suporte do armazenamento Premium.

  Devido ao [problema #388](https://github.com/Azure/WALinuxAgent/issues/388) em WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` aparecem como `Unknown` em algumas distribuições. Com o WALinuxAgent versão 2.1.5 e posterior, esse problema é corrigido automaticamente. Se você vir `Unknown` na saída, poderá verificar o status da criptografia de disco usando o Azure Resource Explorer.

  Vá para [Azure Resource Explorer](https://resources.azure.com/)e, em seguida, expanda essa hierarquia no painel de seleção à esquerda:

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

  No InstanceView, role para baixo para ver o status de criptografia de suas unidades.

  ![Exibição de instância de VM](./media/disk-encryption/vm-instanceview.png)

* Examine o [diagnóstico de inicialização](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). As mensagens da extensão ADE devem ser prefixadas com `[AzureDiskEncryption]`.

* Entre na VM via SSH e obtenha o log de extensão de:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Recomendamos que você não entre na VM enquanto a criptografia do sistema operacional estiver em andamento. Copie os logs somente quando os outros dois métodos falharem.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Preparar um VHD do Linux previamente criptografado
A preparação para VHDs previamente criptografados pode variar dependendo da distribuição. Exemplos de preparação do Ubuntu 16, openSUSE 13,2 e CentOS 7 estão disponíveis. 

### <a name="ubuntu-16"></a>Ubuntu 16
Configure a criptografia durante a instalação da distribuição executando as seguintes etapas:

1. Selecione **configurar volumes criptografados** ao particionar os discos.

   ![Instalação do Ubuntu 16, 4 – configurar volumes criptografados](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de inicialização separada, que não deve ser criptografada. Criptografe a unidade raiz.

   ![Instalação do Ubuntu 16, 4 – selecionar dispositivos para criptografar](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Forneça uma frase secreta. Essa é a frase secreta que você carregou no cofre de chaves.

   ![Instalação do Ubuntu 16, 4 – fornecer senha](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Conclua o particionamento.

   ![Instalação do Ubuntu 16, 4 – concluir o particionamento](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Quando você inicializar a VM e for solicitado uma frase secreta, use a frase secreta que você forneceu na etapa 3.

   ![Instalação do Ubuntu 16, 4 – fornecer senha na inicialização](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Prepare a VM para carregar no Azure usando [estas instruções](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não execute a última etapa (Desprovisionando a VM) ainda.

Configure a criptografia para trabalhar com o Azure executando as seguintes etapas:

1. Crie um arquivo em/usr/local/sbin/azure_crypt_key. sh, com o conteúdo no script a seguir. Preste atenção ao DefaultFilename, pois ele é o nome do arquivo de senha usado pelo Azure.

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

2. Altere a configuração cript em */etc/crypttab*. Deve ter o seguinte aspeto:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Adicione permissões executáveis ao script:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Edite */etc/initramfs-tools/modules* acrescentando linhas:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Execute `update-initramfs -u -k all` para atualizar o initramfs para fazer com que os `keyscript` entrem em vigor.

7. Agora você pode desprovisionar a VM.

   ![Instalação do Ubuntu 16, 4 – update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Continue na próxima etapa e carregue o VHD no Azure.

### <a name="opensuse-132"></a>openSUSE 13,2
Para configurar a criptografia durante a instalação da distribuição, execute as seguintes etapas:
1. Ao particionar os discos, selecione **criptografar grupo de volumes**e, em seguida, insira uma senha. Essa é a senha que você carregará em seu cofre de chaves.

   ![Instalação do openSUSE 13,2 – criptografar grupo de volumes](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Inicialize a VM usando sua senha.

   ![Instalação do openSUSE 13,2 – fornecer senha na inicialização](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Prepare a VM para carregar no Azure seguindo as instruções em [preparar uma máquina virtual SLES ou openSUSE para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não execute a última etapa (Desprovisionando a VM) ainda.

Para configurar a criptografia para funcionar com o Azure, execute as seguintes etapas:
1. Edite o/etc/Dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Comente essas linhas até o final do arquivo/usr/lib/Dracut/modules.d/90crypt/Module-setup.sh:
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

3. Acrescente a seguinte linha no início do arquivo/usr/lib/Dracut/modules.d/90crypt/Parse-Crypt.sh:
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
4. Edite/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh e acrescente-o a "# Open LUKS Device":

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
5. Execute `/usr/sbin/dracut -f -v` para atualizar a initrd.

6. Agora você pode desprovisionar a VM e carregar seu VHD no Azure.

### <a name="centos-7"></a>CentOS 7
Para configurar a criptografia durante a instalação da distribuição, execute as seguintes etapas:
1. Selecione **criptografar meus dados** ao particionar discos.

   ![Instalação do CentOS 7-destino da instalação](./media/disk-encryption/centos-encrypt-fig1.png)

2. Verifique se a **criptografia** está selecionada para a partição raiz.

   ![Instalação do CentOS 7 – Selecione criptografar para a partição raiz](./media/disk-encryption/centos-encrypt-fig2.png)

3. Forneça uma frase secreta. Essa é a frase secreta que você carregará em seu cofre de chaves.

   ![Instalação do CentOS 7 – fornecer senha](./media/disk-encryption/centos-encrypt-fig3.png)

4. Quando você inicializar a VM e for solicitado uma frase secreta, use a frase secreta que você forneceu na etapa 3.

   ![Instalação do CentOS 7 – Insira a senha na inicialização](./media/disk-encryption/centos-encrypt-fig4.png)

5. Prepare a VM para carregar no Azure usando as instruções "CentOS 7.0 +" em [preparar uma máquina virtual baseada em CentOS para o Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não execute a última etapa (Desprovisionando a VM) ainda.

6. Agora você pode desprovisionar a VM e carregar seu VHD no Azure.

Para configurar a criptografia para funcionar com o Azure, execute as seguintes etapas:

1. Edite o/etc/Dracut.conf e adicione a seguinte linha:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Comente essas linhas até o final do arquivo/usr/lib/Dracut/modules.d/90crypt/Module-setup.sh:
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

3. Acrescente a seguinte linha no início do arquivo/usr/lib/Dracut/modules.d/90crypt/Parse-Crypt.sh:
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
4. Edite/usr/lib/Dracut/modules.d/90crypt/cryptroot-Ask.sh e acrescente o seguinte após o "# Open LUKS Device":
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
5. Execute o "/usr/sbin/Dracut-f-v" para atualizar o initrd.

    ![Instalação do CentOS 7 – executar/usr/sbin/Dracut-f-v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carregar o VHD criptografado em uma conta de armazenamento do Azure
Depois que a criptografia DM-cript estiver habilitada, o VHD criptografado local precisará ser carregado em sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Carregar o segredo para a VM previamente criptografada para o cofre de chaves
Ao criptografar usando um aplicativo do Azure AD (versão anterior), o segredo de criptografia de disco que você obteve anteriormente deve ser carregado como um segredo no cofre de chaves. O cofre de chaves precisa ter a criptografia de disco e permissões habilitadas para o cliente do Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de criptografia de disco não criptografado com um KEK
Para configurar o segredo em seu cofre de chaves, use [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase secreta é codificada como uma cadeia de caracteres Base64 e, em seguida, carregada no cofre de chaves. Além disso, certifique-se de que as seguintes marcas sejam definidas quando você criar o segredo no cofre de chaves.

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


Use o `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional sem usar o Kek](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de criptografia de disco criptografado com um KEK
Antes de carregar o segredo para o cofre de chaves, você pode, opcionalmente, criptografá-lo usando uma chave de criptografia de chave. Use a [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) de encapsulamento para primeiro criptografar o segredo usando a chave de criptografia de chave. A saída dessa operação de encapsulamento é uma cadeia de caracteres codificada de URL base64, que você pode carregar como um segredo usando o cmdlet [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

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

Use `$KeyEncryptionKey` e `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional usando Kek](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especificar uma URL secreta ao anexar um disco do sistema operacional

###  <a name="without-using-a-kek"></a>Sem usar um KEK
Enquanto estiver anexando o disco do sistema operacional, você precisa passar `$secretUrl`. A URL foi gerada na seção "segredo de criptografia de disco não criptografado com um KEK".
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
Ao anexar o disco do sistema operacional, passe `$KeyEncryptionKey` e `$secretUrl`. A URL foi gerada na seção "segredo de criptografia de disco criptografado com um KEK".
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
