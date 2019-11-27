---
title: O Azure Disk Encryption, com as VMs de IaaS de Linux do Azure AD App (versão anterior)
description: Este artigo fornece instruções sobre como ativar o Microsoft Azure Disk Encryption para VMs de IaaS Linux.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d41f2138a453e4a34354c10bbebad41724a18d1d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457480"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Habilitar Azure Disk Encryption com o Azure AD em VMs do Linux (versão anterior)

A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo Azure Active Directory (AD do Azure) para habilitar a criptografia de disco de VM. Com a nova versão, não é mais necessário fornecer as credenciais do Azure AD durante a etapa habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD usando a nova versão. Para obter instruções sobre como habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption para VMs do Linux](disk-encryption-linux.md). As VMs que já foram criptografadas com parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.

Você pode habilitar muitos cenários de criptografia de disco e as etapas podem variar de acordo com o cenário. As seções a seguir abordam os cenários em mais detalhes para VMs de IaaS (infraestrutura como serviço) do Linux. Você só pode aplicar a criptografia de disco a máquinas virtuais de [tamanhos de VM e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems). Você também deve atender aos seguintes pré-requisitos:

- [Requisitos adicionais para VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Rede e Política de Grupo](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Faça um [instantâneo](snapshot-copy-managed-disk.md), faça um backup ou ambos antes de criptografar os discos. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Depois que um backup é feito, você pode usar o cmdlet Set-AzVMDiskEncryptionExtension para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. Para obter mais informações sobre como fazer backup e restaurar VMs criptografadas, consulte [backup do Azure](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Se você usou anteriormente [Azure Disk Encryption com o aplicativo Azure ad](disk-encryption-overview-aad.md) para criptografar essa VM, você deve continuar a usar essa opção para criptografar sua VM. Você não pode usar [Azure Disk Encryption](disk-encryption-overview.md) nesta VM criptografada porque esse não é um cenário com suporte, o que significa mudar para fora do aplicativo Azure ad para essa VM criptografada ainda não tem suporte.
 > - Para garantir que os segredos de criptografia não entrem em limites regionais, Azure Disk Encryption precisa que o cofre de chaves e as VMs sejam colocalizados na mesma região. Crie e use um cofre de chaves que esteja na mesma região que a VM a ser criptografada.
 > - Quando você criptografa volumes do sistema operacional Linux, o processo pode levar algumas horas. É normal que OS volumes do SO Linux demorem mais do que os volumes de dados para criptografar.
> - Quando você criptografa volumes do sistema operacional Linux, a VM deve ser considerada indisponível. É altamente recomendável que você evite logons SSH enquanto a criptografia estiver em andamento para evitar o bloqueio de arquivos abertos que precisam ser acessados durante o processo de criptografia. Para verificar o progresso, use os comandos [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) ou [criptografia de VM show](/cli/azure/vm/encryption#az-vm-encryption-show) . Você pode esperar que esse processo demore algumas horas para um volume de so de 30 GB, além de mais tempo para criptografar volumes de dados. O tempo de criptografia do volume de dados é proporcional ao tamanho e à quantidade dos volumes de dados, a menos que a opção de **formato criptografado tudo** seja usada. 
 > - A desativação da encriptação em VMs do Linux só é suportada em volumes de dados. Não haverá suporte para os volumes de dados ou do sistema operacional se o volume do sistema operacional tiver sido criptografado. 

 

## <a name="bkmk_RunningLinux"></a> Habilitar a criptografia em uma VM IaaS do Linux existente ou em execução

Nesse cenário, você pode habilitar a criptografia usando o modelo de Azure Resource Manager, cmdlets do PowerShell ou comandos CLI do Azure. 

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo ou fazer backup de uma instância de VM baseada em disco gerenciado fora do e antes de habilitar o Azure Disk Encryption. Você pode tirar um instantâneo do disco gerenciado do portal do Azure ou pode usar o [backup do Azure](../../backup/backup-azure-vms-encryption.md). As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois que um backup for feito, use o cmdlet Set-AzVMDiskEncryptionExtension para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. O comando Set-AzVMDiskEncryptionExtension falhará em VMs baseadas em disco gerenciadas até que um backup seja feito e esse parâmetro seja especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reinicializada. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Habilitar a criptografia em uma VM Linux existente ou em execução usando o CLI do Azure 
Você pode habilitar a criptografia de disco em seu VHD criptografado Instalando e usando a ferramenta de linha de comando [CLI do Azure 2,0](/cli/azure) . Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para ativar a encriptação em existente ou executar VMs do Linux IaaS no Azure, utilize os seguintes comandos da CLI:

Use o comando [AZ VM Encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure.

-  **Criptografar uma VM em execução usando um segredo do cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Criptografe uma VM em execução usando KEK para encapsular o segredo do cliente:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-Name].</br> </br> A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em: https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID].

- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o comando [AZ VM Encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) . 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desabilitar criptografia:** Para desabilitar a criptografia, use o comando [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Habilitar a criptografia em uma VM Linux existente ou em execução usando o PowerShell
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para habilitar a criptografia em uma máquina virtual IaaS em execução no Azure. Faça um [instantâneo](snapshot-copy-managed-disk.md) ou faça um backup da VM com o [backup do Azure](../../backup/backup-azure-vms-encryption.md) antes que os discos sejam criptografados. O parâmetro-skipVmBackup já está especificado nos scripts do PowerShell para criptografar uma VM do Linux em execução.

- **Criptografar uma VM em execução usando um segredo do cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, o cofre de chaves, o aplicativo do Azure AD e o segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores. Modifique o parâmetro-Volumetype para especificar quais discos você está criptografando.

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
- **Criptografe uma VM em execução usando Kek para encapsular o segredo do cliente:** Azure Disk Encryption permite especificar uma chave existente no cofre de chaves para encapsular os segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para encapsular os segredos de criptografia antes de gravar no cofre de chaves. Modifique o parâmetro-Volumetype para especificar quais discos você está criptografando. 

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
  > A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[KVresource-Group-Name]/providers/Microsoft.KeyVault/vaults/[keyvault-Name].</br> </br>
  A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em: https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID]. 
    
- **Verifique se os discos estão criptografados:** Para verificar o status de criptografia de uma VM IaaS, use o cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) . 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Desabilitar a criptografia de disco:** Para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Habilitar a criptografia em uma VM IaaS Linux existente ou em execução com um modelo

Você pode habilitar a criptografia de disco em uma VM do Linux IaaS existente ou em execução no Azure usando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Selecione **implantar no Azure** no modelo de início rápido do Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Selecione **criar** para habilitar a criptografia na VM IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para existente ou executar VMs com um ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| AADClientSecret | Segredo do cliente de aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| keyVaultName | Nome do Cofre de chaves que a chave deve ser enviada para. Você pode obtê-lo usando o comando CLI do Azure `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada. Esse parâmetro é opcional se você selecionar **nokek** na lista suspensa **UseExistingKek** . Se você selecionar **Kek** na lista suspensa **UseExistingKek** , deverá inserir o valor de _keyEncryptionKeyURL_ . |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores válidos com _suporte são os_ ou _todos_. (Consulte distribuições do Linux com suporte e suas versões para OS discos do sistema operacional e de dados na seção de pré-requisitos anterior.) |
| sequenceVersion | Versão de sequência da operação de disco BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada na mesma VM. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |
| frase de acesso | Escreva uma frase de acesso forte como a chave de encriptação de dados. |



## <a name="bkmk_EFA"> </a>Usar o recurso EncryptFormatAll para discos de dados em VMs IaaS do Linux
O parâmetro EncryptFormatAll reduz o tempo para que os discos de dados do Linux sejam criptografados. As partições que atendem a determinados critérios são formatadas (com o sistema de arquivos atual). Em seguida, eles são remontados de volta para onde estavam antes da execução do comando. Se desejar excluir um disco de dados que atenda aos critérios, você poderá desmontá-lo antes de executar o comando.

 Depois de executar esse comando, todas as unidades que foram montadas anteriormente são formatadas. Em seguida, a camada de criptografia começa na parte superior da unidade agora vazia. Quando essa opção é selecionada, o disco de recurso efêmero anexado à VM também é criptografado. Se a unidade efêmera for redefinida, ela será reformatada e criptografada novamente para a VM pela solução de Azure Disk Encryption na próxima oportunidade.

>[!WARNING]
> EncryptFormatAll não deve ser usado quando houver dados necessários nos volumes de dados de uma VM. Você pode excluir discos da criptografia desmontando-os. Experimente o parâmetro EncryptFormatAll em uma VM de teste primeiro para entender o parâmetro de recurso e sua implicação antes de experimentá-lo na VM de produção. A opção EncryptFormatAll formata o disco de dados, de modo que todos os dados nele serão perdidos. Antes de prosseguir, verifique se os discos que você deseja excluir estão desmontados corretamente. </br></br>
 >Se você definir esse parâmetro enquanto atualiza as configurações de criptografia, isso poderá levar a uma reinicialização antes da criptografia real. Nesse caso, você também deseja remover o disco que não deseja Formatar do arquivo fstab. Da mesma forma, você deve adicionar a partição que deseja criptografar no arquivo fstab antes de iniciar a operação de criptografia. 

### <a name="bkmk_EFACriteria"></a> Critérios de EncryptFormatAll
O parâmetro passa por todas as partições e as criptografa, desde que atendam a *todos* os critérios a seguir: 
- Não é uma partição de raiz/sistema operacional/arranque
- Já não está encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Encripte os discos que compõem o volume RAID ou LVM em vez de volume RAID ou LVM.

### <a name="bkmk_EFATemplate"></a> Usar o parâmetro EncryptFormatAll com um modelo
Para usar a opção EncryptFormatAll, use qualquer modelo de Azure Resource Manager preexistente que criptografa uma VM do Linux e altere o campo **EncryptionOperation** para o recurso AzureDiskEncryption.

1. Por exemplo, use o [modelo do Resource Manager para criptografar uma VM IaaS do Linux em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Selecione **implantar no Azure** no modelo de início rápido do Azure.
3. Altere o campo **EncryptionOperation** de **enableencryption e** para **EnableEncryptionFormatAl**.
4. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, outros parâmetros, os termos legais e contrato. Selecione **criar** para habilitar a criptografia na VM IaaS existente ou em execução.


### <a name="bkmk_EFAPSH"></a> Usar o parâmetro EncryptFormatAll com um cmdlet do PowerShell
Use o cmdlet [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) com o parâmetro EncryptFormatAll.

**Criptografar uma VM em execução usando um segredo do cliente e EncryptFormatAll:** Por exemplo, o script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, a VM, o cofre de chaves, o aplicativo do Azure AD e o segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores.
  
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


### <a name="bkmk_EFALVM"></a> Usar o parâmetro EncryptFormatAll com o LVM (Gerenciador de volumes lógicos) 
Recomendamos uma configuração de LVM-no-crypt. Para todos os exemplos a seguir, substitua o caminho-do-dispositivo e o montagem pelo que for adequado ao seu caso de uso. Esta configuração pode ser feita da seguinte forma:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar estes discos para o ficheiro fstab.

    1. Formate o disco adicionado recentemente. Utilizamos os links simbólicos gerados pelo Azure aqui. Usar os links simbólicos evita problemas relacionados com a alteração de nomes de dispositivo. Para obter mais informações, consulte [solucionar problemas de nomes de dispositivo](troubleshoot-device-names-problems.md).
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Monte os discos.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Adicione a fstab.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Execute o cmdlet do PowerShell Set-AzVMDiskEncryptionExtension com-EncryptFormatAll para criptografar esses discos.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Configure LVM sobre esses novos discos. Tenha em atenção de que as unidades encriptadas são desbloqueadas depois da VM tem concluir a inicialização. Por isso, a montagem de LVM também terá, em seguida, ser atrasada.




## <a name="bkmk_VHDpre"></a> Novas VMs IaaS criadas do VHD criptografado pelo cliente e chaves de criptografia
Neste cenário, pode ativar a encriptação com o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam em maior detalhe o modelo do Resource Manager e os comandos da CLI. 

Utilize as instruções no apêndice para preparar imagens encriptadas que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Preparar um VHD do Linux previamente criptografado](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >É obrigatório tirar um instantâneo ou fazer backup de uma instância de VM baseada em disco gerenciado fora do e antes de habilitar o Azure Disk Encryption. Você pode tirar um instantâneo do disco gerenciado do portal ou pode usar o [backup do Azure](../../backup/backup-azure-vms-encryption.md). As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Depois que um backup for feito, use o cmdlet Set-AzVMDiskEncryptionExtension para criptografar discos gerenciados especificando o parâmetro-skipVmBackup. O comando Set-AzVMDiskEncryptionExtension falhará em VMs baseadas em disco gerenciadas até que um backup seja feito e esse parâmetro seja especificado. 
>
>Criptografar ou desabilitar a criptografia pode fazer com que a VM seja reinicializada.



### <a name="bkmk_VHDprePSH"></a> Usar Azure PowerShell para criptografar VMs IaaS com VHDs previamente criptografados 
Você pode habilitar a criptografia de disco em seu VHD criptografado usando o cmdlet do PowerShell [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir fornece alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente
Você pode adicionar um novo disco de dados usando [AZ VM Disk Attach](add-disk.md) ou [por meio do portal do Azure](attach-disk-portal.md). Pode criptografar, terá de montar o disco de dados anexado recentemente pela primeira vez. Você deve solicitar a criptografia da unidade de dados porque a unidade será inutilizável enquanto a criptografia estiver em andamento. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Habilitar a criptografia em um disco recém-adicionado com o CLI do Azure
 Se a VM tiver sido criptografada anteriormente com "All", o parâmetro--volume-Type deverá permanecer todos. Inclui todos os discos de SO e dados. Se a VM tiver sido criptografada anteriormente com um tipo de volume de "OS", o parâmetro--volume-Type deverá ser alterado para All, de modo que o so e o novo disco de dados serão incluídos. Se a VM tiver sido criptografada apenas com o tipo de volume de "dados", ela poderá permanecer com os dados, conforme demonstrado aqui. Adicionar e anexar um novo disco de dados a uma VM não é uma preparação suficiente para a criptografia. O disco recentemente anexado também deve ser formatado e montado corretamente na VM antes de habilitar a criptografia. No Linux, o disco deve ser montado em/etc/fstab com um [nome de dispositivo de bloqueio persistente](troubleshoot-device-names-problems.md). 

Ao contrário da sintaxe do PowerShell, a CLI não exige que você forneça uma versão de sequência exclusiva ao habilitar a criptografia. A CLI gera e usa seu próprio valor de versão de seqüência única automaticamente.

-  **Criptografar uma VM em execução usando um segredo do cliente:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Criptografe uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando você usa o PowerShell para criptografar um novo disco para Linux, uma nova versão de sequência precisa ser especificada. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. 
 

-  **Criptografar uma VM em execução usando um segredo do cliente:** O script a seguir inicializa suas variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, o cofre de chaves, o aplicativo do Azure AD e o segredo do cliente já devem ter sido criados como pré-requisitos. Substitua MyVirtualMachineResourceGroup, MyKeyVaultResourceGroup, MySecureVM, MySecureVault, meu-AAD-Client-ID e meu-AAD-Client-Secret pelos valores. O parâmetro - VolumeType está definido para discos de dados e não o disco do SO. Se a VM tiver sido criptografada anteriormente com um tipo de volume de "os" ou "All", o parâmetro-Volumetype deverá ser alterado para All, de modo que o sistema operacional e o novo disco de dados serão incluídos.

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
- **Criptografe uma VM em execução usando Kek para encapsular o segredo do cliente:** Azure Disk Encryption permite especificar uma chave existente no cofre de chaves para encapsular os segredos de criptografia de disco que foram gerados ao habilitar a criptografia. Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para encapsular os segredos de criptografia antes de gravar no cofre de chaves. O parâmetro - VolumeType está definido para discos de dados e não o disco do SO. Se a VM tiver sido criptografada anteriormente com um tipo de volume de "os" ou "All", o parâmetro-Volumetype deverá ser alterado para All, de modo que o sistema operacional e o novo disco de dados serão incluídos.

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
> A sintaxe para o valor do parâmetro Disk-Encryption-keyvault é a cadeia de caracteres do identificador completo:/subscriptions/[Subscription-ID-GUID]/resourceGroups/[nome-do-grupo-de-recursos]/providers/Microsoft.KeyVault/vaults/[keyvault-Name]. </br> </br>
A sintaxe para o valor do parâmetro Key-Encryption-Key é o URI completo para o KEK como em: https://[keyvault-Name]. Vault. Azure. net/Keys/[kekname]/[Kek-Unique-ID].

## <a name="disable-encryption-for-linux-vms"></a>Desativar a encriptação para VMs do Linux
Você pode desabilitar a criptografia usando Azure PowerShell, o CLI do Azure ou um modelo do Resource Manager. 

>[!IMPORTANT]
>A desativação da encriptação com o Azure Disk Encryption em VMs do Linux só é suportada em volumes de dados. Não haverá suporte para os volumes de dados ou do sistema operacional se o volume do sistema operacional tiver sido criptografado. 

- **Desabilitar a criptografia de disco com o Azure PowerShell:** Para desabilitar a criptografia, use o cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desabilite a criptografia com o CLI do Azure:** Para desabilitar a criptografia, use o comando [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Desabilitar a criptografia com um modelo do Resource Manager:** Para desabilitar a criptografia, use a [desabilitação de criptografia em um modelo de VM Linux em execução](https://aka.ms/decrypt-linuxvm) .
     1. Selecione **implantar no Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, os termos legais e contrato.
     3. Selecione **comprar** para desabilitar a criptografia de disco em uma VM do Windows em execução. 


## <a name="next-steps"></a>Passos Seguintes

- [Visão geral do Azure Disk Encryption para Linux](disk-encryption-overview-aad.md)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
