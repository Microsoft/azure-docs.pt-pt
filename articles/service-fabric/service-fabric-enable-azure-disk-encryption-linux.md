---
title: Ativar a encriptação do disco para clusters Linux
description: Este artigo descreve como ativar a encriptação do disco para os nós de cluster de tecido de serviço Azure em Linux, utilizando o Azure Resource Manager e o Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78252820"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Ativar encriptação de disco para os nódos de cluster de tecido de serviço Azure em Linux 
> [!div class="op_single_selector"]
> * [Encriptação do disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Encriptação do disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Neste tutorial, você vai aprender como ativar a encriptação de disco em nós de cluster de tecido de serviço Azure em Linux. Você precisa seguir estes passos para cada um dos tipos de nós e conjuntos de balança de máquinas virtuais. Para encriptar os nós, usaremos a capacidade de encriptação do disco Azure em conjuntos de escala de máquinas virtuais.

O guia aborda os seguintes tópicos:

* Conceitos-chave a ter em conta ao permitir a encriptação do disco em conjuntos de escala de máquina virtual de cluster de tecido de serviço em Linux.
* Passos a seguir antes de permitir a encriptação do disco nos nós de cluster de tecido de serviço em Linux.
* Passos a seguir para permitir a encriptação do disco nos nós de cluster de tecido de serviço em Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

 **Auto-registo**

A pré-visualização da encriptação do disco para o conjunto de escala de máquina virtual requer auto-registo. Utilize os passos seguintes:

1. Execute o seguinte comando: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o estado se leia *registado*. Pode verificar o estado executando o seguinte comando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Crie um cofre chave na mesma subscrição e região que o conjunto de escala. Em seguida, selecione a política de acesso de **encriptação EnabledForDiskencryption** no cofre da chave utilizando o seu cmdlet PowerShell. Também pode definir a política utilizando o Cofre de Chaves UI no portal Azure com o seguinte comando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Instale a versão mais recente do [Azure CLI](/cli/azure/install-azure-cli), que possui os novos comandos de encriptação.

3. Instale a versão mais recente do [Azure SDK a partir do lançamento do Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) Seguem-se os cmdlets de encriptação de disco de azure para ativar[(conjunto)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)encriptação, recuperar ([obter)](/powershell/module/az.compute/get-azvmssvmdiskencryption)estado de encriptação e remover encriptação[(desativada)](/powershell/module/az.compute/disable-azvmssdiskencryption)na instância definida pela escala.


| Comando | Versão |  Origem  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou mais tarde | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou mais tarde | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou mais tarde | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou mais tarde | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou mais tarde | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou mais tarde | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários suportados para encriptação de discos
* A encriptação para conjuntos de escala de máquinas virtuais é suportada apenas para conjuntos de escala criados com discos geridos. Não é suportado para conjuntos de escala de disco nativo (ou não geridos).
* Tanto a encriptação como a encriptação desativação são suportadas para os volumes de SO e volumes de dados em conjuntos de escala de máquinas virtuais em Linux. 
* As operações de reimagem e atualização da máquina virtual (VM) não são suportadas na pré-visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Crie um novo cluster e permita a encriptação do disco

Utilize os seguintes comandos para criar um cluster e ativar a encriptação do disco utilizando um modelo de Gestor de Recursos Azure e um certificado auto-assinado.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure  

Inscreva-se com os seguintes comandos:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Use o modelo personalizado que já tem 

Se você precisar de autorizar um modelo personalizado, recomendamos vivamente que use um dos modelos na página de amostras de modelo de criação de cluster de tecido de [serviço Azure.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 

Se já tem um modelo personalizado, verifique duas vezes se os três parâmetros relacionados com o certificado no modelo e no ficheiro de parâmetros são nomeados da seguinte forma. Certifique-se também de que os valores são nulos da seguinte forma:

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

Como apenas a encriptação do disco de dados é suportada para conjuntos de escala de máquinas virtuais no Linux, deve adicionar um disco de dados utilizando um modelo de Gestor de Recursos. Atualize o seu modelo para a prestação de discos de dados da seguinte forma:

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

Aqui está o comando da CLI equivalente. Alterar os valores nas declarações declarando para os valores apropriados. O CLI suporta todos os outros parâmetros que o comando PowerShell anterior suporta.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Monte um disco de dados para uma instância Linux
Antes de continuar a encriptar num conjunto de escala de máquina virtual, certifique-se de que o disco de dados adicionado está corretamente montado. Inscreva-se no cluster Linux VM e execute o comando **LSBLK.** A saída deve mostrar que o disco de dados adicionado na coluna **Mount Point.**


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Implementar aplicação para um cluster de tecido de serviço em Linux
Para implementar uma aplicação no seu cluster, siga os passos e orientação no [Quickstart: Desdobre os recipientes Linux para o Tecido de Serviço](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Ativar a encriptação do disco para os conjuntos de escala de máquina virtual criados anteriormente
Para ativar a encriptação do disco para os conjuntos de escala de máquina virtual que criou através dos passos anteriores, execute os seguintes comandos:
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Validar se a encriptação do disco estiver ativada para uma escala de máquina virtual definida no Linux
Para obter o estado de um conjunto de escala de máquina virtual inteira ou qualquer instância num conjunto de escala, executar os seguintes comandos.
Além disso, pode iniciar sôm no VM do cluster Linux e executar o comando **LSBLK.** A saída deve mostrar o disco de dados adicionado na coluna **Mount Point,** e a coluna **Tipo** deve ler *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Desative a encriptação do disco para uma escala de máquina virtual definida num cluster de tecido de serviço
Desative a encriptação do disco para uma escala de máquina virtual definida executando os seguintes comandos. Note que a encriptação do disco incapacitante se aplica a todo o conjunto de escala de máquina virtual e não a uma instância individual.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passos seguintes
Neste ponto, você deve ter um cluster seguro e saber como ativar e desativar a encriptação do disco para nós de cluster de tecido de serviço e conjuntos de escala de máquina virtual. Para obter orientações semelhantes sobre os nós de cluster de tecido de serviço no Linux, consulte [a encriptação do disco para windows](service-fabric-enable-azure-disk-encryption-windows.md). 
