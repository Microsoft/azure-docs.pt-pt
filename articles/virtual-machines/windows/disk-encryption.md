---
title: Encriptação do lado do servidor de Discos Geridos Azure - PowerShell
description: O Azure Storage protege os seus dados encriptando-os em repouso antes de persistir nos clusters de Armazenamento. Pode contar com chaves geridas pela Microsoft para a encriptação dos seus discos geridos, ou pode utilizar chaves geridas pelo cliente para gerir a encriptação com as suas próprias chaves.
author: roygara
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 0541b12d73cc5b5f7fdf713c759069e2ecbd8c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299636"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Encriptação do lado do servidor dos discos geridos pelo Azure

Os discos geridos pelo Azure encriptam automaticamente os seus dados por padrão quando os persistem na nuvem. A encriptação do lado do servidor protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade. Os dados em discos geridos pelo Azure são encriptados de forma transparente utilizando [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits , uma das cifras de blocos mais fortes disponíveis, e é compatível com O FIPS 140-2.

A encriptação não afeta o desempenho dos discos geridos. Não há custos adicionais para a encriptação.

Para obter mais informações sobre os módulos criptográficos subjacentes aos discos geridos pelo Azure, consulte [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Pode confiar em chaves geridas pela plataforma para a encriptação do seu disco gerido, ou pode gerir a encriptação usando as suas próprias chaves. Se optar por gerir a encriptação com as suas próprias chaves, pode especificar uma *chave gerida pelo cliente* para encriptar e desencriptar todos os dados em discos geridos. 

As seguintes secções descrevem cada uma das opções para a gestão chave em maior detalhe.

## <a name="platform-managed-keys"></a>Chaves geridas pela plataforma

Por padrão, os discos geridos utilizam chaves de encriptação geridas pela plataforma. A partir de 10 de junho de 2017, todos os novos discos geridos, instantâneos, imagens e novos dados escritos aos discos geridos existentes são automaticamente encriptados em repouso com chaves geridas pela plataforma.

## <a name="customer-managed-keys"></a>Chaves geridas pelo cliente

Pode optar por gerir a encriptação ao nível de cada disco gerido, com as suas próprias chaves. A encriptação do lado do servidor para discos geridos com chaves geridas pelo cliente oferece uma experiência integrada com o Azure Key Vault. Pode importar [as suas chaves RSA](../../key-vault/key-vault-hsm-protected-keys.md) para o seu Cofre chave ou gerar novas chaves RSA no Cofre de Chaves Azure. Os discos geridos pelo Azure tratam da encriptação e da desencriptação de uma forma totalmente transparente usando [encriptação](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)de envelopes. Encripta dados utilizando uma chave de encriptação de dados baseada em [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (DEK), que está, por sua vez, protegida usando as suas chaves. Tem de conceder acesso a discos geridos no seu Cofre de Chaves para utilizar as suas chaves para encriptar e desencriptar o DEK. Isto permite-lhe o controlo total dos seus dados e chaves. Pode desativar as chaves ou revogar o acesso aos discos geridos a qualquer momento. Também pode auditar o uso da chave de encriptação com monitorização do Cofre de Chaves Azure para garantir que apenas discos geridos ou outros serviços Azure fidedignos acedem às suas chaves.

Para SSDs premium, SSDs padrão e HDDs padrão: Quando desativar ou eliminar a sua chave, quaisquer VMs com discos que utilizem essa tecla desligam-se automaticamente. Depois disso, os VMs não serão utilizáveis a menos que a chave esteja ativada novamente ou atribua uma nova chave.

Para discos ultra, quando desativa ou elimina uma chave, quaisquer VMs com discos ultra que utilizem a tecla não desligam automaticamente. Assim que deslocar e reiniciar os VMs, os discos deixarão de usar a chave e os VMs não voltarão a funcionar. Para voltar a colocar os VMs online, deve atribuir uma nova tecla ou ativar a chave existente.

O diagrama seguinte mostra como os discos geridos utilizam o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

![Fluxo de trabalho gerido pelo disco e chaves geridas pelo cliente. Um administrador cria um Cofre de Chave Azure, depois cria um conjunto de encriptação de disco e configura o conjunto de encriptação do disco. O Conjunto está associado a um VM, que permite ao disco fazer uso da AD Azure para autenticar](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


A seguinte lista explica o diagrama com mais detalhes:

1. Um administrador do Cofre Chave Azure cria recursos chave do cofre.
1. O cofre chave importa as chaves RSA para o Cofre chave ou gera novas chaves RSA no Cofre chave.
1. Este administrador cria uma instância de recurso de Conjunto de Encriptação de Disco, especificando um ID do cofre de chaves Azure e um URL chave. O Conjunto de Encriptação do Disco é um novo recurso introduzido para simplificar a gestão da chave para discos geridos. 
1. Quando um conjunto de encriptação de disco é criado, uma [identidade gerida atribuída](../../active-directory/managed-identities-azure-resources/overview.md) pelo sistema é criada em Azure Ative Directory (AD) e associada ao conjunto de encriptação do disco. 
1. O administrador do cofre chave Azure concede então a permissão de identidade gerida para realizar operações no cofre chave.
1. Um utilizador vM cria discos associando-os ao conjunto de encriptação do disco. O utilizador VM também pode ativar a encriptação do lado do servidor com chaves geridas pelo cliente para os recursos existentes, associando-os com o conjunto de encriptação do disco. 
1. Os discos geridos usam a identidade gerida para enviar pedidos para o Cofre chave Azure.
1. Para ler ou escrever dados, os discos geridos enviam pedidos ao Azure Key Vault para encriptar (embrulhar) e desencriptar (desembrulhar) a chave de encriptação de dados para realizar encriptação e desencriptação dos dados. 

Para revogar o acesso às chaves geridas pelo cliente, consulte [o Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [o Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os dados da conta de armazenamento, uma vez que a chave de encriptação é inacessível pelo Armazenamento Azure.

### <a name="supported-regions"></a>Regiões suportadas

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrições

Por enquanto, as chaves geridas pelo cliente têm as seguintes restrições:

- Se esta função estiver ativada para o disco, não poderá desativá-la.
    Se precisa de trabalhar em torno disto, tem de [copiar todos os dados](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) para um disco gerido completamente diferente que não esteja a utilizar chaves geridas pelo cliente.
- Apenas são suportadas [teclas RSA "macias" e "duras"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) do tamanho 2080, sem outras teclas ou tamanhos.
- Os discos criados a partir de imagens personalizadas que são encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptados utilizando as mesmas chaves geridas pelo cliente e devem estar na mesma subscrição.
- As imagens criadas a partir de discos que são encriptados com encriptação do lado do servidor e chaves geridas pelo cliente devem ser encriptadas com as mesmas chaves geridas pelo cliente.
- Imagens personalizadas encriptadas utilizando encriptação do lado do servidor e chaves geridas pelo cliente não podem ser usadas na galeria de imagens partilhadas.
- Todos os recursos relacionados com as suas chaves geridas pelo cliente (Cofres chave Azure, conjuntos de encriptação de discos, VMs, discos e instantâneos) devem estar na mesma subscrição e região.
- Discos, instantâneos e imagens encriptadas com chaves geridas pelo cliente não podem mover-se para outra subscrição.
- Se utilizar o portal Azure para criar o seu conjunto de encriptação de discos, não pode utilizar instantâneos por enquanto.
- Os discos geridos encriptados utilizando chaves geridas pelo cliente também não podem ser encriptados com encriptação do disco Azure.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configuração do seu cofre de chave azure e conjunto de encriptação de disco

1. Certifique-se de que instalou a mais recente [versão do PowerShell do Azure](/powershell/azure/install-az-ps), e está inscrito numa conta Azure com o Connect-AzAccount

1. Crie uma instância de Cofre chave Azure e chave de encriptação.

    Ao criar a instância Key Vault, deve ativar a proteção de eliminação suave e purga. A eliminação suave garante que o Cofre-Chave detém uma chave eliminada durante um determinado período de retenção (padrão de 90 dias). A proteção da purga garante que uma chave eliminada não pode ser eliminada permanentemente até que o período de retenção caduque. Estas definições protegem-no de perder dados devido à eliminação acidental. Estas definições são obrigatórias quando se utiliza um Cofre chave para encriptar discos geridos.

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

1.    Crie uma instância de um DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Conceda o acesso ao recurso DiskEncryptionSet ao cofre da chave.

        > [!NOTE]
        > Pode levar alguns minutos para o Azure criar a identidade do seu DiskEncryptionSetSet no seu Diretório Ativo Azure. Se tiver um erro como "Não encontrar o objeto de Diretório Ativo" ao executar o seguinte comando, aguarde alguns minutos e tente novamente.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um VM utilizando uma imagem do Marketplace, encriptando o OS e os discos de dados com chaves geridas pelo cliente

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Crie um disco vazio encriptado utilizando encriptação do lado do servidor com chaves geridas pelo cliente e fixe-o a um VM

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

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

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

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Crie um conjunto de escala de máquina virtual utilizando uma imagem do Marketplace, encriptando o OS e os discos de dados com chaves geridas pelo cliente

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
> As chaves geridas pelo cliente baseiam-se em identidades geridas para os recursos Azure, uma característica do Azure Ative Directory (Azure AD). Quando configura as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o disco gerido de um diretório DaD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para mais informações, consulte a Transferência de uma subscrição entre os [diretórios da AD azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves geridas pelo cliente baseiam-se em identidades geridas para os recursos Azure, uma característica do Azure Ative Directory (Azure AD). Quando configura as chaves geridas pelo cliente, uma identidade gerida é automaticamente atribuída aos seus recursos sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o disco gerido de um diretório DaD Azure para outro, a identidade gerida associada aos discos geridos não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para mais informações, consulte a Transferência de uma subscrição entre os [diretórios da AD azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Encriptação do lado do servidor versus encriptação do disco Azure

[A encriptação](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) do disco Azure aproveita a funcionalidade [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para encriptar discos geridos com chaves geridas pelo cliente dentro do VM convidado.  A encriptação do lado do servidor com as chaves geridas pelo cliente melhora no ADE, permitindo-lhe utilizar quaisquer tipos e imagens de SO para os seus VMs encriptando dados no serviço de Armazenamento.

## <a name="next-steps"></a>Passos seguintes

- [Explore os modelos do Gestor de Recursos Azure para criar discos encriptados com chaves geridas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o cofre de chave do Azure?](../../key-vault/key-vault-overview.md)
- [Replicar máquinas com chaves geridas pelo cliente discos habilitados](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastres de VMware VMs para Azure com powerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para VMs hiper-V utilizando o PowerShell e o Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
