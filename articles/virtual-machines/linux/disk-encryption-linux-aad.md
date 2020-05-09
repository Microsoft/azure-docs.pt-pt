---
title: Encriptação de disco azure com Aplicação AD Azure Linux IaaS VMs (versão anterior)
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs Linux IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2ce3afb533aa33b88b15510eacc88c0884811cc6
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792603"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Ativar encriptação de disco Azure com AD Azure em VMs Linux (versão anterior)

O novo lançamento da Encriptação do Disco Azure elimina a exigência de fornecer um parâmetro de aplicação Azure Ative Directory (Azure AD) para permitir a encriptação do disco VM. Com o novo lançamento, já não é obrigado a fornecer credenciais de AD Azure durante a fase de encriptação ativa. Todos os novos VMs devem ser encriptados sem os parâmetros da aplicação Azure AD utilizando o novo lançamento. Para obter instruções sobre como ativar a encriptação do disco VM utilizando o novo lançamento, consulte a [encriptação do disco Azure para O VMS do Linux](disk-encryption-linux.md). Os VMs que já estavam encriptados com parâmetros de aplicação Azure AD ainda são suportados e devem continuar a ser mantidos com a sintaxe AAD.

Pode ativar muitos cenários de encriptação de discos, e os passos podem variar de acordo com o cenário. As secções seguintes cobrem mais pormenorizadamente os cenários para a infraestrutura Linux como VMs de serviço (IaaS). Só é possível aplicar encriptação de disco a máquinas virtuais de [tamanhos vm suportados e sistemas operativos.](disk-encryption-overview.md#supported-vms-and-operating-systems) Deve também cumprir os seguintes pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Política de Networking e Grupo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Tire uma [fotografia,](snapshot-copy-managed-disk.md)faça uma cópia de segurança ou ambas antes de encriptar os discos. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Depois de ser feita uma cópia de segurança, pode utilizar o cmdlet set-AzVMDiskEncryptionExtension para encriptar discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs encriptados, consulte [O Backup Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se usou anteriormente a [Encriptação do Disco Azure com a aplicação Azure AD](disk-encryption-overview-aad.md) para encriptar este VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Não é possível utilizar [a Encriptação do Disco Azure](disk-encryption-overview.md) neste VM encriptado porque este não é um cenário suportado, o que significa que mudar a aplicação Azure AD para este VM encriptado ainda não é suportado.
 > - Para garantir que os segredos de encriptação não cruzam os limites regionais, a Encriptação do Disco Azure precisa do cofre chave e dos VMs para ser co-localizado na mesma região. Crie e use um cofre chave que esteja na mesma região que o VM para ser encriptado.
 > - Quando encripta os volumes linux OS, o processo pode demorar algumas horas. É normal que os volumes de Sistema sO o demorem mais tempo do que os volumes de dados a encriptar.
> - Quando encripta os volumes Linux OS, o VM deve ser considerado indisponível. Recomendamos vivamente que evite logins ssh enquanto a encriptação está em andamento para evitar bloquear quaisquer ficheiros abertos que precisem de ser acedidos durante o processo de encriptação. Para verificar o progresso, utilize os comandos [get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou [vm crypton.](/cli/azure/vm/encryption#az-vm-encryption-show) Pode esperar que este processo dere a pena algumas horas para um volume de 30 GB de OS, além de tempo adicional para encriptar volumes de dados. O tempo de encriptação do volume de dados é proporcional ao tamanho e quantidade dos volumes de dados, a menos que o **formato de encriptação seja utilizado.** 
 > - A encriptação incapacitante nos VMs linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de OS se o volume de S tiver sido encriptado. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Ativar a encriptação de um VM IaaS Linux existente ou em execução

Neste cenário, pode ativar a encriptação utilizando o modelo do Gestor de Recursos Azure, os comandos PowerShell ou Azure CLI. 

>[!IMPORTANT]
 >É obrigatório tirar uma fotografia ou fazer back-up de uma instância VM gerida fora e antes de permitir a encriptação do disco Azure. Pode tirar uma foto do disco gerido do portal Azure, ou pode utilizar o [Azure Backup](../../backup/backup-azure-vms-encryption.md). As cópias de segurança asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois de ser feita uma cópia de segurança, utilize o cmdlet set-AzVMDiskCryptonExtension para encriptar discos geridos especificando o parâmetro -skipVmBackup. O comando set-AzVMDiskCryptonExtension falha contra VMs geridos baseados em disco até que seja feita uma cópia de segurança e este parâmetro seja especificado. 
>
>Encriptar ou desativar encriptação pode fazer com que o VM reinicie. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Ativar a encriptação de um VM Linux existente ou em execução utilizando o Azure CLI 
Pode ativar a encriptação do disco no seu VHD encriptado instalando e utilizando a ferramenta de linha de comando [Azure CLI 2.0.](/cli/azure) Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para permitir a encriptação dos VMs IaaS Linux existentes ou em execução em Azure, utilize os seguintes comandos CLI:

Utilize o comando [de encriptação az vm para](/cli/azure/vm/encryption#az-vm-encryption-enable) ativar a encriptação numa máquina virtual IaaS em funcionamento em Azure.

-  **Criptografe um VM em execução utilizando um segredo de cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografe um VM em execução usando kek para embrulhar o segredo do cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A sintaxe para o valor do parâmetro do cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM IaaS, use o comando de [encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) A encriptação incapacitante só é permitida em volumes de dados para VMs Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Ativar a encriptação de um VM Linux existente ou em execução utilizando powerShell
Utilize o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para permitir a encriptação numa máquina virtual IaaS em funcionamento em Azure. Tire uma [fotografia](snapshot-copy-managed-disk.md) ou faça uma cópia de segurança do VM com [a Cópia de Segurança Azure](../../backup/backup-azure-vms-encryption.md) antes de os discos estarem encriptados. O parâmetro -skipVmBackup já está especificado nos scripts PowerShell para encriptar um VM Linux em execução.

- **Criptografe um VM em execução utilizando um segredo** de cliente: O seguinte script inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, cofre chave, app Azure AD e segredo de cliente já deveriam ter sido criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, myKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e o my-AAD-client-secret com os seus valores. Modifique o parâmetro -VolumeType para especificar quais os discos que está a encriptar.

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
- **Criptografe um VM em execução usando kek para embrulhar o segredo do cliente:** A Encriptação do Disco Azure permite especificar uma chave existente no seu cofre chave para embrulhar os segredos de encriptação do disco que foram gerados ao mesmo tempo que permite a encriptação. Quando uma chave de encriptação é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para o cofre da chave. Modifique o parâmetro -VolumeType para especificar quais os discos que está a encriptar. 

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
  > A sintaxe para o valor do parâmetro do cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM IaaS, utilize o [cmdlet Get-AzVmDiskCryptonStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzureRmVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) A encriptação incapacitante só é permitida em volumes de dados para VMs Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Ativar a encriptação num VM IaaS Linux existente ou em execução com um modelo

Pode ativar a encriptação do disco num VM IaaS Linux existente ou em execução em Azure utilizando o [modelo de Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)de Recursos .

1. Selecione **Implementar para Azure** no modelo de arranque rápido Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, termos legais e acordo. Selecione **Criar** para ativar a encriptação no VM IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Gestor de Recursos para VMs existentes ou em execução que utilizam um ID de cliente Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | Identificação do cliente da aplicação Azure AD que tem permissões para escrever segredos para o cofre chave. |
| AADClientSecret | Segredo do cliente da aplicação Azure AD que tem permissões para escrever segredos para o seu cofre chave. |
| keyVaultName | Nome do cofre chave para o que a chave deve ser enviada. Pode obtê-lo utilizando o `az keyvault show --name "MySecureVault" --query KVresourceGroup`comando Azure CLI . |
|  keyEncryptionKeyURL | URL da chave de encriptação que é usada para encriptar a chave gerada. Este parâmetro é opcional se selecionar **nokek** na lista de drop-down **UseExistingKek.** Se selecionar **kek** na lista de drop-down **UseExistingKek,** deve introduzir o valor _keyEncryptionKeyURL._ |
| volumeType | Tipo de volume em que a operação de encriptação é realizada. Os valores suportados válidos são _OS_ ou _All_. (Ver distribuições linux suportadas e as suas versões para OS e discos de dados na secção de pré-requisitos anteriores.) |
| sequênciaVersãoVersão | Versão de sequência da operação BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada no mesmo VM. |
| vmName | Nome do VM em que a operação de encriptação deve ser executada. |
| frase de acesso | Digite uma frase de passe forte como a chave de encriptação de dados. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Utilize a funcionalidade EncryptFormatAll para discos de dados em VMs Linux IaaS
O parâmetro EncryptFormatAll reduz o tempo para os discos de dados do Linux serem encriptados. As divisórias que satisfaçam determinados critérios são formatadas (com o seu sistema de ficheiros atual). Depois voltam a ser remontados ao sítio onde estavam antes da execução do comando. Se quiser excluir um disco de dados que satisfaça os critérios, pode desmontá-lo antes de executar o comando.

 Depois de executar este comando, quaisquer unidades que foram montadas anteriormente são formatadas. Em seguida, a camada de encriptação começa em cima da unidade agora vazia. Quando esta opção é selecionada, o disco temporário ligado ao VM também é encriptado. Se a unidade efémera for recriada, é reformada e reencriptada para o VM pela solução de encriptação do disco Azure na próxima oportunidade.

>[!WARNING]
> O EncryptFormatAll não deve ser utilizado quando existem dados necessários sobre os volumes de dados de um VM. Pode excluir discos da encriptação desmontando-os. Experimente o parâmetro EncryptFormatAll num VM de teste primeiro para compreender o parâmetro de funcionalidade e a sua implicação antes de o experimentar no VM de produção. A opção EncryptFormatAll formata o disco de dados, pelo que todos os dados nele serão perdidos. Antes de prosseguir, verifique se os discos que pretende excluir estão devidamente desmontados. </br></br>
 >Se definir este parâmetro enquanto atualiza as definições de encriptação, pode levar a um reboot antes da encriptação real. Neste caso, também pretende remover o disco que não quer formatado do ficheiro fstab. Da mesma forma, deve adicionar a partição que pretende encriptar formatado no ficheiro fstab antes de iniciar a operação de encriptação. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatTodos os critérios
O parâmetro passa por todas as divisórias e encripta-as desde que cumpram *todos os* seguintes critérios: 
- Não é uma partição raiz/OS/bota
- Não está já encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Criptografe os discos que compõem o volume RAID ou LVM em vez do volume RAID ou LVM.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Utilize o parâmetro EncryptFormatAll com um modelo
Para utilizar a opção EncryptFormatAll, utilize qualquer modelo de Gestor de Recursos Azure pré-existente que encripta um VM Linux e altere o campo **de encriptação Operation** para o recurso AzureDiskEncryption.

1. Como exemplo, utilize o modelo de Gestor de [Recursos para encriptar um VM Linux IaaS em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selecione **Implementar para Azure** no modelo de arranque rápido Azure.
3. Altere o campo de operação de **encriptação** de **EnableEncryption** para **EnableEncryptionFormatAl**.
4. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, outros parâmetros, termos legais e acordo. Selecione **Criar** para ativar a encriptação no VM IaaS existente ou em execução.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> Utilize o parâmetro EncryptFormatAll com um cmdlet PowerShell
Utilize o cmdlet de extensão de [encriptação Set-AzVMDisk](/powershell/module/az.compute/set-azvmdiskencryptionextension) Com o parâmetro EncryptFormatAll.

**Criptografe um VM em execução utilizando um segredo de cliente e EncryptFormatAll:** Como exemplo, o seguinte script inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, VM, cofre chave, app Azure AD e segredo de cliente já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, o MyVirtualMachineResourceGroup, o MySecureVM, o MySecureVault, o My-AAD-client-ID e o my-AAD-client-secret com os seus valores.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> Utilize o parâmetro EncryptFormatAll com Gestor de Volume Lógico (LVM) 
Recomendamos uma configuração LVM-on-cripta. Para todos os exemplos seguintes, substitua o caminho do dispositivo e os pontos de montagem por qualquer que seja o seu caso de utilização. Esta configuração pode ser feita da seguinte forma:

- Adicione os discos de dados que irão compor o VM.
- Formato, monte e adicione estes discos ao ficheiro fstab.

    1. Forforte o disco recém-adicionado. Usamos symlinks gerados pelo Azure aqui. A utilização de symlinks evita problemas relacionados com a mudança de nomes do dispositivo. Para mais informações, consulte problemas de nomes do [dispositivo Troubleshoot](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Monte os discos.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Adicione a fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Executar o Set-AzVMDiskCryptonExtension Extension PowerShell cmdlet com -EncryptFormatAll para encriptar estes discos.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Instale lvm em cima destes discos novos. Note que as unidades encriptadas são desbloqueadas depois de o VM ter terminado o arranque. Assim, a montagem do LVM também terá de ser posteriormente adiada.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Novos VMs IaaS criados a partir de VHD encriptado pelo cliente e chaves de encriptação
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. As seguintes secções explicam mais detalhadamente o modelo de Gestor de Recursos e comandos CLI. 

Utilize as instruções no apêndice para preparar imagens pré-encriptadas que possam ser utilizadas em Azure. Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.

* [Prepare um Linux VHD pré-encriptado](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >É obrigatório tirar uma fotografia ou fazer back-up de uma instância VM gerida fora e antes de permitir a encriptação do disco Azure. Pode tirar uma foto do disco gerido do portal, ou pode utilizar o [Azure Backup](../../backup/backup-azure-vms-encryption.md). As cópias de segurança asseguram que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois de ser feita uma cópia de segurança, utilize o cmdlet set-AzVMDiskCryptonExtension para encriptar discos geridos especificando o parâmetro -skipVmBackup. O comando set-AzVMDiskCryptonExtension falha contra VMs geridos baseados em disco até que seja feita uma cópia de segurança e este parâmetro seja especificado. 
>
>Encriptar ou desativar encriptação pode fazer com que o VM reinicie.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Utilize o Azure PowerShell para encriptar VMs IaaS com VHDs pré-encriptados 
Pode ativar a encriptação do disco no vHD encriptado utilizando o PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo que se segue dá-lhe alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado
Pode adicionar um novo disco de dados utilizando a fixação do [disco Az vm](add-disk.md) ou [através do portal Azure](attach-disk-portal.md). Antes de poder encriptar, tem de montar primeiro o disco de dados recém-anexado. Deve solicitar a encriptação da unidade de dados porque a unidade será inutilizável enquanto a encriptação está em andamento. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com o Azure CLI
 Se o VM foi previamente encriptado com "All", então o parâmetro do tipo volume deve permanecer All. Tudo inclui os discos de OS e de dados. Se o VM foi previamente encriptado com um tipo de volume de "OS", então o parâmetro do tipo -volume deve ser alterado para Todos para que tanto o SISTEMA como o novo disco de dados sejam incluídos. Se o VM foi encriptado apenas com o tipo de volume de "Dados", então pode permanecer Data como demonstrado aqui. Adicionar e anexar um novo disco de dados a um VM não é uma preparação suficiente para encriptação. O disco recém-ligado também deve ser formatado e montado corretamente dentro do VM antes de ativar a encriptação. No Linux, o disco deve ser montado em /etc/fstab com um [nome de dispositivo](troubleshoot-device-names-problems.md)de bloco persistente . 

Ao contrário da sintaxe PowerShell, o CLI não requer que forneça uma versão de sequência única quando ativa a encriptação. O CLI gera automaticamente e utiliza o seu próprio valor de versão de sequência única.

-  **Criptografe um VM em execução utilizando um segredo de cliente:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe um VM em execução usando kek para embrulhar o segredo do cliente:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Quando utiliza o PowerShell para encriptar um novo disco para o Linux, é necessário especificar uma nova versão de sequência. A versão da sequência tem de ser única. O seguinte script gera um GUID para a versão de sequência. 
 

-  **Criptografe um VM em execução utilizando um segredo** de cliente: O seguinte script inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM, cofre chave, app Azure AD e segredo de cliente já deveriam ter sido criados como pré-requisitos. Substitua o MyVirtualMachineResourceGroup, myKeyVaultResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID e o my-AAD-client-secret com os seus valores. O parâmetro -VolumeType é definido para discos de dados e não para o disco OS. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para Todos para que tanto o SISTEMA como o novo disco de dados sejam incluídos.

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
- **Criptografe um VM em execução usando kek para embrulhar o segredo do cliente:** A Encriptação do Disco Azure permite especificar uma chave existente no seu cofre chave para embrulhar os segredos de encriptação do disco que foram gerados ao mesmo tempo que permite a encriptação. Quando uma chave de encriptação é especificada, a Encriptação do Disco Azure usa essa chave para embrulhar os segredos de encriptação antes de escrever para o cofre da chave. O parâmetro -VolumeType é definido para discos de dados e não para o disco OS. Se o VM foi previamente encriptado com um tipo de volume de "OS" ou "All", então o parâmetro -VolumeType deve ser alterado para Todos para que tanto o SISTEMA como o novo disco de dados sejam incluídos.

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
> A sintaxe para o valor do parâmetro do cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Desativar a encriptação para VMs Linux
Pode desativar a encriptação utilizando o Azure PowerShell, o Azure CLI ou um modelo de Gestor de Recursos. 

>[!IMPORTANT]
>A encriptação incapacitante com encriptação do disco Azure nos VMs linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de OS se o volume de S tiver sido encriptado. 

- **Desative a encriptação do disco com o Azure PowerShell:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzureRmVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo de Gestor de Recursos:** Para desativar a encriptação, utilize a [encriptação Desativação num modelo VM Linux em execução.](https://aka.ms/decrypt-linuxvm)
     1. Selecione **Implementar para Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, termos legais e acordo.
     3. Selecione **Comprar** para desativar a encriptação do disco num VM windows em execução. 


## <a name="next-steps"></a>Passos seguintes

- [Encriptação de disco azure para visão geral de Linux](disk-encryption-overview-aad.md)
- [Criação e configuração de um cofre chave para encriptação de disco azure com AD Azure (versão anterior)](disk-encryption-key-vault-aad.md)
