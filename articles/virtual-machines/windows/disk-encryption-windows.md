---
title: Cenários do Azure Disk Encryption em VMs do Windows
description: Este artigo fornece instruções para permitir a encriptação do disco do Azure do Microsoft para VMs windows para vários cenários
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 925b685cd5b28c469e4b918fd0347d04ba5860d8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443802"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Cenários do Azure Disk Encryption em VMs do Windows

A encriptação do disco Azure para máquinas virtuais do Windows (VMs) utiliza a funcionalidade BitLocker do Windows para fornecer encriptação completa do disco de segurança e do disco de dados. Além disso, fornece encriptação do disco temporário quando o parâmetro VolumeType é All.

A Azure Disk Encryption está [integrada com o Azure Key Vault](disk-encryption-key-vault.md) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco. Para uma visão geral do serviço, consulte [a encriptação do disco Azure para VMs do Windows](disk-encryption-overview.md).

Só é possível aplicar encriptação de discos a máquinas virtuais de [tamanhos e sistemas operativos VM suportados.](disk-encryption-overview.md#supported-vms-and-operating-systems) Deve também cumprir os seguintes pré-requisitos:

- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM. Consulte [a encriptação do disco Azure com Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter mais detalhes. 
>
> - Deve [tirar uma fotografia](snapshot-copy-managed-disk.md) e/ou criar uma cópia de segurança antes de os discos serem encriptados. As cópias de segurança garantem que uma opção de recuperação é possível se ocorrer uma falha inesperada durante a encriptação. VMs com discos geridos requerem uma cópia de segurança antes da encriptação ocorrer. Uma vez feita uma cópia de segurança, pode utilizar o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para encriptar os discos geridos especificando o parâmetro -skipVmBackup. Para obter mais informações sobre como fazer o back up e restaurar VMs encriptados, consulte [Back up e restaure O VM encriptado](../../backup/backup-azure-vms-encryption.md). 
>
> - Encriptar ou desativar a encriptação pode fazer com que um VM reinicie.

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e ligue-se ao Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Ativar a encriptação num VM do Windows existente ou em execução
Neste cenário, pode ativar a encriptação utilizando o modelo de Gestor de Recursos, cmdlets PowerShell ou comandos CLI. Se precisar de informações sobre esquemas para a extensão da máquina virtual, consulte a Encriptação do Disco Azure para o artigo [de extensão do Windows.](../extensions/azure-disk-enc-windows.md)

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Ativar a encriptação em VMs existentes ou em execução com Azure PowerShell 
Utilize o [cmdlet Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) para ativar a encriptação numa máquina virtual IaaS em funcionamento em Azure. 

-  **Criptografe um VM em execução:** O script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM e cofre-chave já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores.

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
- **Criptografe um VM em execução utilizando KEK:** 

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
   > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um IaaS VM, utilize o [cmdlet Get-AzVmDiskEncrypationStatus.](/powershell/module/az.compute/get-azvmdiskencryptionstatus) 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Desativar a encriptação do disco:** Para desativar a encriptação, utilize o [cmdlet de desencriptação Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) A desativação da encriptação dos discos de dados numa VM do Windows quando os discos do SO e de dados foram encriptados não funciona conforme esperado. Em vez disso, desative a encriptação em todos os discos.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Ativar a encriptação em VMs existentes ou em execução com o CLI Azure
Utilize a [encriptação az vm ativar](/cli/azure/vm/encryption#az-vm-encryption-enable) o comando para ativar a encriptação numa máquina virtual IaaS em execução em Azure.

- **Criptografe um VM em execução:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Criptografe um VM em execução utilizando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Verifique se os discos estão encriptados:** Para verificar o estado de encriptação de um IaaS VM, utilize o comando [do programa de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-show) 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Desativar a encriptação:** Para desativar a encriptação, utilize o comando [de desativação de encriptação az vm.](/cli/azure/vm/encryption#az-vm-encryption-disable) A desativação da encriptação dos discos de dados numa VM do Windows quando os discos do SO e de dados foram encriptados não funciona conforme esperado. Em vez disso, desative a encriptação em todos os discos.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Usando o modelo de gestor de recursos

Pode ativar a encriptação do disco em VMs do IaaS Windows existente ou em execução, utilizando o [modelo de Gestor de Recursos para encriptar um VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. No modelo de arranque rápido Azure, clique em **Implementar para Azure**.

2. Selecione a subscrição, grupo de recursos, localização, configurações, termos legais e acordo. Clique em **Comprar** para ativar a encriptação no IaaS VM existente ou em execução.

A tabela que se segue lista os parâmetros do modelo do Gestor de Recursos para os VM existentes ou em execução:

| Parâmetro | Descrição |
| --- | --- |
| vmName | Nome do VM para executar a operação de encriptação. |
| keyVaultName | Nome do cofre chave para o que a chave BitLocker deve ser carregada. Você pode obtê-lo usando o cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` ou o comando Azure CLI `az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | Nome do grupo de recursos que contém o cofre chave|
|  keyEncryptionKeyURL | O URL da chave de encriptação, no formato https:// &lt; nome de &gt; chavevault .vault.azure.net/key/ &lt; nome-chave &gt; . Se não desejar utilizar um KEK, deixe este campo em branco. |
| volumeType | Tipo de volume em que a operação de encriptação é executada. Valores válidos são _OS,_ _Dados_ e _Todos._ 
| forceUpdateTag | Passe um valor único como um GUID sempre que a operação precisa de ser executada à força. |
| redimensionarOSDisco | Se a partição de SO for redimensionada para ocupar o VHD completo do SO antes de dividir o volume do sistema. |
| localização | Localização de todos os recursos. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Ativar a encriptação em discos NVMe para Lsv2 VMs

Este cenário descreve a ativação da encriptação do disco Azure em discos NVMe para VMs da série Lsv2.  A série Lsv2 apresenta armazenamento NVMe local. Os Discos NVMe locais são temporários e os dados serão perdidos nestes discos se parar/negociar o seu VM (Ver: [série Lsv2).](../lsv2-series.md)

Para ativar a encriptação nos discos NVMe:

1. Inicialize os discos NVMe e crie volumes NTFS.
1. Ativar a encriptação no VM com o parâmetro VolumeType definido para Todos. Isto permitirá a encriptação de todos os discos de SO e dados, incluindo volumes apoiados por discos NVMe. Para obter informações, consulte [Ativar a encriptação num VM do Windows existente ou em execução](#enable-encryption-on-an-existing-or-running-windows-vm).

A encriptação persistirá nos discos NVMe nos seguintes cenários:
- Reinicialização do VM
- Conjunto de escala de máquina virtual reimagem
- Troca de OS

Os discos NVMe serão não ininitializados os seguintes cenários:

- Iniciar VM após alocação de negócios
- Cura de serviço
- Backup

Nestes cenários, os discos NVMe precisam de ser inicializados após o início do VM. Para ativar a encriptação nos discos NVMe, executar o comando para ativar a Encriptação do Disco Azure novamente após a inicialização dos discos NVMe.

Além dos cenários listados na secção [Cenários Não Suportados,](#unsupported-scenarios) a encriptação dos discos NVMe não é suportada para:

- VMs encriptados com Encriptação do Disco Azure com AAD (versão anterior)
- Discos NVMe com espaços de armazenamento
- Recuperação do sítio azul de SKUs com discos NVMe (ver matriz de suporte para recuperação de [desastres Azure VM entre regiões de Azure: Máquinas replicadas - armazenamento](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Novos VMs iaas criados a partir de VHD encriptados pelo cliente e chaves de encriptação

Neste cenário, pode criar um novo VM a partir de um VHD pré-encriptado e das chaves de encriptação associadas utilizando comandos PowerShell ou CLI. 

Utilize as instruções em [Preparar um VHD do Windows pré-encriptado](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Após a criação da imagem, pode utilizar os passos na secção seguinte para criar um Azure VM encriptado.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Criptografe VMs com VHDs pré-encriptados com Azure PowerShell
Pode ativar a encriptação do disco no seu VHD encriptado utilizando o [Conjunto de Cmdlet PowerShell Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). O exemplo abaixo dá-lhe alguns parâmetros comuns. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Ativar a encriptação num disco de dados recém-adicionado
Pode [adicionar um novo disco a um Windows VM utilizando o PowerShell,](attach-disk-ps.md)ou [através do portal Azure](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Ativar a encriptação num disco recém-adicionado com a Azure PowerShell
 Ao utilizar o PowerShell para encriptar um novo disco para VMs do Windows, deve ser especificada uma nova versão de sequência. A versão da sequência tem de ser única. O script abaixo gera um GUID para a versão de sequência. Em alguns casos, um disco de dados recém-adicionado pode ser encriptado automaticamente pela extensão de encriptação do disco Azure. A encriptação automática ocorre geralmente quando o VM reinicia após o novo disco estar online. Isto é normalmente causado porque "All" foi especificado para o tipo de volume quando a encriptação do disco foi previamente correu no VM. Se ocorrer uma encriptação automática num disco de dados recém-adicionado, recomendamos que volte a executar o cmdlet Set-AzVmDiskEncryptionExtension com nova versão de sequência. Se o seu novo disco de dados estiver encriptado automaticamente e não desejar ser encriptado, desencriptar todas as unidades primeiro, então reencrimar com uma nova versão de sequência especificando o SO para o tipo de volume. 
  
 

-  **Criptografe um VM em execução:** O script abaixo inicializa as suas variáveis e executa o Set-AzVMDiskEncryptionExtension cmdlet. O grupo de recursos, VM e cofre-chave já deveriam ter sido criados como pré-requisitos. Substitua o MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM e MySecureVault com os seus valores. Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui volumes de SO e Dados. Se quiser apenas encriptar o volume de SO, utilize "OS" para o parâmetro -VolumeType. 

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
- **Criptografe um VM em execução utilizando KEK:** Este exemplo utiliza "All" para o parâmetro -VolumeType, que inclui volumes de SO e Dados. Se quiser apenas encriptar o volume de SO, utilize "OS" para o parâmetro -VolumeType.

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
    > A sintaxe para o valor do parâmetro de chave-chave de encriptação de disco é a cadeia de identificação completa: /subscrições/[subscrição-id-guid]/resourceGroups/[resource-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A sintaxe para o valor do parâmetro chave-chave de encriptação é o URI completo para o KEK como em: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Ativar a encriptação num disco recém-adicionado com O Azure CLI
 O comando Azure CLI fornecerá automaticamente uma nova versão de sequência para si quando executar o comando para ativar a encriptação. O exemplo utiliza "All" para o parâmetro do tipo volume. Pode ter de alterar o parâmetro do tipo volume para SISTEMA se estiver apenas a encriptar o disco de so. Em contraste com a sintaxe PowerShell, o CLI não requer que o utilizador forneça uma versão de sequência única ao permitir a encriptação. O CLI gera e utiliza automaticamente o seu próprio valor exclusivo de versão de sequência.   

-  **Criptografe um VM em execução:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Criptografe um VM em execução utilizando KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Desativar a encriptação
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Cenários não suportados

A Azure Disk Encryption não funciona para os seguintes cenários, funcionalidades e tecnologia:

- Encriptação de VM ou VMs básicos criados através do método clássico de criação de VM.
- Encriptação de VMs configurados com sistemas RAID baseados em software.
- Encriptação de VMs configurados com os espaços de armazenamento direto (S2D) ou versões do Windows Server antes de 2016 configurados com espaços de armazenamento do Windows.
- Integração com um sistema de gestão chave no local.
- Ficheiros Azure (sistema de ficheiros partilhados).
- Sistema de ficheiros de rede (NFS).
- Volumes dinâmicos.
- Recipientes do Servidor Windows, que criam volumes dinâmicos para cada recipiente.
- Discos efémeros de OS.
- Encriptação de sistemas de ficheiros partilhados/distribuídos como (mas não limitados a) DFS, GFS, DRDB e CephFS.
- Mover um VM encriptado para outra subscrição ou região.
- Criar uma imagem ou instantâneo de um VM encriptado e usá-lo para implementar VMs adicionais.
- VMs da série M com discos de acelerador de escrita.
- Aplicação de ADE a um VM que tenha discos encriptados com [encriptação do lado do servidor com teclas geridas pelo cliente](../disk-encryption.md) (SSE + CMK). Aplicar SSE + CMK a um disco de dados num VM encriptado com ADE também é um cenário não suportado.
- Migrar um VM que é encriptado com ADE, ou **que já** foi encriptado com ADE, para [encriptação do lado do servidor com teclas geridas pelo cliente](../disk-encryption.md).
- Encriptar VMs em clusters de failover.
- Encriptação dos [discos ultra Azure](../disks-enable-ultra-ssd.md).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da encriptação do disco Azure](disk-encryption-overview.md)
- [Scripts de exemplo do Azure Disk Encryption](disk-encryption-sample-scripts.md)
- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)