---
title: Ativar a encriptação do disco para os nós geridos do cluster de tecido de serviço (pré-visualização)
description: Saiba como ativar a encriptação do disco para o Azure Service Fabric gerido os nós de cluster no Windows utilizando um modelo ARM.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100642510"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Ativar a encriptação do disco para os nós geridos do cluster de tecido de serviço (pré-visualização)

Neste guia, irá aprender a ativar a encriptação de discos nos nós de cluster geridos do Service Fabric no Windows utilizando a capacidade de encriptação do [disco Azure](../virtual-machines/windows/disk-encryption-overview.md) para [conjuntos de escala de máquinas virtuais](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) através dos modelos Azure Resource Manager (ARM).

> [!IMPORTANT]
> A pré-visualização da encriptação do disco de definição de escala de máquina virtual ainda não suporta a atualização ou reimagem da imagem. Não utilize se precisar de atualizar a sua imagem de SO.

## <a name="register-for-azure-disk-encryption"></a>Registre-se para encriptação do disco Azure

A pré-visualização da encriptação do disco para o conjunto de escala de máquina virtual requer auto-registo. Execute o seguinte comando:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

Verificar o estado do registo em execução:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Assim que o estado se alterar para *Registrado,* está pronto para prosseguir.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Provisionar um Cofre-Chave para encriptação de discos

A encriptação do disco Azure requer um Cofre de Chave Azure para controlar e gerir chaves e segredos de encriptação de discos. O seu cluster gerido key Vault e Service Fabric deve residir na mesma região de Azure e subscrição. Enquanto estes requisitos forem cumpridos, pode utilizar um cofre de chaves novo ou existente, permitindo-o para encriptação de discos.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Criar Cofre de Chaves com encriptação de disco ativada

Executar os seguintes comandos para criar um novo Key Vault para encriptação de disco. Certifique-se de que a região para o seu Key Vault é [suportada para clusters geridos por Tecido de Serviço](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) e está na mesma região que o seu cluster.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Atualizar o Cofre de Chaves existente para ativar a encriptação do disco

Executar os seguintes comandos para ativar a encriptação do disco para um Cofre de Chaves existente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>Atualizar os ficheiros do modelo e dos parâmetros para encriptação do disco

O passo seguinte irá acompanhá-lo através das alterações de modelo necessárias para permitir a encriptação do disco num [cluster gerido existente](tutorial-managed-cluster-deploy.md). Alternadamente, pode implementar um novo cluster gerido por Tecido de Serviço com encriptação de disco ativada com este modelo: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Adicione os seguintes parâmetros ao modelo, substituindo a sua própria subscrição, nome de grupo de recursos e nome do cofre em `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Em seguida, adicione a `AzureDiskEncryption` extensão VM aos tipos de nó de cluster geridos no modelo:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Por fim, atualize o ficheiro de parâmetros, substituindo a sua própria subscrição, grupo de recursos e nome de cofre chave no *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>Implementar e verificar as alterações

Assim que estiver pronto, desloque as alterações para ativar a encriptação do disco no seu cluster gerido.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

Pode verificar o estado de encriptação do disco no conjunto de escala subjacente de um tipo de nó utilizando o `Get-AzVmssDiskEncryption` comando. Primeiro, terá de encontrar o nome do grupo de recursos de apoio gerido do seu cluster (contendo a rede virtual subjacente, o balanceador de carga, o IP público, o NSG, os conjuntos de escalas e as contas de armazenamento). Certifique-se de modificar `VmssName` para qualquer nome do tipo de nó de cluster que deseja verificar (conforme especificado no seu modelo de implementação).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

A saída deve parecer semelhante a esta:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Passos seguintes

[Amostra: Cluster gerido pelo Tecido de Serviço SKU padrão, 1 tipo de nó com encriptação de disco ativado](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Encriptação do disco Azure para VMs windows](../virtual-machines/windows/disk-encryption-overview.md)

[Criptografe conjuntos de escala de máquina virtual com O Gestor de Recursos Azure](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
