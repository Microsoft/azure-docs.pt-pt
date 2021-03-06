---
title: Ativar encriptação de ponta a ponta utilizando encriptação no anfitrião - Azure CLI - discos geridos
description: Utilize encriptação no anfitrião para ativar a encriptação de ponta a ponta nos discos geridos Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 45cdb9217eebf6e3129718a96d9f7b72a3ab62b3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533612"
---
# <a name="use-the-azure-cli-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Utilize o CLI Azure para ativar a encriptação de ponta a ponta utilizando encriptação no anfitrião

Quando ativa a encriptação no anfitrião, os dados armazenados no anfitrião VM são encriptados em repouso e os fluxos encriptados para o serviço de Armazenamento. Para obter informações conceptuais sobre encriptação no anfitrião, bem como outros tipos de encriptação de discos [geridos, consulte a Encriptação no anfitrião - Encriptação de ponta a ponta para os seus dados VM](../disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

## <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-vm-sizes"></a>Tamanhos de VM suportados

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

A lista completa dos tamanhos de VM suportados pode ser puxada programáticamente. Para aprender a recuperá-los programáticamente, consulte a secção [de tamanhos VM suportados por Finding.](#finding-supported-vm-sizes)
A atualização do tamanho do VM resultará em validação para verificar se o novo tamanho VM suporta a funcionalidade EncryptionAtHost.

## <a name="prerequisites"></a>Pré-requisitos

Tem de ativar a funcionalidade da sua subscrição antes de utilizar a propriedade EncryptionAtHost para o seu VM/VMSS. Siga os passos abaixo para ativar a funcionalidade para a sua subscrição:

1.  Execute o seguinte comando para registar a funcionalidade para a sua subscrição

    ```azurecli
    az feature register --namespace Microsoft.Compute --name EncryptionAtHost
    ```
 
2.  Verifique se o estado de registo está registado (demora alguns minutos) usando o comando abaixo antes de experimentar a funcionalidade.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name EncryptionAtHost
    ```


### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Criar um cofre de chave azul e diskEncryptionSet

Uma vez ativada a funcionalidade, terá de configurar um Cofre de Teclas Azure e um DiskEncryptionSet, se ainda não o fez.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>Exemplos

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Crie um VM com encriptação no anfitrião ativado com teclas geridas pelo cliente. 

Crie um VM com discos geridos utilizando o recurso URI do DiskEncrypationSet criado anteriormente para encriptar cache de SISTEMA e discos de dados com chaves geridas pelo cliente. Os discos temporários são encriptados com chaves geridas pela plataforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Crie um VM com encriptação no anfitrião ativado com teclas geridas pela plataforma. 

Crie um VM com encriptação no anfitrião habilitado a encriptar cache de discos de OS/dados e discos temporários com teclas geridas pela plataforma. 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>Atualize um VM para permitir a encriptação no anfitrião. 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>Verifique o estado da encriptação no anfitrião para obter um VM

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>Crie um conjunto de escala de máquina virtual com encriptação no anfitrião ativado com teclas geridas pelo cliente. 

Crie um conjunto de escala de máquina virtual com discos geridos utilizando o recurso URI do DiskEncrypationSet criado anteriormente para encriptar cache de SISTEMA e discos de dados com teclas geridas pelo cliente. Os discos temporários são encriptados com chaves geridas pela plataforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>Crie um conjunto de escala de máquina virtual com encriptação no anfitrião ativado com teclas geridas pela plataforma. 

Crie um conjunto de escala de máquina virtual com encriptação no anfitrião habilitado a encriptar cache de discos de OS/dados e discos temporários com teclas geridas pela plataforma. 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>Atualize uma escala de máquina virtual definida para permitir a encriptação no anfitrião. 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>Verifique o estado da encriptação no anfitrião para obter um conjunto de escala de máquina virtual

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>Encontrar tamanhos de VM suportados

Os tamanhos VM legacy não são suportados. Pode encontrar a lista de tamanhos VM suportados por:

Chamando a [API de Recursos Skus](/rest/api/compute/resourceskus/list) e verificando se a `EncryptionAtHostSupported` capacidade está definida para **True**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

Ou, chamando o [cmdlet Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) PowerShell.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que criou e configura estes recursos, pode usá-los para proteger os seus discos geridos. O link que se segue contém scripts de exemplo, cada um com um cenário respetivo, que pode utilizar para proteger os seus discos geridos.

[Amostras de modelo do gestor de recursos Azure](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
