---
title: Ativar a encriptação de disco para a clusters do Linux do Azure Service Fabric | Documentos da Microsoft
description: Este artigo descreve como ativar a encriptação de disco para nós de cluster do Azure Service Fabric no Linux com o Azure Resource Manager e o Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258766"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Ativar a encriptação de disco para nós do cluster do Azure Service Fabric no Linux 
> [!div class="op_single_selector"]
> * [Encriptação de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Encriptação de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Neste tutorial, aprenderá a ativar a encriptação de disco em nós do cluster do Azure Service Fabric no Linux. Terá de seguir estes passos para cada um dos tipos de nós e conjuntos de dimensionamento de máquina virtual. Para encriptar os nós, vamos utilizar a capacidade de encriptação de disco do Azure em conjuntos de dimensionamento de máquina virtual.

O guia abrange os seguintes tópicos:

* Conceitos-chave a ter em consideração quando define o ativar encriptação de disco no dimensionamento de máquinas virtuais de cluster do Service Fabric no Linux.
* Passos para ser seguidas antes de ativar a encriptação de disco no Service Fabric nós de cluster no Linux.
* Passos a seguir para ativar a encriptação de disco em nós de cluster do Service Fabric no Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

 **Self-registration**

A pré-visualização da encriptação de disco para o conjunto de dimensionamento de máquina virtual requer o registo automático. Utilize os passos seguintes:

1. Execute o seguinte comando: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que lê o estado *registado*. Pode verificar o estado ao executar o seguinte comando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Cofre de Chaves do Azure**

1. Crie um cofre de chaves na mesma subscrição e região, como o conjunto de dimensionamento. Em seguida, selecione o **EnabledForDiskEncryption** política no Cofre de chaves de acesso utilizando o cmdlet do PowerShell. Também pode definir a política através da IU de Cofre de chave no portal do Azure com o seguinte comando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Instale a versão mais recente dos [CLI do Azure](/cli/azure/install-azure-cli), que tem os novos comandos de encriptação.

3. Instale a versão mais recente dos [Azure SDK do Azure PowerShell](https://github.com/Azure/azure-powershell/releases) de versão. Seguem-se a máquina virtual conjunto de dimensionamento cmdlets do Azure Disk Encryption para ativar ([definir](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) a criptografia, obter ([obter](/powershell/module/az.compute/get-azvmssvmdiskencryption)) o estado de encriptação e remover ([desativar](/powershell/module/az.compute/disable-azvmssdiskencryption)) instância do conjunto de encriptação em escala.


| Comando | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou posterior | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou posterior | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou posterior | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários suportados para a encriptação de disco
* Encriptação para os conjuntos de dimensionamento de máquina virtual só é suportada para conjuntos de dimensionamento criados com discos geridos. Não é suportado para conjuntos de dimensionamento do disco nativo (ou não gerenciado).
* Encriptação e desativar encriptação são suportados para os volumes de dados e SO de conjuntos de dimensionamento de máquina virtual em Linux. 
* Operações de recriação de imagem e atualização de máquina virtual (VM) para conjuntos de dimensionamento de máquinas virtuais não são suportadas na pré-visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Criar um novo cluster e ativar a encriptação de disco

Utilize os seguintes comandos para criar um cluster e ativar a encriptação de disco utilizando um modelo Azure Resource Manager e um certificado autoassinado.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure  

Inicie sessão com os seguintes comandos:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Utilizar o modelo personalizado que já tem 

Se precisar de criar um modelo personalizado, é altamente recomendável que utilize um dos modelos no [exemplos de modelos de criação de cluster do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) página. 

Se já tiver um modelo personalizado, verifique se todos os parâmetros relacionados com o certificado no modelo e o ficheiro de parâmetros três são nomeados da seguinte forma. Certifique-se também de que os valores são nulos da seguinte forma:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Porque a encriptação de disco de dados apenas é suportada para conjuntos de dimensionamento de máquina virtual em Linux, tem de adicionar um disco de dados utilizando um modelo do Resource Manager. Atualize o modelo para aprovisionar de disco de dados da seguinte forma:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Eis o comando da CLI equivalente. Altere os valores nas instruções declare para os valores adequados. A CLI suporta todos os outros parâmetros que suporta o comando do PowerShell anterior.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Montar um disco de dados para uma instância do Linux
Antes de continuar com a encriptação num conjunto de dimensionamento de máquina virtual, certifique-se de que o disco de dados foi adicionado corretamente está montado. Inicie sessão para o cluster do Linux VM e execute o **LSBLK** comando. A saída deve mostrar esse disco de dados adicionados no **ponto de montagem** coluna.


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Implementar a aplicação num cluster do Service Fabric no Linux
Para implementar uma aplicação no seu cluster, siga os passos e as orientações em [início rápido: Implementar contentores do Linux no Service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Ativar a encriptação de disco para os conjuntos de dimensionamento de máquina virtual criada anteriormente
Para ativar a encriptação de disco para o dimensionamento de máquinas virtuais define que criou através dos passos anteriores, executados os seguintes comandos:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Confirmar se a encriptação de disco está ativada para um conjunto de dimensionamento no Linux
Para obter o estado de um conjunto de dimensionamento de máquina virtual inteira ou de qualquer instância num conjunto de dimensionamento, execute os seguintes comandos.
Além disso, pode iniciar sessão para o cluster do Linux VM e executar o **LSBLK** comando. A saída deve mostrar o disco de dados foi adicionado no **ponto de montagem** coluna e o **tipo** deve ler a coluna *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Desativar a encriptação de disco para um conjunto de dimensionamento no cluster do Service Fabric
Desative a encriptação de disco para um conjunto de dimensionamento, executando os comandos seguintes. Tenha em atenção que a desativação da encriptação de disco aplica-se para o conjunto de dimensionamento de máquina virtual inteira e não uma instância individual.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passos Seguintes
Neste momento, deve ter um cluster seguro e saber como ativar e desativar a encriptação de disco para nós de cluster do Service Fabric e conjuntos de dimensionamento de máquina virtual. Para obter diretrizes semelhantes em nós de cluster do Service Fabric no Linux, consulte [encriptação de disco para o Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
