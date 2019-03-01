---
title: O Azure Disk Encryption, com as VMs de IaaS de Linux do Azure AD App (versão anterior)
description: Este artigo fornece instruções sobre como ativar o Microsoft Azure Disk Encryption para VMs de IaaS Linux.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 12/17/2018
ms.custom: seodec18
ms.openlocfilehash: 22b04b6820d838122c38c258065ebe6cf52807ba
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990871"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Ativar o Azure Disk Encryption para VMs de IaaS Linux (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicação do Azure AD para ativar a encriptação de disco da VM. Com a nova versão, já não tem de fornecer credenciais do Azure AD durante o passo da criptografia de ativação. Todas as novas VMs tem de estar encriptadas sem os parâmetros da aplicação do Azure AD com a nova versão. Para ver instruções para ativar a encriptação de disco VM com a nova versão, consulte [do Azure Disk Encryption para VMS do Linux](azure-security-disk-encryption-linux.md). As VMs que já foram encriptadas com parâmetros de aplicação do Azure AD ainda são suportadas e devem continuar a ser mantido com a sintaxe do AAD.**

Pode ativar vários cenários de encriptação de disco e os passos podem variar de acordo com o cenário. As secções seguintes abrangem os cenários mais detalhadamente para VMs de IaaS Linux. Antes de poder utilizar a encriptação de disco, o [pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites-aad.md) têm de ser concluídos e o [pré-requisitos adicionais para VMs de IaaS Linux](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) secção deve ser revista.

Efetuar uma [instantâneo](../virtual-machines/windows/snapshot-copy-managed-disk.md) e/ou criar cópias de segurança antes dos discos são encriptados. As cópias de segurança Certifique-se de que uma opção de recuperação é possível que ocorra uma falha inesperada durante a encriptação. As VMs com discos geridos requerem uma cópia de segurança antes de ocorre de encriptação. Assim que for feita uma cópia de segurança, pode utilizar o cmdlet Set-AzVMDiskEncryptionExtension encriptar discos geridos ao especificar o parâmetro - skipVmBackup. Para obter mais informações sobre como criar cópias de segurança e restaurar VMs encriptadas, consulte a [Azure Backup](../backup/backup-azure-vms-encryption.md) artigo. 

>[!WARNING]
 > - Se tiver utilizado anteriormente [do Azure Disk Encryption, com a aplicação do Azure AD](azure-security-disk-encryption-prerequisites-aad.md) para encriptar esta VM, terá de continuar a utilizar esta opção para encriptar a sua VM. Não é possível usar [do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nesta VM encriptada como isso não é um cenário suportado, significado mudar para fora da aplicação do AAD para encriptados esta VM não é suportada ainda.
 > - Para certificar-se de que os segredos de encriptação, não ultrapassam limites regionais, o Azure Disk Encryption tem o Cofre de chaves e as VMs para estar colocalizados na mesma região. Criar e utilizar um cofre de chaves que está na mesma região que a VM ser encriptada.
 > - Quando encriptar os volumes do SO Linux, o processo pode demorar algumas horas. É normal para volumes de SO Linux para a demorar mais do que os volumes de dados para encriptar. 
 > - A desativação da encriptação em VMs do Linux só é suportada em volumes de dados. Não é suportada em dados ou volumes de sistema operacional se o volume do sistema operacional tenha sido criptografado.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_NewLinux"></a> Implementar uma nova VM do IaaS Linux com a encriptação de disco ativada 

1. Utilize o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) criar um novo encriptados VM do Linux IaaS. O modelo irá criar uma nova VM do Red Hat Linux 7.2 com uma matriz de RAID-0 de 200 GB e a encriptação de disco completa a utilizar discos geridos. Sobre o [FAQ](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) artigo, perceberá que algumas distribuições do Linux apenas suportam a encriptação de discos de dados. No entanto, este modelo fornece uma oportunidade de se familiarizar com os modelos de implantar e verificar o estado de encriptação com vários métodos. 
 
1. Clique em **implementar no Azure** no modelo do Azure Resource Manager.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **criar** para ativar a encriptação na VM de IaaS existente ou em execução.

