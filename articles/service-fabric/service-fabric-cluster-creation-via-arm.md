---
title: Criar um cluster de Service Fabric do Azure | Microsoft Docs
description: Saiba como configurar um cluster de Service Fabric seguro no Azure usando Azure Resource Manager.  Você pode criar um cluster usando um modelo padrão ou usando seu próprio modelo de cluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: atsenthi
ms.openlocfilehash: 241349724929845afa2fd2a4bacabf9b5017cc7c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901558"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Criar um Cluster Service Fabric usando Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Um [cluster de Service Fabric do Azure](service-fabric-deploy-anywhere.md) é um conjunto de máquinas virtuais conectadas à rede em que seus microserviços são implantados e gerenciados.  Um Cluster Service Fabric em execução no Azure é um recurso do Azure e é implantado usando o Azure Resource Manager. Este artigo descreve como implantar um cluster de Service Fabric seguro no Azure usando o Gerenciador de recursos. Você pode usar um modelo de cluster padrão ou um modelo personalizado.  Se você ainda não tiver um modelo personalizado, poderá [aprender a criar um](service-fabric-cluster-creation-create-template.md).

O tipo de segurança escolhido para proteger o cluster (ou seja,: Windows Identity, X509 etc.) deve ser especificado para a criação inicial do cluster e não pode ser alterado depois disso. Antes de configurar um cluster, leia [Service Fabric cenários de segurança de cluster][service-fabric-cluster-security]. No Azure, Service Fabric usa o certificado X509 para proteger o cluster e seus pontos de extremidade, autenticar clientes e criptografar dados. Azure Active Directory também é recomendável para proteger o acesso aos pontos de extremidade de gerenciamento. Para obter mais informações, leia [Configurar o Azure ad para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

Se você estiver criando um cluster de produção para executar cargas de trabalho de produção, recomendamos que você leia primeiro a [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
Neste artigo, use os módulos Service Fabric RM PowerShell ou CLI do Azure para implantar um cluster:

* [Azure PowerShell 4,1 e superior][azure-powershell]
* [CLI do Azure versão 2,0 e posterior][azure-CLI]

Você pode encontrar a documentação de referência para os módulos de Service Fabric aqui:
* [AZ. onfabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [módulo AZ it CLI](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Antes de executar qualquer um dos comandos neste artigo, primeiro entre no Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Criar um novo cluster usando um certificado autoassinado gerado pelo sistema

Use os comandos a seguir para criar um cluster protegido com um certificado autoassinado gerado pelo sistema. Esse comando configura um certificado de cluster primário que é usado para segurança de cluster e para configurar o acesso de administrador para executar operações de gerenciamento usando esse certificado.  Os certificados autoassinados são úteis para proteger os clusters de teste.  Os clusters de produção devem ser protegidos com um certificado de uma autoridade de certificação (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Usar o modelo de cluster padrão fornecido no módulo

Use o comando a seguir para criar um cluster rapidamente, especificando parâmetros mínimos, usando o modelo padrão.

O modelo usado está disponível nos [exemplos de modelo do Service Fabric do Azure: modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modelo do Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

O comando a seguir pode criar clusters Windows ou Linux, você precisa especificar o sistema operacional de acordo. Os comandos do PowerShell/CLI também geram o certificado no *CertificateOutputFolder*especificado; no entanto, verifique se a pasta do certificado já foi criada. O comando também usa outros parâmetros, como SKU de VM.

> [!NOTE]
> O comando do PowerShell a seguir funciona apenas com o módulo Azure PowerShell `Az`. Para verificar a versão atual do Azure Resource Manager versão do PowerShell, execute o seguinte comando do PowerShell "Get-Module AZ". Siga [este link](/powershell/azure/install-Az-ps) para atualizar sua versão Azure Resource Manager PowerShell. 
>
>

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Implante o cluster usando CLI do Azure:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Usar seu próprio modelo personalizado

Se você precisar criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você comece com um dos modelos que estão disponíveis nos exemplos de [modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Saiba como [Personalizar o modelo de cluster][customize-your-cluster-template].

Se você já tiver um modelo personalizado, verifique se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da seguinte maneira:

```json
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

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implante o cluster usando CLI do Azure:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Criar um novo cluster usando seu próprio certificado X. 509

Use o comando a seguir para criar um cluster, se você tiver um certificado que deseja usar para proteger o cluster com o.

Se esse for um certificado assinado por uma autoridade de certificação que você acabará usando para outros fins também, é recomendável que você forneça um grupo de recursos distinto especificamente para o cofre de chaves. Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o cluster de Service Fabric, sem perder suas chaves e segredos. **O grupo de recursos que contém o cofre de chaves *deve estar na mesma região* que o cluster que o está usando.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Usar o modelo padrão de cinco nós, um tipo de nó que é fornecido no módulo
O modelo usado está disponível nos [exemplos do Azure: modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modelo do Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Implante o cluster usando CLI do Azure:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Usar seu próprio modelo de cluster personalizado
Se você precisar criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que você comece com um dos modelos que estão disponíveis nos exemplos de [modelo do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Saiba como [Personalizar o modelo de cluster][customize-your-cluster-template].

Se você já tiver um modelo personalizado, certifique-se de verificar novamente se todos os três parâmetros relacionados ao certificado no modelo e no arquivo de parâmetro são nomeados da seguinte maneira e se os valores são nulos da seguinte maneira.

```json
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

Implantar o cluster usando o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Implante o cluster usando CLI do Azure:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Usar um ponteiro para um segredo carregado em um cofre de chaves

Para usar um cofre de chaves existente, o cofre de chaves deve ser [habilitado para implantação](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) para permitir que o provedor de recursos de computação obtenha certificados dele e instale-os em nós de cluster.

Implantar o cluster usando o PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implante o cluster usando CLI do Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Passos seguintes
Neste ponto, você tem um cluster seguro em execução no Azure. Em seguida, [Conecte-se ao seu cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerenciar os segredos do aplicativo](service-fabric-application-secret-management.md).

Para obter a sintaxe e as propriedades JSON para usar um modelo, consulte [referência de modelo Microsoft. perfabric/clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
