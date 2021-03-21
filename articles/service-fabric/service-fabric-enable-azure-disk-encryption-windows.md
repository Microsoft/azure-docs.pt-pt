---
title: Ativar a encriptação do disco para clusters Windows
description: Este artigo descreve como ativar a encriptação do disco para os nós de cluster de tecido de serviço Azure usando o Cofre da Chave Azure em Azure Resource Manager.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "78251823"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Ativar encriptação de disco para os nóns de cluster de tecido de serviço Azure no Windows 
> [!div class="op_single_selector"]
> * [Encriptação do disco para Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Encriptação do disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Neste tutorial, você vai aprender como ativar a encriptação de disco em nós de cluster de Tecido de Serviço no Windows. Você precisa seguir estes passos para cada um dos tipos de nós e conjuntos de balança de máquinas virtuais. Para encriptar os nós, usaremos a capacidade de encriptação do disco Azure em conjuntos de escala de máquinas virtuais.

O guia aborda os seguintes tópicos:

* Conceitos-chave a ter em conta ao permitir a encriptação do disco em conjuntos de escala de máquina virtual de cluster de tecido de serviço no Windows.
* Passos a seguir antes de permitir a encriptação do disco nos nós de cluster de tecido de serviço no Windows.
* Passos a seguir para permitir a encriptação do disco nos nós de cluster de tecido de serviço no Windows.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

**Auto-registo** 

A pré-visualização da encriptação do disco para o conjunto de escala de máquina virtual requer auto-registo. Utilize os passos seguintes: 

1. Primeiro, executar o seguinte comando:
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Aguarde cerca de 10 minutos até que o estado se leia *registado*. Pode verificar o estado executando o seguinte comando: 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. Crie um cofre chave na mesma subscrição e região que o conjunto de escala, em seguida, selecione a política de acesso **de acrypção EnabledForDiskEncryption** no cofre da chave utilizando o seu cmdlet PowerShell. Também pode definir a política utilizando o Cofre de Chaves UI no portal Azure com o seguinte comando:
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
* A encriptação é suportada para os volumes de SO e de dados em conjuntos de escala de máquinas virtuais no Windows. A encriptação de desativada também é suportada para os volumes de SISTEMA e de dados para conjuntos de escala de máquinas virtuais no Windows.
* As operações de reimagem e atualização da máquina virtual não são suportadas na pré-visualização atual.


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

Se você precisa de autorizar um modelo personalizado para atender às suas necessidades, recomendamos vivamente que comece com um dos modelos que estão disponíveis na página de amostras do modelo de criação de cluster de tecido de [serviço Azure.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) Para [personalizar a secção do modelo de cluster,][customize-your-cluster-template] consulte a seguinte orientação.

Se já tiver um modelo personalizado, verifique duas vezes se os três parâmetros relacionados com o certificado no modelo e o ficheiro de parâmetros são nomeados da seguinte forma e que os valores são nulos da seguinte forma:

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Implementar uma aplicação para um cluster de tecido de serviço no Windows
Para implementar uma aplicação no seu cluster, siga os passos e orientação no [Deploy e remova as aplicações utilizando o PowerShell](service-fabric-deploy-remove-applications.md).


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Validar se a encriptação do disco estiver ativada para uma escala de máquina virtual definida no Windows
Obtenha o estado de um conjunto de escala de máquina virtual inteiro ou qualquer instância numa escala definida executando os seguintes comandos.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


Além disso, pode iniciar sedução no conjunto de escala de máquina virtual e certificar-se de que as unidades estão encriptadas.

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
Neste ponto, você deve ter um cluster seguro e saber como ativar e desativar a encriptação do disco para nós de cluster de tecido de serviço e conjuntos de escala de máquina virtual. Para obter orientações semelhantes sobre os nós de cluster de tecido de serviço em Linux, consulte [a encriptação do disco para Linux](service-fabric-enable-azure-disk-encryption-linux.md).

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