3. Depois de implementar o modelo, verifique se o estado de encriptação da VM com o seu método preferencial:
     - Certifique-se com a CLI do Azure utilizando o [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Certifique-se com o Azure PowerShell, utilizando o [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Selecione a VM, em seguida, clique em **discos** sob a **definições** cabeçalho para verificar o estado de encriptação no portal. No gráfico de no **Encryption**, verá se estiver ativada. 

| Parâmetro | Descrição |
| --- | --- |
| ID de cliente do AAD | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| Segredo do cliente AAD | Segredo do cliente de aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| Nome do Key Vault | Nome do Cofre de chaves que a chave deve ser colocada. |
| Grupo de Recursos do Cofre de Chaves | Grupo de recursos do Cofre de chaves. |


## <a name="bkmk_RunningLinux"> </a> Ativar a encriptação numa VM do Linux IaaS existente ou em execução
Neste cenário, pode ativar a encriptação com o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. 

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Ativar a encriptação numa VM do Linux em execução ou existente com a CLI do Azure 
Pode ativar a encriptação de disco no seu VHD encriptado por instalar e utilizar o [CLI 2.0 do Azure](/cli/azure) ferramenta da linha de comandos. Pode utilizá-lo no seu browser com o [Azure Cloud Shell](../cloud-shell/overview.md) ou pode instalá-lo no seu computador local e utilizá-lo em qualquer sessão do PowerShell. Para ativar a encriptação em existente ou executar VMs do Linux IaaS no Azure, utilize os seguintes comandos da CLI:

Utilize o [ativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-enable) comando para ativar a encriptação numa máquina virtual de IaaS em execução no Azure.

-  **Encripte uma VM em execução usando um segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Encripte uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Certifique-se de que os discos são encriptados:** Para verificar o estado de encriptação de uma VM de IaaS, utilize o [show do az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-show) comando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Desative a encriptação:** Para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Ativar a encriptação numa VM do Linux em execução ou existente com o PowerShell
Utilize o [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet para ativar a encriptação numa máquina virtual de IaaS em execução no Azure. 

-  **Encripte uma VM em execução usando um segredo do cliente:** O script a seguir inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, Cofre de chaves, uma aplicação do AAD e segredo do cliente devem já foram criadas como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, minha-AAD-client-ID e minha-AAD-client-secret pelos seus valores. Terá de adicionar o parâmetro - VolumeType se de que está a encriptar discos de dados e não o disco do SO. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Encripte uma VM em execução usando KEK para encapsular o segredo do cliente:** O Azure Disk Encryption permite-lhe especificar uma chave existente no seu Cofre de chaves para encapsular os segredos de encriptação de disco que foram gerados ao ativar a encriptação. Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. Terá de adicionar o parâmetro - VolumeType se de que está a encriptar discos de dados e não o disco do SO. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

 >[!NOTE]
 > A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Certifique-se de que os discos são encriptados:** Para verificar o estado de encriptação de uma VM de IaaS, utilize o [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Desative a encriptação de disco:** Para desativar a encriptação, utilize o [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. A desativação da encriptação só é permitida em volumes de dados para VMs do Linux.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Ativar a encriptação numa VM do Linux IaaS existente ou em execução com um modelo

Pode ativar a encriptação de disco numa VM do Linux IaaS existente ou em execução no Azure utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Clique em **implementar no Azure** no modelo de início rápido do Azure.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **criar** para ativar a encriptação na VM de IaaS existente ou em execução.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para existente ou executar VMs com um ID de cliente do Azure AD:

| Parâmetro | Descrição |
| --- | --- |
| AADClientID | ID de cliente da aplicação do Azure AD que tenha permissões para escrever segredos para o Cofre de chaves. |
| AADClientSecret | Segredo do cliente de aplicação do Azure AD que tenha permissões para escrever segredos para o seu Cofre de chaves. |
| keyVaultName | Nome do Cofre de chaves que a chave deve ser enviada para. Pode obtê-lo através do comando da CLI do Azure `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  keyEncryptionKeyURL | URL da chave de encriptação de chave que é utilizada para encriptar a chave gerada. Este parâmetro é opcional se selecionou **nokek** na lista pendente UseExistingKek. Se selecionou **kek** na lista pendente UseExistingKek, tem de introduzir o _keyEncryptionKeyURL_ valor. |
| VolumeType | Tipo de volume que a operação de criptografia é executada em. Os valores suportados válidos são _SO_ ou _todos os_ (consulte suportado distribuições de Linux e suas versões de SO e discos de dados na secção de pré-requisitos anteriormente). |
| sequenceVersion | Versão de sequência da operação de disco BitLocker. Incremente este número de versão sempre que uma operação de encriptação de disco é realizada na mesma VM. |
| vmName | Nome da VM que a operação de encriptação está a ser efetuada numa. |
| frase de acesso | Escreva uma frase de acesso forte como a chave de encriptação de dados. |



## <a name="bkmk_EFA"> </a>Utilizar a funcionalidade de EncryptFormatAll para discos de dados em VMs do IaaS Linux
O **EncryptFormatAll** parâmetro reduz o tempo para discos de dados do Linux ser encriptada. Partições determinados critérios de reunião serão formatadas (com o seu atual sistema de ficheiros). Em seguida, irá ser remontadas para onde estava antes da execução do comando. Se desejar excluir um disco de dados que satisfaça os critérios, poder desmontá-la antes de executar o comando.

 Depois de executar este comando, todas as unidades que foram montadas anteriormente serão formatadas. Em seguida, a camada de encriptação será iniciada na parte superior da unidade agora vazia. Quando esta opção estiver selecionada, o disco de recurso efémeras anexado à VM também será encriptado. Se a unidade efémeras é reposta, será reformatada e encriptado novamente para a VM pela solução Azure Disk Encryption na próxima oportunidade.

>[!WARNING]
> EncryptFormatAll não deve ser usado quando há dados necessários em volumes de dados de uma VM. Pode excluir discos da encriptação pela desmontagem-los. Primeiro deve experimentar EncryptFormatAll pela primeira vez numa VM de teste, compreender o parâmetro de recurso e sua implicação antes de tentá-lo na VM de produção. A opção de EncryptFormatAll formata o disco de dados e todos os dados serão perdidos. Antes de continuar, certifique-se de que os discos que pretende excluir estão desmontados corretamente. </br></br>
 >Se estiver definindo esse parâmetro ao atualizar as definições de encriptação, pode originar um reinício antes da encriptação real. Neste caso, também desejará remover o disco que não desejado do ficheiro fstab. Da mesma forma, deve adicionar a partição que pretende encriptar formatada para o ficheiro fstab antes de iniciar a operação de criptografia. 

### <a name="bkmk_EFACriteria"> </a> Critérios de EncryptFormatAll
O parâmetro vai, no entanto, todas as partições e criptografa-las, desde que cumpram **todos os** dos critérios abaixo: 
- Não é uma partição de raiz/sistema operacional/arranque
- Já não está encriptado
- Não é um volume BEK
- Não é um volume RAID
- Não é um volume LVM
- Está montado

Encripte os discos que compõem o volume RAID ou LVM em vez de volume RAID ou LVM.

### <a name="bkmk_EFATemplate"> </a> Utilize o parâmetro de EncryptFormatAll com um modelo
Para utilizar a opção de EncryptFormatAll, utilize qualquer modelo Azure Resource Manager já existente, que criptografa uma VM do Linux e alterar os **EncryptionOperation** campo para o recurso de AzureDiskEncryption.

1. Por exemplo, utilize o [modelo do Resource Manager para encriptar uma VM de IaaS Linux em execução](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Clique em **implementar no Azure** no modelo de início rápido do Azure.
3. Alteração da **EncryptionOperation** partir **EnableEncryption** para **EnableEncryptionFormatAl**
4. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, outros parâmetros, os termos legais e contrato. Clique em **criar** para ativar a encriptação na VM de IaaS existente ou em execução.


### <a name="bkmk_EFAPSH"> </a> Utilize o parâmetro de EncryptFormatAll com um cmdlet do PowerShell
Utilize o [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet com o `EncryptFormatAll` parâmetro.

**Encripte uma VM em execução através de um segredo do cliente e EncryptFormatAll:** Por exemplo, o script a seguir inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension com o parâmetro EncryptFormatAll. O grupo de recursos, a VM, Cofre de chaves, uma aplicação do AAD e segredo do cliente devem já foram criadas como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, minha-AAD-client-ID e minha-AAD-client-secret pelos seus valores.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Utilize o parâmetro de EncryptFormatAll com o Gestor de Volume lógicos (LVM) 
Recomendamos uma configuração de LVM-no-crypt. Para todos os exemplos seguintes, substitua o caminho do dispositivo e mountpoints tudo o que se adequa aos seus casos de utilização. Esta configuração pode ser feita da seguinte forma:

- Adicione os discos de dados que irão compor a VM.
- Formatar, montar e adicionar estes discos para o ficheiro fstab.

    1. Formate o disco adicionado recentemente. Utilizamos os links simbólicos gerados pelo Azure aqui. Usar os links simbólicos evita problemas relacionados com a alteração de nomes de dispositivo. Para obter mais informações, consulte a [problemas de resolução de problemas de nomes de dispositivo](../virtual-machines/linux/troubleshoot-device-names-problems.md) artigo.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Monte os discos.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Adicione a fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Execute o cmdlet do PowerShell Set-AzVMDiskEncryptionExtension com - EncryptFormatAll para encriptar estes discos.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Configure LVM sobre esses novos discos. Tenha em atenção de que as unidades encriptadas são desbloqueadas depois da VM tem concluir a inicialização. Por isso, a montagem de LVM também terá, em seguida, ser atrasada.




## <a name="bkmk_VHDpre"> </a> Novas VMs IaaS, criado a partir de chaves VHD de encriptação encriptadas de cliente
Neste cenário, pode ativar a encriptação com o modelo do Resource Manager, cmdlets do PowerShell ou comandos da CLI. As secções seguintes explicam em maior detalhe o modelo do Resource Manager e os comandos da CLI. 

Utilize as instruções no apêndice para preparar imagens encriptadas que podem ser utilizadas no Azure. Depois da imagem é criada, pode utilizar os passos na secção seguinte para criar uma VM do Azure encriptadas.

* [Preparar um VHD do Windows encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Preparar um VHD do Linux encriptadas](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >É obrigatório para instantâneo e/ou a cópia de segurança um disco gerido com base instância de VM fora do e antes de ativar o Azure Disk Encryption. Pode ser criado um instantâneo do disco gerido a partir do portal, ou [Azure Backup](../backup/backup-azure-vms-encryption.md) pode ser utilizado. As cópias de segurança Certifique-se de que uma opção de recuperação é possível no caso de qualquer falha inesperada durante a encriptação. Assim que for feita uma cópia de segurança, o cmdlet Set-AzVMDiskEncryptionExtension pode servir para encriptar discos geridos ao especificar o parâmetro - skipVmBackup. O comando Set-AzVMDiskEncryptionExtension irá falhar nas VMs de disco gerido com base em até que foi efetuada uma cópia de segurança e este parâmetro foi especificado. 
>
>Encriptar ou a desativação da encriptação pode fazer com que a VM a reiniciar. 



### <a name="bkmk_VHDprePSH"> </a> Utilizar o Azure PowerShell para encriptar VMs de IaaS com VHDs previamente encriptados 
Pode ativar a encriptação de disco no seu VHD encriptada utilizando o cmdlet do PowerShell [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo a seguir dá-lhe alguns parâmetros comuns. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Utilizar o modelo do Resource Manager para encriptar VMs de IaaS com VHDs previamente encriptados 
Pode ativar a encriptação de disco no seu VHD encriptada utilizando o [modelo do Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. No modelo de início rápido do Azure, clique em **implementar no Azure**.

2. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, parâmetros, os termos legais e contrato. Clique em **criar** para ativar a encriptação na nova VM de IaaS.

A tabela seguinte lista os parâmetros de modelo do Resource Manager para o VHD encriptado:

| Parâmetro | Descrição |
| --- | --- |
| newStorageAccountName | Nome da conta do storage para armazenar o VHD do SO encriptados. Esta conta de armazenamento deve já ter foi criada no mesmo grupo de recursos e na mesma localização da VM. |
| osVhdUri | URI do VHD do SO da conta de armazenamento. |
| osType | Tipo de produto do sistema operacional (Windows/Linux). |
| virtualNetworkName | Nome da VNet ao qual a NIC de VM devem pertencer à. O nome deve já ter foi criado no mesmo grupo de recursos e na mesma localização da VM. |
| subnetName | Nome da sub-rede na VNet que a NIC de VM devem pertencer à. |
| vmSize | Tamanho da VM. Atualmente, a série A Standard apenas, D e G é suportado. |
| keyVaultResourceID | O ResourceID que identifica o recurso do Cofre de chaves no Azure Resource Manager. Pode obtê-lo utilizando o cmdlet do PowerShell `(Get-AzKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` ou utilizando o comando da CLI do Azure `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL da chave de encriptação de disco que está configurada no Cofre de chaves. |
| keyVaultKekUrl | URL da chave de encriptação de chave para criptografar a chave de encriptação de disco gerado. |
| vmName | Nome da IaaS VM. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados adicionados recentemente
Pode adicionar um novo através de disco de dados [anexar o disco da vm az](../virtual-machines/linux/add-disk.md), ou [através do portal do Azure](../virtual-machines/linux/attach-disk-portal.md). Pode criptografar, terá de montar o disco de dados anexado recentemente pela primeira vez. Tem de solicitar a encriptação da unidade de dados, uma vez que a unidade não poderão ser utilizada, enquanto a encriptação está em curso. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco adicionado recentemente com a CLI do Azure
 Se a VM tiver sido anteriormente encriptada com "All", em seguida, o-- tipo de volume parâmetro deve permanecer todos. Inclui todos os discos de SO e dados. Se a VM tiver sido anteriormente encriptada com um tipo de volume de "SO", em seguida, o-- tipo de volume parâmetro deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos. Se a VM tiver sido encriptada com o tipo de volume de "Dados", em seguida, ele pode permanecer "Data" como demonstrado a seguir. Adicionar e anexar um disco de dados novo a uma VM não é suficiente preparação para a encriptação. O disco anexado recentemente também tem de ser formatado e corretamente montada dentro da VM antes de ativar a encriptação. No Linux, o disco deve ser montado no/etc/fstab com um [nome do dispositivo de bloco persistente](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Em contraste com a sintaxe do Powershell, a CLI não exigir que o utilizador fornecer uma versão de seqüência única ao ativar a encriptação. A CLI gera e usa seu próprio valor de versão de seqüência única automaticamente.

-  **Encripte uma VM em execução usando um segredo do cliente:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Encripte uma VM em execução usando KEK para encapsular o segredo do cliente:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco adicionado recentemente com o Azure PowerShell
 Quando utilizar o Powershell para encriptar um disco novo para Linux, uma nova versão de sequência tem de ser especificado. A versão de sequência tem de ser exclusivo. O script a seguir gera um GUID para a versão de sequência. 
 

-  **Encripte uma VM em execução usando um segredo do cliente:** O script a seguir inicializa as variáveis e executa o cmdlet Set-AzVMDiskEncryptionExtension. O grupo de recursos, a VM, Cofre de chaves, uma aplicação do AAD e segredo do cliente devem já foram criadas como pré-requisitos. Substitua MySecureRg, MySecureVM, MySecureVault, minha-AAD-client-ID e minha-AAD-client-secret pelos seus valores. O parâmetro - VolumeType está definido para discos de dados e não o disco do SO. Se a VM foi anteriormente encriptada com um tipo de volume de "SO" ou "Todos", em seguida, o parâmetro - VolumeType deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos.

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Encripte uma VM em execução usando KEK para encapsular o segredo do cliente:** O Azure Disk Encryption permite-lhe especificar uma chave existente no seu Cofre de chaves para encapsular os segredos de encriptação de disco que foram gerados ao ativar a encriptação. Quando é especificada uma chave de encriptação de chave, o Azure Disk Encryption utiliza essa chave para encapsular os segredos de encriptação antes de escrever para o Key Vault. O parâmetro - VolumeType está definido para discos de dados e não o disco do SO. Se a VM foi anteriormente encriptada com um tipo de volume de "SO" ou "Todos", em seguida, o parâmetro - VolumeType deve ser alterado para todos, para que o sistema operacional e o novo disco de dados que serão incluídos.

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';
     ```


>[!NOTE]
> A sintaxe para o valor do parâmetro de disco-encriptação-Cofre de chaves é a cadeia de caracteres de identificador completo: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
A sintaxe para o valor do parâmetro de chave de encriptação de chave é o URI completo para a KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Desativar a encriptação para VMs do Linux
Pode desativar a encriptação com o Azure PowerShell, CLI do Azure, ou com um modelo do Resource Manager. 

>[!IMPORTANT]
>A desativação da encriptação com o Azure Disk Encryption em VMs do Linux só é suportada em volumes de dados. Não é suportada em dados ou volumes de sistema operacional se o volume do sistema operacional tenha sido criptografado.  

- **Desative a encriptação de disco com o Azure PowerShell:** Para desativar a encriptação, utilize o [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Desative a encriptação com a CLI do Azure:** Para desativar a encriptação, utilize o [desativar a encriptação de vm de az](/cli/azure/vm/encryption#az-vm-encryption-disable) comando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Desative a encriptação com um modelo do Resource Manager:** Utilize o [desativar a encriptação numa VM do Linux em execução](https://aka.ms/decrypt-linuxvm) modelo para desativar a encriptação.
     1. Clique em **Implementar no Azure**.
     2. Selecione a subscrição, grupo de recursos, localização, VM, os termos legais e contrato.
     3.  Clique em **Compra** para desativar a encriptação de disco numa VM do Windows em execução. 


## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Ativar a encriptação de disco do Azure para Windows](azure-security-disk-encryption-windows-aad.md)
