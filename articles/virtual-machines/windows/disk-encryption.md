---
title: Criptografia do lado do servidor do Azure Managed Disks-PowerShell
description: O armazenamento do Azure protege seus dados criptografando-os em repouso antes de mantê-los para clusters de armazenamento. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia de seus discos gerenciados ou pode usar chaves gerenciadas pelo cliente para gerenciar a criptografia com suas próprias chaves.
author: roygara
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 8833a70b88b59c59dd373f1cfa9535fd7205ceb5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837753"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Criptografia do lado do servidor de Azure Managed disks

O Azure Managed disks criptografa automaticamente os dados por padrão ao mantê-los para a nuvem. A criptografia do lado do servidor protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Os dados nos Managed disks do Azure são criptografados de forma transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2.   

A criptografia não afeta o desempenho dos discos gerenciados. Não há nenhum custo adicional para a criptografia.

Para obter mais informações sobre os módulos de criptografia subjacentes ao Azure Managed disks, consulte [API de criptografia: próxima geração](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Você pode contar com chaves gerenciadas por plataforma para a criptografia do seu disco gerenciado ou pode gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, poderá especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os dados em discos gerenciados. 

As seções a seguir descrevem cada uma das opções de gerenciamento de chaves mais detalhadamente.

## <a name="platform-managed-keys"></a>Chaves gerenciadas pela plataforma

Por padrão, os discos gerenciados usam chaves de criptografia gerenciadas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos gerenciados, instantâneos, imagens e novos dados gravados em discos gerenciados existentes são automaticamente criptografados em repouso com chaves gerenciadas pela plataforma. 

## <a name="customer-managed-keys"></a>Chaves geridas pelo cliente

Você pode optar por gerenciar a criptografia no nível de cada disco gerenciado com suas próprias chaves. A criptografia do lado do servidor para discos gerenciados com chaves gerenciadas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Você pode importar [suas chaves RSA](../../key-vault/key-vault-hsm-protected-keys.md) para seu Key Vault ou gerar novas chaves rsa no Azure Key Vault. O Azure Managed disks lida com a criptografia e a descriptografia de uma maneira totalmente transparente usando a [criptografia de envelope](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Ele criptografa dados usando uma DEK (chave de criptografia de dados) baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, que é, por sua vez, protegida usando suas chaves. Você precisa conceder acesso a discos gerenciados em seu Key Vault para usar suas chaves para criptografar e descriptografar o DEK. Isso permite o controle total de seus dados e chaves. Você pode desabilitar suas chaves ou revogar o acesso a discos gerenciados a qualquer momento. Você também pode auditar o uso da chave de criptografia com monitoramento Azure Key Vault para garantir que apenas os discos gerenciados ou outros serviços confiáveis do Azure estejam acessando suas chaves.

O diagrama a seguir mostra como os discos gerenciados usam Azure Active Directory e Azure Key Vault para fazer solicitações usando a chave gerenciada pelo cliente:

![Fluxo de trabalho de chaves gerenciadas pelo cliente de discos gerenciados](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A lista a seguir explica o diagrama ainda mais detalhadamente:

1. Um administrador de Azure Key Vault cria recursos do Key Vault.
1. O administrador do cofre de chaves importa suas chaves RSA para Key Vault ou gerar novas chaves RSA no Key Vault.
1. Esse administrador cria uma instância do recurso de conjunto de criptografia de disco, especificando uma ID de Azure Key Vault e uma URL de chave. O conjunto de criptografia de disco é um novo recurso introduzido para simplificar o gerenciamento de chaves para discos gerenciados. 
1. Quando um conjunto de criptografia de disco é criado, uma [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/overview.md) é criada no Azure Active Directory (AD) e associada ao conjunto de criptografia de disco. 
1. O administrador do cofre de chaves do Azure concede a permissão de identidade gerenciada para executar operações no cofre de chaves.
1. Um usuário da VM cria discos associando-os ao conjunto de criptografia de disco. O usuário da VM também pode habilitar a criptografia do lado do servidor com chaves gerenciadas pelo cliente para recursos existentes associando-as ao conjunto de criptografia de disco. 
1. Os discos gerenciados usam a identidade gerenciada para enviar solicitações para o Azure Key Vault.
1. Para ler ou gravar dados, o Managed disks envia solicitações para Azure Key Vault criptografar (encapsular) e descriptografar (desencapsular) a chave de criptografia de dados para executar criptografia e descriptografia dos dados. 

Para revogar o acesso às chaves gerenciadas pelo cliente, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso efetivamente bloqueia o acesso a todos os dados na conta de armazenamento, pois a chave de criptografia é inacessível pelo armazenamento do Azure.

### <a name="supported-regions"></a>Regiões suportadas

No momento, há suporte apenas para as seguintes regiões:

- Disponível como uma oferta GA nas regiões leste dos EUA, oeste dos EUA 2 e EUA Central do Sul.
- Disponível como uma visualização pública nas regiões EUA Central ocidental, leste dos EUA 2, Canadá central e Europa Setentrional.

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Somente [as chaves RSA "soft" e "Hard"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) do tamanho 2080 têm suporte, sem outras chaves ou tamanhos.
- Os discos criados a partir de imagens personalizadas que são criptografadas usando criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados usando as mesmas chaves gerenciadas pelo cliente e devem estar na mesma assinatura.
- Os instantâneos criados a partir de discos criptografados com criptografia do lado do servidor e chaves gerenciadas pelo cliente devem ser criptografados com as mesmas chaves gerenciadas pelo cliente.
- Imagens personalizadas criptografadas usando criptografia do lado do servidor e chaves gerenciadas pelo cliente não podem ser usadas na Galeria de imagens compartilhadas.
- Todos os recursos relacionados às chaves gerenciadas pelo cliente (cofres de chaves do Azure, conjuntos de criptografia de disco, VMs, discos e instantâneos) devem estar na mesma assinatura e região.
- Discos, instantâneos e imagens criptografadas com chaves gerenciadas pelo cliente não podem passar para outra assinatura.
- Se você usar o portal do Azure para criar o conjunto de criptografia de disco, não poderá usar instantâneos por enquanto.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurando seu Azure Key Vault e DiskEncryptionSet

1. Verifique se você instalou a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps)e se está conectado a uma conta do Azure no com Connect-AzAccount

1. Crie uma instância de Azure Key Vault e a chave de criptografia.

    Ao criar a instância de Key Vault, você deve habilitar a exclusão reversível e a proteção de limpeza. A exclusão reversível garante que a Key Vault mantenha uma chave excluída para um determinado período de retenção (padrão de 90 dias). A proteção de limpeza garante que uma chave excluída não possa ser excluída permanentemente até que o período de retenção se espuser. Essas configurações protegem você contra a perda de dados devido à exclusão acidental. Essas configurações são obrigatórias ao usar um Key Vault para criptografar discos gerenciados.

    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Crie uma instância de um DiskEncryptionSet. 
    
    ```powershell
    $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned

    $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
    ```

1.  Conceda ao recurso DiskEncryptionSet acesso ao cofre de chaves.

    > [!NOTE]
    > Pode levar alguns minutos para o Azure criar a identidade de seu DiskEncryptionSet em seu Azure Active Directory. Se você receber um erro como "não é possível localizar o objeto Active Directory" ao executar o comando a seguir, aguarde alguns minutos e tente novamente.
    
    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
     
    New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Criar uma VM usando uma imagem do Marketplace, criptografando o sistema operacional e os discos de dados com chaves gerenciadas pelo cliente

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Criar um disco vazio criptografado usando a criptografia do lado do servidor com chaves gerenciadas pelo cliente e anexá-lo a uma VM

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $rgName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>Criptografe discos geridos não ligados existentes 

Os seus discos existentes não devem ser ligados a um VM em execução para que os criptografe utilizando o seguinte script:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Criar um conjunto de dimensionamento de máquinas virtuais usando uma imagem do Marketplace, criptografando o sistema operacional e os discos de dados com chaves gerenciadas pelo cliente

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura chaves gerenciadas pelo cliente, uma identidade gerenciada é automaticamente atribuída aos seus recursos nos bastidores. Se, subsequentemente, você mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure AD para outro, a identidade gerenciada associada a discos gerenciados não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte [transferindo uma assinatura entre diretórios do Azure ad](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente dependem de identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Quando você configura chaves gerenciadas pelo cliente, uma identidade gerenciada é automaticamente atribuída aos seus recursos nos bastidores. Se, subsequentemente, você mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure AD para outro, a identidade gerenciada associada a discos gerenciados não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, consulte [transferindo uma assinatura entre diretórios do Azure ad](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Criptografia do lado do servidor versus criptografia de disco do Azure

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) aproveita o recurso [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e o recurso [DM-cript](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para criptografar discos gerenciados com chaves gerenciadas pelo cliente na VM convidada.  A criptografia do lado do servidor com chaves gerenciadas pelo cliente melhora o ADE, permitindo que você use qualquer tipo de sistema operacional e imagens para suas VMs criptografando os dados no serviço de armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Explore os modelos de Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre chave Azure?](../../key-vault/key-vault-overview.md)
- [Replicar máquinas com discos habilitados para chaves gerenciadas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastre de VMs do VMware para o Azure com o PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para VMs do Hyper-V usando o PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
