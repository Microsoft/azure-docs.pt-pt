---
title: Encriptação do disco Azure com Azure AD App Linux IaaS VMs (versão anterior)
description: Este artigo fornece instruções para permitir a encriptação do disco do Microsoft Azure para Os VMs Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: d1607ef4ff277f9c9cdb55db3e58da1052a00756
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558407"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Ativar encriptação do disco Azure com Azure AD em VMs Linux (versão anterior)

A nova versão da Encriptação do Disco Azure elimina o requisito para fornecer um parâmetro de aplicação Azure Ative Directory (Azure AD) para permitir a encriptação do disco VM. Com a nova versão, já não é obrigado a fornecer credenciais AZure AD durante o passo de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação AD Azure utilizando a nova versão. Para obter instruções sobre como ativar a encriptação do disco VM utilizando a nova versão, consulte [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md). Os VMs que já estavam encriptados com os parâmetros da aplicação AD Azure ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.

Pode ativar muitos cenários de encriptação de discos, e os passos podem variar de acordo com o cenário. As seguintes secções cobrem os cenários em maior detalhe para a infraestrutura Linux como um VMs de serviço (IaaS). Só é possível aplicar encriptação de discos a máquinas virtuais de [tamanhos e sistemas operativos VM suportados.](disk-encryption-overview.md#supported-vms-and-operating-systems) Deve também cumprir os seguintes pré-requisitos:

- [Requisitos adicionais para os VM](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Política de Networking e Grupo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Tire uma [foto,](snapshot-copy-managed-disk.md)faça uma cópia de segurança, ou ambos antes de encriptar os discos. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Depois de ser feita uma cópia de segurança, pode utilizar o Set-AzVMDiskEncryptionExtension cmdlet para encriptar discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs encriptados, consulte [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se utilizou previamente [a Encriptação do Disco Azure com a aplicação AD Azure](disk-encryption-overview-aad.md) para encriptar este VM, deve continuar a utilizar esta opção para encriptar o seu VM. Não é possível utilizar [a Encriptação do Disco Azure](disk-encryption-overview.md) neste VM encriptado porque este não é um cenário suportado, o que significa que mudar a aplicação AD AZure para este VM encriptado ainda não está suportado.
 > - Para garantir que os segredos de encriptação não cruzam fronteiras regionais, a Encriptação do Disco Azure precisa do cofre-chave e os VMs para serem co-localizados na mesma região. Crie e use um cofre chave que esteja na mesma região que o VM para ser encriptado.
 > - Quando encripta os volumes do Linux OS, o processo pode demorar algumas horas. É normal que os volumes de SISTEMA Linux levem mais tempo do que os volumes de dados para encriptar.
> - Quando encriptar os volumes do Linux OS, o VM deve ser considerado indisponível. Recomendamos vivamente que evite logins SSH enquanto a encriptação está em andamento para evitar bloquear quaisquer ficheiros abertos que precisem de ser acedidos durante o processo de encriptação. Para verificar o progresso, utilize os [comandos Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou [vm encryption show.](/cli/azure/vm/encryption#az-vm-encryption-show) Pode esperar que este processo despente algumas horas para um volume de SO de 30 GB, mais tempo adicional para encriptar volumes de dados. O tempo de encriptação do volume de dados é proporcional ao tamanho e quantidade dos volumes de dados, a menos que o **formato de encriptação seja** utilizado. 
 > - A encriptação incapacitante nos VMs Linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de SO se o volume de SO tiver sido encriptado. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"></a> Ativar a encriptação num IaaS Linux VM existente ou em execução

Neste cenário, pode ativar a encriptação utilizando o modelo Azure Resource Manager, os cmdlets PowerShell ou os comandos Azure CLI. 

>[!IMPORTANT]
 >É obrigatório tirar uma fotografia ou fazer uma essão de apoio a uma instância VM gerida baseada em discos fora e antes de permitir a encriptação do disco Azure. Pode tirar uma fotografia do disco gerido a partir do portal Azure ou utilizar a Cópia de [Segurança Azure](../../backup/backup-azure-vms-encryption.md). As cópias de segurança garantem que uma opção de recuperação é possível em caso de falha inesperada durante a encriptação. Depois de ser feita uma cópia de segurança, utilize o Set-AzVMDiskEncryptionExtension cmdlet para encriptar os discos geridos especificando o parâmetro -skipVmBackup. O comando Set-AzVMDiskEncryptionExtension falha contra VMs geridos baseados em disco até que uma cópia de segurança seja feita e este parâmetro seja especificado. 
>
>Encriptar ou desativar a encriptação pode fazer com que o VM reinicie. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Ativar a encriptação num VM Linux existente ou em execução utilizando o CLI Azure 
Pode ativar a encriptação do disco no seu VHD encriptado instalando e utilizando a ferramenta de linha de comando [Azure CLI 2.0.](/cli/azure) Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para permitir a encriptação nos VMs IaS Linux existentes ou em execução em Azure, utilize os seguintes comandos CLI:

Utilize a [encriptação az vm ativar](/cli/azure/vm/encryption#az-vm-encryption-enable) o comando para ativar a encriptação numa máquina virtual IaaS em execução em Azure.

-  **Criptografe um VM em execução utilizando um segredo de cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografe um VM em execução utilizando kek para embrulhar o segredo do cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A sintaxe para o valor do parâmetro de chave-chave de encriptação do disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um IaaS VM, utilize o comando [do programa de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) A encriptação incapacitante só é permitida em volumes de dados para Os VMs Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"></a> Ativar a encriptação num VM Linux existente ou em execução utilizando o PowerShell
Utilize o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para ativar a encriptação numa máquina virtual IaaS em funcionamento em Azure. Tire uma [foto](snapshot-copy-managed-disk.md) ou faça uma cópia de segurança do VM com [a Azure Backup](../../backup/backup-azure-vms-encryption.md) antes que os discos sejam encriptados. O parâmetro -skipVmBackup já está especificado nos scripts PowerShell para encriptar um Linux VM em execução.

- **Criptografe um VM em execução utilizando um segredo de cliente:** O seguinte script inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM, cofre-chave, app Azure AD e segredo de clientes já deveriam ter sido criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret pelos seus valores. Modifique o parâmetro -VolumeType para especificar quais os discos que está a encriptar.

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Criptografe um VM em execução utilizando kek para embrulhar o segredo do cliente:** A encriptação do disco Azure permite especificar uma chave existente no cofre para embrulhar segredos de encriptação de discos que foram gerados enquanto permite a encriptação. Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para o cofre chave. Modifique o parâmetro -VolumeType para especificar quais os discos que está a encriptar. 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > A sintaxe para o valor do parâmetro de chave-chave de encriptação do disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/grupos de recursos/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um IaaS VM, utilize o [cmdlet Get-AzVmDiskEncrypationStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) A encriptação incapacitante só é permitida em volumes de dados para Os VMs Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"></a> Ativar a encriptação em um IaaS Linux VM existente ou em execução com um modelo

Pode ativar a encriptação do disco num IaaS Linux VM existente ou em execução em Azure utilizando o [modelo de Gestor de Recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selecione **Implementar para Azure** no modelo de arranque rápido Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Selecione **Criar** para ativar a encriptação no IaaS VM existente ou em execução.

A tabela que se segue lista os parâmetros do modelo do Gestor de Recursos para VMs existentes ou em execução que utilizam um ID do cliente Azure:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | Identificação do cliente da aplicação AD Azure que tem permissões para escrever segredos para o cofre chave. |
| AADClientSecret | Segredo de cliente da aplicação AZure AD que tem permissões para escrever segredos para o seu cofre chave. |
| keyVaultName | Nome do cofre chave para o que a chave deve ser carregada. Pode obtê-lo utilizando o comando Azure CLI `az keyvault show --name "MySecureVault" --query KVresourceGroup` . |
|  keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave gerada. Este parâmetro é opcional se selecionar **nokek** na lista de drop-down **UseExistingKek.** Se selecionar **kek** na lista de drop-down **UseExistingKek,** deve introduzir o valor _KeyEncryptionKeyURL._ |
| volumeType | Tipo de volume em que a operação de encriptação é executada. Valores suportados válidos são _OS_ ou _Todos_. (Ver distribuições de Linux suportadas e suas versões para OS e discos de dados na secção pré-requisitos anteriormente.) |
| sequênciaVersão | Versão de sequência da operação BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada no mesmo VM. |
| vmName | Nome do VM em que a operação de encriptação deve ser executada. |
| frase de acesso | Digite uma frase-passe forte como a chave de encriptação de dados. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Utilize a funcionalidade EncryptFormatAll para discos de dados em VMs Linux Iaa
O parâmetro EncryptFormatAll reduz o tempo para os discos de dados Do Linux serem encriptados. As divisórias que satisfaçam determinados critérios são formatadas (com o seu sistema de ficheiros atual). Depois, voltam para onde estavam antes da execução do comando. Se pretender excluir um disco de dados que satisfaça os critérios, pode desmontá-lo antes de executar o comando.

 Depois de executar este comando, quaisquer unidades que foram montadas anteriormente são formatadas. Em seguida, a camada de encriptação começa em cima da unidade agora vazia. Quando esta opção é selecionada, o disco temporário ligado ao VM também é encriptado. Se a unidade efémera for reiniciada, é reformatada e reencri encriptada para o VM pela solução de encriptação do disco Azure na próxima oportunidade.

>[!WARNING]
> O EncryptFormatAll não deve ser utilizado quando há dados necessários nos volumes de dados de um VM. Pode excluir os discos da encriptação desmontando-os. Experimente primeiro o parâmetro EncryptFormatAll num VM de teste para compreender primeiro o parâmetro de funcionalidade e a sua implicação antes de o experimentar no VM de produção. A opção EncryptFormatAll forma o disco de dados, pelo que todos os dados nele serão perdidos. Antes de prosseguir, verifique se quaisquer discos que pretenda excluir estão devidamente desmontados. </br></br>
 >Se definir este parâmetro enquanto atualiza as definições de encriptação, pode levar a um reboot antes da encriptação real. Neste caso, também pretende remover o disco que não pretende formatado do ficheiro fstab. Da mesma forma, deve adicionar a partição que pretende ter formatada encriptada no ficheiro fstab antes de iniciar a operação de encriptação. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"></a> EncriptaçãoTete todos os critérios
O parâmetro passa por todas as divisórias e encripta-as desde que cumpram *todos os* seguintes critérios: 
- Não é uma divisória raiz/OS/boot
- Ainda não está encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"></a> Use o parâmetro EncryptFormatAll com um modelo
Para utilizar a opção EncryptFormatAll, utilize qualquer modelo de Gestor de Recursos Azure pré-existente que criptografe um VM Linux e altere o campo **de EncriptaçãoOperação** para o recurso AzureDiskEncryption.

1. Como exemplo, utilize o [modelo de Gestor de Recursos para encriptar um Linux IaaS VM em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selecione **Implementar para Azure** no modelo de arranque rápido Azure.
3. Alterar o campo **de encriptaçãoOperação** a partir de **EnableEncryption** to **EnableEncryptionFormatAl**.
4. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, outros parâmetros, termos legais e acordo. Selecione **Criar** para ativar a encriptação no IaaS VM existente ou em execução.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"></a> Utilize o parâmetro EncryptFormatAll com um cmdlet PowerShell
Utilize o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) com o parâmetro EncryptFormatAll.

**Criptografe um VM em execução utilizando um segredo de cliente e EncryptFormatAll:** Como exemplo, o seguinte script inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet com o parâmetro EncryptFormatAll. O grupo de recursos, VM, cofre-chave, app Azure AD e segredo de clientes já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret pelos seus valores.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"></a> Utilize o parâmetro EncryptFormatAll com o Gestor de Volume Lógico (LVM) 
Recomendamos uma configuração LVM-on-crypt. Para todos os exemplos a seguir, substitua o caminho do dispositivo e os pontos de montagem por qualquer que seja a sua utilização. Esta configuração pode ser feita da seguinte forma:

- Adicione os discos de dados que compõem o VM.
- Formato, montagem e adicione estes discos ao ficheiro fstab.

    1. Formate o disco recém-adicionado. Usamos symlinks gerados pelo Azure aqui. A utilização de symlinks evita problemas relacionados com a alteração de nomes de dispositivos. Para obter mais informações, consulte [problemas de nomes de dispositivos de resolução de problemas](../troubleshooting/troubleshoot-device-names-problems.md).
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. Monte os discos.

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. Adicione ao fstab.

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. Executar o Set-AzVMDiskEncryptionExtension PowerShell cmdlet com -EncryptFormatAll para encriptar estes discos.

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. Instale o LVM em cima destes novos discos. Note que as unidades encriptadas são desbloqueadas depois de o VM ter terminado o arranque. Assim, a montagem de LVM também terá de ser posteriormente adiada.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"></a> Novos VMs iaas criados a partir de VHD encriptados pelo cliente e chaves de encriptação
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. As seguintes secções explicam com maior detalhe o modelo de Gestor de Recursos e comandos CLI. 

Utilize as instruções no apêndice para preparar imagens pré-encriptadas que podem ser utilizadas em Azure. Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.

* [Preparar um Linux VHD pré-encriptado](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >É obrigatório tirar uma fotografia ou fazer uma essão de apoio a uma instância VM gerida baseada em discos fora e antes de permitir a encriptação do disco Azure. Pode tirar uma fotografia do disco gerido a partir do portal ou utilizar a Cópia de [Segurança Azure](../../backup/backup-azure-vms-encryption.md). As cópias de segurança garantem que uma opção de recuperação é possível em caso de falha inesperada durante a encriptação. Depois de ser feita uma cópia de segurança, utilize o Set-AzVMDiskEncryptionExtension cmdlet para encriptar os discos geridos especificando o parâmetro -skipVmBackup. O comando Set-AzVMDiskEncryptionExtension falha contra VMs geridos baseados em disco até que uma cópia de segurança seja feita e este parâmetro seja especificado. 
>
>Encriptar ou desativar a encriptação pode fazer com que o VM reinicie.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"></a> Use Azure PowerShell para encriptar IaaS VMs com VHDs pré-encriptados 
Pode ativar a encriptação do disco no seu VHD encriptado utilizando o [Conjunto de Cmdlet PowerShell Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado
Pode adicionar um novo disco de dados utilizando [o discope Az vm](add-disk.md) ou [através do portal Azure](attach-disk-portal.md). Antes de conseguir encriptar, primeiro tem de montar o disco de dados recentemente anexado. Tem de solicitar a encriptação da unidade de dados porque a unidade será inutilizável enquanto a encriptação estiver em curso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com o Azure CLI
 Se o VM foi previamente encriptado com "All", então o parâmetro do tipo volume deve permanecer All. Tudo inclui os discos de SO e de dados. Se o VM foi previamente encriptado com um tipo de volume de "SO", então o parâmetro do tipo volume deve ser alterado para All de modo que tanto o SISTEMA como o novo disco de dados sejam incluídos. Se o VM foi encriptado apenas com o tipo de volume de "Dados", então pode permanecer dados como demonstrado aqui. Adicionar e anexar um novo disco de dados a um VM não é preparação suficiente para encriptação. O disco recém-ligado também deve ser formatado e montado corretamente dentro do VM antes de permitir a encriptação. No Linux, o disco deve ser montado em /etc/fstab com um [nome persistente do dispositivo de bloqueio](../troubleshooting/troubleshoot-device-names-problems.md). 

Em contraste com a sintaxe PowerShell, o CLI não requer que forneça uma versão de sequência única quando ativa a encriptação. O CLI gera e utiliza automaticamente o seu próprio valor exclusivo de versão de sequência.

-  **Criptografe um VM em execução utilizando um segredo de cliente:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe um VM em execução utilizando kek para embrulhar o segredo do cliente:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Quando utilizar o PowerShell para encriptar um novo disco para o Linux, é necessário especificar uma nova versão de sequência. A versão da sequência tem de ser única. O seguinte script gera um GUID para a versão de sequência. 
 

-  **Criptografe um VM em execução utilizando um segredo de cliente:** O seguinte script inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM, cofre-chave, app Azure AD e segredo de clientes já deveriam ter sido criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e My-AAD-client-secret pelos seus valores. O parâmetro -VolumeType é definido para discos de dados e não para o disco DE. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para All de modo que tanto o SISTEMA como o novo disco de dados serão incluídos.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup'; 
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Criptografe um VM em execução utilizando kek para embrulhar o segredo do cliente:** A encriptação do disco Azure permite especificar uma chave existente no cofre para embrulhar segredos de encriptação de discos que foram gerados enquanto permite a encriptação. Quando uma chave de encriptação chave é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para o cofre chave. O parâmetro -VolumeType é definido para discos de dados e não para o disco DE. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para All de modo que tanto o SISTEMA como o novo disco de dados serão incluídos.

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $vmName = 'MyExtraSecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> A sintaxe para o valor do parâmetro de chave-chave de encriptação do disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Desativar encriptação para Os VMs Linux
Pode desativar a encriptação utilizando o Azure PowerShell, o Azure CLI ou um modelo de Gestor de Recursos. 

>[!IMPORTANT]
>A encriptação incapacitante com encriptação de disco Azure em VMs Linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de SO se o volume de SO tiver sido encriptado. 

- **Desative a encriptação do disco com a Azure PowerShell:** Para desativar a encriptação, utilize o cmdlet [de desencriptação Disable-AzureRmVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo de Gestor de Recursos:** Para desativar a encriptação, utilize a [encriptação desativação num modelo linux VM em execução.](https://aka.ms/decrypt-linuxvm)
     1. Selecione **Implementar para Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, termos legais e acordo.
     3. Selecione **Comprar** para desativar a encriptação do disco num VM do Windows em execução. 


## <a name="next-steps"></a>Passos seguintes

- [Encriptação do disco Azure para visão geral do Linux](disk-encryption-overview-aad.md)
- [Criar e configurar um cofre chave para encriptação de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
