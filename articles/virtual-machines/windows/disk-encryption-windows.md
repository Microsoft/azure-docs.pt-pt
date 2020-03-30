---
title: Cenários do Azure Disk Encryption em VMs do Windows
description: Este artigo fornece instruções sobre a ativação da encriptação do disco Microsoft Azure para VMs do Windows para vários cenários
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed64ee3d0e024c32be08ed4e010a6933033c3f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476523"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Cenários do Azure Disk Encryption em VMs do Windows

A Encriptação do Disco Azure utiliza o protetor de teclas externas BitLocker para fornecer encriptação de volume para o OS e discos de dados de máquinas virtuais Azure (VMs), e está integrada com o Azure Key Vault para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco. Para uma visão geral do serviço, consulte a [Encriptação do Disco Azure para VMs do Windows](disk-encryption-overview.md).

Existem muitos cenários de encriptação de discos, e os passos podem variar de acordo com o cenário. As seguintes secções cobrem os cenários com maior detalhe para Os VMs do Windows.

Só é possível aplicar encriptação de disco a máquinas virtuais de [tamanhos vm suportados e sistemas operativos.](disk-encryption-overview.md#supported-vms-and-operating-systems) Deve também cumprir os seguintes pré-requisitos:

- [Requisitos de networking](disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM. Consulte a Encriptação do [Disco Azure com AD Azure (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
>
> - Deve [tirar uma fotografia](snapshot-copy-managed-disk.md) e/ou criar uma cópia de segurança antes de os discos serem encriptados. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Uma vez feita uma cópia de segurança, pode utilizar o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para encriptar discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer o back-up e restaurar os VMs encriptados, consulte [O Back up e restaure o Azure VM encriptado](../../backup/backup-azure-vms-encryption.md). 
>
> - Encriptar ou desativar encriptação pode fazer com que um VM reinicie.

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Ativar a encriptação de um VM Windows existente ou em execução
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. Se precisar de informações de esquemapara a extensão virtual da máquina, consulte a encriptação do disco Azure para o artigo de [extensão do Windows.](../extensions/azure-disk-enc-windows.md)

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Ativar a encriptação em VMs iaaS existentes ou em execução
Pode ativar a encriptação utilizando um modelo, comandos PowerShell ou CLI. Se precisar de informações de esquemapara a extensão virtual da máquina, consulte a encriptação do disco Azure para o artigo de [extensão do Windows.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Ativar a encriptação em VMs existentes ou em execução com O PowerShell Azure 
Utilize o [cmdlet set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para permitir a encriptação numa máquina virtual IaaS em funcionamento em Azure. 

-  **Criptografe um VM em execução:** O script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM e cofre chave já deviam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, myVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Criptografe um VM em execução utilizando kek:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM IaaS, utilize o [cmdlet Get-AzVmDiskCryptonStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) A desativação da encriptação dos discos de dados numa VM do Windows quando os discos do SO e de dados foram encriptados não funciona conforme esperado. Desative a encriptação em todos os discos.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Ativar a encriptação em VMs existentes ou em execução com o CLI Azure
Utilize o comando [de encriptação az vm para](/cli/azure/vm/encryption#az-vm-encryption-enable) ativar a encriptação numa máquina virtual IaaS em funcionamento em Azure.

- **Criptografe um VM em execução:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Criptografe um VM em execução utilizando kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um VM IaaS, use o comando de [encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) A desativação da encriptação dos discos de dados numa VM do Windows quando os discos do SO e de dados foram encriptados não funciona conforme esperado. Desative a encriptação em todos os discos.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Usando o modelo de Gestor de Recursos

Pode ativar a encriptação do disco em VMs iaaS existentes ou em execução em Azure, utilizando o modelo de Gestor de [Recursos para encriptar um VM Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. No modelo de arranque rápido azure, clique em **Implementar para Azure**.

2. Selecione a subscrição, grupo de recursos, localização, configurações, termos legais e acordo. Clique em **Comprar** para ativar a encriptação no VM IaaS existente ou em execução.

A tabela seguinte lista os parâmetros do modelo do Gestor de Recursos para VMs existentes ou em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome do VM para executar a operação de encriptação. |
| keyVaultName | Nome do cofre chave para o que a chave BitLocker deve ser enviada. Pode obtê-lo utilizando `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` o cmdlet ou o comando Azure CLI`az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre chave|
|  keyEncryptionKeyURL | O URL da chave de encriptação,&lt;no formato https:// nome&gt;do cofre .vault.azure.net/key/&lt;nome-chave&gt;. Se não quiser utilizar um KEK, deixe este campo em branco. |
| volumeType | Tipo de volume em que a operação de encriptação é realizada. Os valores válidos são _OS,_ _Dados,_ e _Tudo._ 
| forceUpdateTag | Passe num valor único como um GUID cada vez que a operação precisa de ser executada à força. |
| redimensionamentoOSDisk | Se a divisória osso for redimensionada para ocupar o VHD total do SISTEMA antes de dividir o volume do sistema. |
| localização | Localização para todos os recursos. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Novos VMs IaaS criados a partir de VHD encriptado pelo cliente e chaves de encriptação

Neste cenário, pode criar um novo VM a partir de um VHD pré-encriptado e das chaves de encriptação associadas utilizando cmdlets PowerShell ou comandos CLI. 

Utilize as instruções em [Preparar um VHD windows pré-encriptado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Criptografe VMs com VHDs pré-encriptados com PowerShell Azure
Pode ativar a encriptação do disco no vHD encriptado utilizando o PowerShell cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo abaixo dá-lhe alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado
Pode [adicionar um novo disco a um VM windows utilizando powerShell](attach-disk-ps.md), ou [através do portal Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Ao utilizar o Powershell para encriptar um novo disco para VMs do Windows, deve ser especificada uma nova versão de sequência. A versão da sequência tem de ser única. O script abaixo gera um GUID para a versão de sequência. Em alguns casos, um disco de dados recentemente adicionado pode ser encriptado automaticamente pela extensão de encriptação do disco Azure. A encriptação automática ocorre geralmente quando o VM reinicia após a onda do novo disco. Isto é normalmente causado porque "All" foi especificado para o tipo de volume quando a encriptação do disco anteriormente funcionava no VM. Se a encriptação automática ocorrer num disco de dados recém-adicionado, recomendamos que volte a executar o cmdlet Set-AzVmDiskEncryptionExtension com nova versão de sequência. Se o seu novo disco de dados estiver encriptado automaticamente e não pretender ser encriptado, desencriptar primeiro todas as unidades e depois voltar a encriptar com uma nova versão de sequência especificando OS para o tipo de volume. 
  
 

-  **Criptografe um VM em execução:** O script abaixo inicializa as suas variáveis e executa o cmdlet set-AzVMDiskEncryptionExtension. O grupo de recursos, VM e cofre chave já deviam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, myVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores. Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui tanto os volumes de OS como os dados. Se pretender encriptar apenas o volume de OS, utilize "OS" para o parâmetro -VolumeType. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Criptografe um VM em execução utilizando kek:** Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui tanto os volumes de OS como os dados. Se pretender encriptar apenas o volume de OS, utilize "OS" para o parâmetro -VolumeType.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A sintaxe para o valor do parâmetro de cofre de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-encriptação-chave é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com o Azure CLI
 O comando Azure CLI fornecerá automaticamente uma nova versão de sequência para si quando executar o comando para ativar a encriptação. O exemplo usa "All" para o parâmetro do tipo volume. Pode ser necessário alterar o parâmetro do tipo volume para OS se estiver apenas a encriptar o disco OS. Ao contrário da sintaxe Powershell, o CLI não requer que o utilizador forneça uma versão de sequência única ao permitir a encriptação. O CLI gera automaticamente e utiliza o seu próprio valor de versão de sequência única.   

-  **Criptografe um VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografe um VM em execução utilizando kek:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Desativar a encriptação
Pode desativar a encriptação utilizando o Azure PowerShell, o Azure CLI ou com um modelo de Gestor de Recursos. A desativação da encriptação dos discos de dados numa VM do Windows quando os discos do SO e de dados foram encriptados não funciona conforme esperado. Desative a encriptação em todos os discos.

- **Desative a encriptação do disco com o Azure PowerShell:** Para desativar a encriptação, utilize o cmdlet [Desactivação-AzVMDiskCrypton.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Desative a encriptação com o Azure CLI:** Para desativar a encriptação, utilize o comando de [desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Desative a encriptação com um modelo de Gestor de Recursos:** 

    1. Clique em **'Implementar para Azure'** a partir da encriptação do [disco desativar no modelo VM do Windows.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Selecione a subscrição, grupo de recursos, localização, VM, tipo de volume, termos legais e acordo.
    3.  Clique em **Comprar** para desativar a encriptação do disco num VM do Windows em execução. 

## <a name="unsupported-scenarios"></a>Cenários não suportados

A encriptação do disco azure não funciona para os seguintes cenários, funcionalidades e tecnologia:

- Encriptar VM ou VMs básicos criados através do método clássico de criação VM.
- Encriptar VMs configurados com sistemas RAID baseados em software.
- Encriptar VMs configurados com espaços de armazenamento direto (S2D) ou versões do Windows Server antes de 2016 configurados com espaços de armazenamento do Windows.
- Integração com um sistema de gestão chave no local.
- Ficheiros Azure (sistema de ficheiros partilhados).
- Sistema de ficheiros de rede (NFS).
- Volumes dinâmicos.
- Os recipientes do Windows Server, que criam volumes dinâmicos para cada recipiente.
- Discos efémeros osso.
- Encriptação de sistemas de ficheiros partilhados/distribuídos como (mas não se limitando a) DFS, GFS, DRDB e CephFS.
- Mover um VMs encriptado para outra subscrição.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação do disco azure](disk-encryption-overview.md)
- [Scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
