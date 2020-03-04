---
title: Ativar encriptação de disco para clusters Linux
description: Este artigo descreve como ativar a encriptação do disco para os nós de cluster azure service fabric em Linux usando o Azure Resource Manager e o Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252820"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Ativar encriptação de disco para nódosos de cluster de tecido de serviço Azure em Linux 
> [!div class="op_single_selector"]
> * [Encriptação de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Encriptação de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Neste tutorial, você vai aprender a ativar a encriptação do disco em nós de cluster Azure Service Fabric em Linux. Você precisará seguir estes passos para cada um dos tipos de nó e conjuntos de escala de máquina virtual. Para encriptar os nós, usaremos a capacidade de encriptação do disco Azure em conjuntos de escala de máquinas virtuais.

O guia aborda os seguintes tópicos:

* Conceitos-chave a ter em conta ao permitir a encriptação do disco em conjuntos de máquinas virtuais de cluster de tecido de serviço em Linux.
* Passos a seguir antes de permitir a encriptação do disco nos nós do cluster Service Fabric em Linux.
* Passos a seguir para permitir a encriptação do disco nos nós do cluster service Fabric em Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

 **Auto-inscrição**

A pré-visualização de encriptação do disco para o conjunto de escala de máquina virtual requer auto-registo. Utilize os passos seguintes:

1. Execute o seguinte comando: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o estado leia *Registado*. Pode verificar o estado executando o seguinte comando:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Cofre de Chaves do Azure**

1. Crie um cofre chave na mesma subscrição e região que o conjunto de escala. Em seguida, selecione a política de acesso **EnabledForDiskEncryption** no cofre da chave utilizando o seu cmdlet PowerShell. Também pode definir a política utilizando o Cofre chave UI no portal Azure com o seguinte comando:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Instale a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)que tem os novos comandos de encriptação.

3. Instale a versão mais recente do [Azure SDK a partir do lançamento da Azure PowerShell.](https://github.com/Azure/azure-powershell/releases) Seguem-se o conjunto de máquinas virtuais Azure Disk Crypton cmdlets para ativar[(set)](/powershell/module/az.compute/set-azvmssdiskencryptionextension)encriptação, recuperar[(obter](/powershell/module/az.compute/get-azvmssvmdiskencryption)) estado de encriptação e remover[(desativar](/powershell/module/az.compute/disable-azvmssdiskencryption)) encriptação na instância de conjunto de escala.


| Comando | Versão |  Origem  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou mais tarde | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou mais tarde | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou mais tarde | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou mais tarde | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou mais tarde | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou mais tarde | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários suportados para encriptação de disco
* A encriptação para conjuntos de escala de máquinas virtuais é suportada apenas para conjuntos de escala criados com discos geridos. Não é suportado para conjuntos de escala de disco nativo (ou não geridos).
* Tanto a encriptação como a encriptação desativação são suportadas para OS e volumes de dados em conjuntos de escala de máquinas virtuais em Linux. 
* As operações de reimagem e atualização da máquina virtual (VM) para conjuntos de escala de máquinas virtuais não são suportadas na pré-visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Criar um novo cluster e ativar a encriptação do disco

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

Se precisar de autoria de um modelo personalizado, recomendamos vivamente que utilize um dos modelos na página de amostras de modelos de criação de cluster fabric a [azure.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) 

Se já tem um modelo personalizado, verifique duas vezes se os três parâmetros relacionados com certificados no modelo e no ficheiro do parâmetro são nomeados da seguinte forma. Certifique-se também de que os valores são nulos da seguinte forma:

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

Como apenas a encriptação do disco de dados é suportada para conjuntos de escala de máquinavirtual no Linux, você deve adicionar um disco de dados usando um modelo de Gestor de Recursos. Atualize o seu modelo para a provisão do disco de dados da seguinte forma:

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

Aqui está o comando CLI equivalente. Alterar os valores nas declarações aos valores apropriados. O CLI suporta todos os outros parâmetros que o comando PowerShell anterior suporta.

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Monte um disco de dados para uma instância de Linux
Antes de continuar com a encriptação num conjunto de escala de máquina virtual, certifique-se de que o disco de dados adicionado está corretamente montado. Inscreva-se no cluster Linux VM e comande o comando **LSBLK.** A saída deve mostrar que o disco de dados adicionado na coluna **De Mount Point.**


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Implementar aplicação para um cluster de tecido de serviço em Linux
Para implementar uma aplicação no seu cluster, siga os passos e orientações na [Quickstart: Desloque os recipientes Linux para o Tecido de Serviço](service-fabric-quickstart-containers-linux.md).


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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Validar se a encriptação do disco estiver ativada para um conjunto de escala de máquina virtual em Linux
Para obter o estado de todo um conjunto de escala de máquina virtual ou qualquer instância num conjunto de escala, execute os seguintes comandos.
Além disso, pode iniciar sessão no cluster Linux VM e executar o comando **LSBLK.** A saída deve mostrar o disco de dados adicionado na coluna **Mount Point,** e a coluna **Tipo** deve ler *Crypt*.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Desative a encriptação do disco para uma escala de máquina virtual definida num cluster de Tecido de Serviço
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
Neste ponto, deve ter um cluster seguro e saber como ativar e desativar a encriptação do disco para os nós de cluster de tecido de serviço e conjuntos de escala de máquinavirtual. Para obter orientações semelhantes sobre os nós de cluster do tecido de serviço em Linux, consulte [encriptação de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md). 
