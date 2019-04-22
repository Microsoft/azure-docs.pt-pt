---
title: Ativar a encriptação de disco para a clusters do Linux do Azure Service Fabric | Documentos da Microsoft
description: Este artigo descreve como ativar a encriptação de disco para o Service Fabric cluster conjunto de dimensionamento no Azure utilizando o Gestor de recursos do Azure, Azure Key Vault.
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
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046985"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>Ativar a encriptação de disco para nós de cluster do service fabric Linux 
> [!div class="op_single_selector"]
> * [Encriptação de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Encriptação de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

Siga os passos abaixo para ativar a encriptação de disco em nós de Cluster do Service Fabric Linux. Terá de fazer isso para cada um dos conjuntos de dimensionamento de máquina virtual/tipos do nó. Para encriptar os nós, iremos tirar partido da capacidade de Azure Disk Encryption em conjuntos de dimensionamento de máquina virtual.

O guia abrange os seguintes procedimentos:

* Conceitos-chave que precisa de conhecer desativar ativar a encriptação de disco em dimensionamento de máquinas virtuais de Cluster do Service Fabric Linux definido.
* Passos de pré-requisitos para ser seguidas antes de ativar a encriptação de disco no conjunto de dimensionamento de máquina virtual de Cluster do Linux do Service Fabric.
* Conjunto de passos a seguir para ativar a encriptação de disco em dimensionamento de máquinas virtuais de Cluster do Linux do Service Fabric.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* **Registo automático** – para poder utilizar, pré-visualização de encriptação de disco do máquina virtual dimensionamento conjunto requer o registo automático
* Self-pode registrar sua assinatura ao executar os seguintes passos: 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* Aguarde cerca de 10 minutos até o estado como "Registado". Pode verificar o estado ao executar o seguinte comando: 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **O Azure Key Vault** -criar um cofre de chaves na mesma subscrição e região, como o dimensionamento de máquinas virtuais definido e definir a política de acesso "EnabledForDiskEncryption" no Cofre de chaves utilizando o cmdlet de PS. Também pode definir a política utilizando a interface do Usuário do Cofre de chaves no portal do Azure: 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* Instalar a versão mais recente [CLI do Azure](/cli/azure/install-azure-cli) , que tem os novos comandos de encriptação.
* Instale a versão mais recente do [Azure SDK do Azure PowerShell](https://github.com/Azure/azure-powershell/releases) de versão. Seguem-se a máquina virtual conjunto de dimensionamento ADE cmdlets para ativar ([definir](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) a criptografia, obter ([obter](/powershell/module/az.compute/get-azvmssvmdiskencryption)) Estado de encriptação e remover ([desativar](/powershell/module/az.compute/disable-azvmssdiskencryption)) encriptação em escala instância do conjunto. 

| Comando | Versão |  Origem  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 ou superior | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 ou superior | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 ou superior | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 ou superior | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 ou superior | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 ou superior | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Cenários suportados para a encriptação de disco
* Encriptação de conjunto de dimensionamento de máquina virtual só é suportada para conjuntos de dimensionamento criados com discos geridos e não suportadas para conjuntos de dimensionamento do disco nativo (ou não gerenciado).
* Encriptação de conjunto de dimensionamento de máquina virtual é suportada para o volume de dados para o conjunto de dimensionamento de máquina virtual do Linux. Encriptação de disco de SO não é suportada na pré-visualização atual para Linux.
* Recriação de imagem de VM do conjunto de dimensionamento de máquina virtual e operações de atualização não são suportadas na pré-visualização atual.


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>Criar novo cluster do Linux e ativar a encriptação de disco

Utilize os seguintes comandos para criar o cluster e ativar a encriptação de disco usando o modelo Azure Resource Manager e o certificado autoassinado.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Utilizar o modelo personalizado que já tem 

Se precisar de criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que comece com um dos modelos que estão disponíveis na [exemplos de modelos de recursos de infraestrutura do serviço do azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) para Cluster do Linux. 

Se já tiver um modelo personalizado, certifique-se de que a verificação de double, que todos os três relacionadas com o certificado parâmetros no modelo e o ficheiro de parâmetros são nomeados da seguinte forma e valores são nulas da seguinte forma.

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

Desde para conjunto de dimensionamento de máquina virtual do Linux - encriptação de disco de dados apenas é suportada pelo que precisamos adicionar disco de dados com o modelo Azure Resource Manager. Atualize o modelo para aprovisionar de disco de dados como abaixo:

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

Eis o comando da CLI equivalente para fazer o mesmo. Altere os valores nas instruções declare para valores adequados. CLI suporta todos os outros parâmetros que suporta o comando do powershell acima.

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

#### <a name="linux-data-disk-mounting"></a>Montagem de disco de dados do Linux
Antes de continuar com a encriptação no conjunto de dimensionamento de máquina virtual do Linux, é necessário para se certificar de que o disco de dados foi adicionado corretamente é montado ou não. Inicie sessão na VM de Cluster do Linux e execute o comando LSBLK. A saída deve mostrar esse disco de dados adicionados na coluna de ponto de montagem.


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>Implementar aplicação no cluster do Service Fabric do Linux
Siga os passos e orientação para [implementar a aplicação no seu cluster](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>Ativar a encriptação de disco para o conjunto de dimensionamento de máquina virtual de Cluster do Service Fabric Linux criada acima
 
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>Valide se o conjunto de encriptação de disco ativada para o dimensionamento de máquinas virtuais do Linux.
Obter o estado de um conjunto de dimensionamento de máquina virtual inteira ou de qualquer instância VM num conjunto de dimensionamento. Consulte os comandos abaixo.
Além do utilizador pode iniciar sessão na VM de Cluster do Linux e execute o comando LSBLK. A saída deve mostrar esse disco de dados adicionados na coluna de ponto de montagem e na coluna de tipo como Crypt para o disco de dados adicionados.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Desativar a encriptação de disco para o conjunto de dimensionamento de máquina virtual de Cluster do Service Fabric 
Desativar a encriptação de disco aplica-se ao conjunto de dimensionamento de máquina virtual inteira e não por instância 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passos Seguintes
Neste ponto, tem um cluster seguro com a forma de ativar/desativar a encriptação de disco para o conjunto de dimensionamento de máquina virtual de Cluster do Service Fabric do Linux. Em seguida, [encriptação para Windows de disco](service-fabric-enable-azure-disk-encryption-windows.md) 
