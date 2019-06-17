---
title: Ativar a encriptação de disco para a clusters do Windows de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Este artigo descreve como ativar a encriptação de disco para nós de cluster do Azure Service Fabric com o Azure Key Vault no Azure Resource Manager.
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
ms.openlocfilehash: c31fc43729bcb58c755959db0c8bc5185b8197f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66471405"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Ativar a encriptação de disco para nós do cluster do Azure Service Fabric no Windows 
> [!div class="op_single_selector"]
> * [Encriptação de disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Encriptação de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Neste tutorial, aprenderá a ativar a encriptação de disco em nós de cluster do Service Fabric no Windows. Terá de seguir estes passos para cada um dos tipos de nós e conjuntos de dimensionamento de máquina virtual. Para encriptar os nós, vamos utilizar a capacidade de encriptação de disco do Azure em conjuntos de dimensionamento de máquina virtual.

O guia abrange os seguintes tópicos:

* Conceitos-chave a ter em consideração quando define o ativar encriptação de disco no dimensionamento de máquinas virtuais de cluster do Service Fabric no Windows.
* Passos para ser seguidas antes de ativar a encriptação de disco no Service Fabric nós de cluster no Windows.
* Passos a seguir para ativar a encriptação de disco em nós de cluster do Service Fabric no Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

**Self-registration** 

A pré-visualização da encriptação de disco para o conjunto de dimensionamento de máquina virtual requer o registo automático. Utilize os passos seguintes: 

1. Em primeiro lugar, execute o seguinte comando:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que lê o estado *registado*. Pode verificar o estado ao executar o seguinte comando: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Cofre de Chaves do Azure** 

1. Criar um cofre de chaves na mesma subscrição e região que o conjunto de dimensionamento, em seguida, selecione o **EnabledForDiskEncryption** política no Cofre de chaves de acesso utilizando o cmdlet do PowerShell. Também pode definir a política através da IU de Cofre de chave no portal do Azure com o seguinte comando:
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
* A encriptação é suportada para o SO e nos conjuntos de volumes de dados no dimensionamento de máquinas virtuais no Windows. Desativar encriptação também é suportada para o sistema operacional e nos conjuntos de volumes de dados para o dimensionamento de máquinas virtuais no Windows.
* Operações de recriação de imagem e a atualização de máquina virtual para conjuntos de dimensionamento de máquinas virtuais não são suportadas na pré-visualização atual.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Criar um novo cluster e ativar a encriptação de disco

Utilize os seguintes comandos para criar um cluster e ativar a encriptação de disco utilizando um modelo Azure Resource Manager e um certificado autoassinado.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão com os seguintes comandos:
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>Utilizar o modelo personalizado que já tem 

Se precisar de criar um modelo personalizado para atender às suas necessidades, recomendamos vivamente que comece com um dos modelos que estão disponíveis na [exemplos de modelos de criação de cluster do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) página. Para [personalize o seu modelo de cluster] [ customize-your-cluster-template] secção, consulte as seguintes orientações.

Se já tiver um modelo personalizado, verifique novamente que todos os parâmetros relacionados com o certificado no modelo e o ficheiro de parâmetros três são nomeados da seguinte forma e que valores são nulos da seguinte forma:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Implementar uma aplicação num cluster do Service Fabric no Windows
Para implementar uma aplicação no seu cluster, siga os passos e as orientações em [implementar e remover aplicações com o PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Confirmar se a encriptação de disco está ativada para um conjunto de dimensionamento no Windows
Obtenha o estado de um conjunto de dimensionamento de máquina virtual inteira ou de qualquer instância num conjunto, executando os seguintes comandos de dimensionamento.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Além disso, pode iniciar sessão para o conjunto de dimensionamento de máquina virtual e certifique-se de que as unidades são encriptadas.

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Desativar a encriptação de disco para um conjunto de dimensionamento no cluster do Service Fabric 
Desative a encriptação de disco para um conjunto de dimensionamento, executando os comandos seguintes. Tenha em atenção que a desativação da encriptação de disco aplica-se para o conjunto de dimensionamento de máquina virtual inteira e não uma instância individual.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Passos Seguintes
Neste momento, deve ter um cluster seguro e saber como ativar e desativar a encriptação de disco para nós de cluster do Service Fabric e conjuntos de dimensionamento de máquina virtual. Para obter diretrizes semelhantes em nós de cluster do Service Fabric no Linux, consulte [encriptação de disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
