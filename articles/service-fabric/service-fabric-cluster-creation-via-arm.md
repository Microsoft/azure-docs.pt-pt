---
title: Criar um cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba como configurar um cluster do Service Fabric seguro no Azure com o Azure Resource Manager.  Pode criar um cluster utilizando um modelo padrão ou utilizando o seu próprio modelo de cluster.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 715b0f9c5a5a68bb1fc42ed4206ca81ad85b844f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450690"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Criar um cluster do Service Fabric com o Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal do Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Uma [cluster do Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto ligado à rede de máquinas virtuais para o qual os microsserviços são implementados e geridos.  Um cluster do Service Fabric em execução no Azure é um recurso do Azure e é implementado utilizando o Gestor de recursos do Azure. Este artigo descreve como implementar um cluster do Service Fabric seguro no Azure com o Resource Manager. Pode utilizar um modelo de cluster predefinido ou um modelo personalizado.  Se ainda não tiver um modelo personalizado, pode [Saiba como criar um](service-fabric-cluster-creation-create-template.md).

Segurança do cluster é configurada quando o cluster é o primeiro programa de configuração e não é possível alterar mais tarde. Antes de configurar um cluster, leia [cenários de segurança de cluster do Service Fabric][service-fabric-cluster-security]. No Azure, o Service Fabric utiliza x509 certificado para proteger o seu cluster e seus pontos de extremidade, autenticar clientes e encripta os dados. Também é recomendado proteger o acesso aos pontos finais de gestão do Azure Active Directory. O Azure AD inquilinos e os utilizadores devem ser criados antes de criar o cluster.  Para obter mais informações, leia [configurar o Azure AD para autenticar clientes](service-fabric-cluster-creation-setup-aad.md).

Se estiver a criar um cluster de produção para executar cargas de trabalho de produção, recomendamos que leia primeiro através da [lista de verificação de preparação de produção](service-fabric-production-readiness-checklist.md).

## <a name="prerequisites"></a>Pré-requisitos 
Neste artigo, utilize o powershell do Service Fabric RM ou módulos de CLI do Azure para implementar um cluster:

* [O Azure PowerShell 4.1 e posterior][azure-powershell]
* [CLI do Azure versão 2.0 e superior][azure-CLI]

Pode encontrar a documentação de referência para os módulos do Service Fabric aqui:
* [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric)
* [módulo CLI AZ SF](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Antes de executar qualquer um dos comandos neste artigo, primeiro inicie sessão no Azure.

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Criar um novo cluster com um certificado autoassinado gerada pelo sistema

Utilize os seguintes comandos para criar um cluster protegido por um certificado autoassinado gerada pelo sistema. Este comando configura um certificado de cluster principal que é utilizado para segurança do cluster e configurar o acesso de administrador para efetuar operações de gestão a utilizar esse certificado.  Certificados autoassinados são úteis para proteger clusters de teste.  Clusters de produção devem ser protegidos por um certificado de uma autoridade de certificação (AC).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Utilizar o modelo de cluster predefinido que é fornecido no módulo

Utilize o seguinte comando para criar um cluster rapidamente, ao especificar os parâmetros mínimos, usando o modelo padrão.

O modelo que é utilizado está disponível na [exemplos de modelos do Azure Service Fabric: modelo do windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [Ubuntu modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

O comando seguinte, pode criar qualquer um dos Windows ou clusters do Linux, tem de especificar o sistema operacional em conformidade. Os comandos do PowerShell/CLI de saída também o certificado especificado *CertificateOutputFolder*; no entanto, certifique-se de pasta de certificados que já criou. O comando recebe os outros parâmetros, bem como o SKU de VM.

> [!NOTE]
> O seguinte comando do PowerShell só funciona com o Azure Resource Manager PowerShell versão > 6.1. Para verificar a versão atual do Azure Resource Manager PowerShell versão, execute o seguinte comando do PowerShell "Get-Module AzureRM". Siga [esta ligação](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.3.0) para atualizar a sua versão do PowerShell do Azure Resource Manager. 
>
>

Implemente o cluster com o PowerShell:

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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Implemente o cluster com a CLI do Azure:

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

### <a name="use-your-own-custom-template"></a>Utilizar o seu próprio modelo personalizado

Se precisar de criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que comece com um dos modelos que estão disponíveis na [exemplos de modelos do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Saiba como [personalize o seu modelo de cluster][customize-your-cluster-template].

Se já tiver um modelo personalizado, verifique se o certificado três relacionado com parâmetros no modelo e o ficheiro de parâmetros são nomeados da seguinte forma e valores são nulos da seguinte forma:

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

Implemente o cluster com o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implemente o cluster com a CLI do Azure:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Criar um novo cluster com o seu próprio certificado X.509

Utilize o seguinte comando para criar o cluster, se tiver um certificado que pretende utilizar para proteger o seu cluster com.

Se se tratar de um certificado assinado de AC que vai acabar com para outros fins como, em seguida, é recomendado que forneça um grupo de recursos distintos especificamente para o seu Cofre de chaves. Recomendamos que colocar o Cofre de chaves em seu próprio grupo de recursos. Esta ação permite-lhe remover os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o cluster do Service Fabric, sem perder as chaves e segredos. **O grupo de recursos que contém o seu Cofre de chaves *tem de estar na mesma região* que o cluster que está a utilizar.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Utilize o nó de cinco padrão, o modelo de tipo de um nó que é fornecido no módulo
O modelo que é utilizado está disponível na [exemplos do Azure: Modelo do Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [Ubuntu modelo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Implemente o cluster com o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Implemente o cluster com a CLI do Azure:

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

### <a name="use-your-own-custom-cluster-template"></a>Utilizar o seu próprio modelo de cluster personalizado
Se precisar de criar um modelo personalizado para atender às suas necessidades, é altamente recomendável que comece com um dos modelos que estão disponíveis na [exemplos de modelos do Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Saiba como [personalize o seu modelo de cluster][customize-your-cluster-template].

Se já tiver um modelo personalizado, certifique-se de que verifique novamente se o certificado três relacionados com parâmetros no modelo e o ficheiro de parâmetros são nomeados da seguinte forma e valores são nulas da seguinte forma.

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

Implemente o cluster com o PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Implemente o cluster com a CLI do Azure:

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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Utilizar um ponteiro para um segredo carregado para um cofre de chaves

Para utilizar o Cofre de chaves existente, o Cofre de chaves tem de ser [ativada para a implementação](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) para permitir que o fornecedor de recursos de computação obter certificados de-lo e instalá-lo em nós de cluster.

Implemente o cluster com o PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implemente o cluster com a CLI do Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Passos Seguintes
Neste ponto, tem um cluster seguro em execução no Azure. Em seguida, [ligar ao cluster](service-fabric-connect-to-secure-cluster.md) e saiba como [gerir segredos da aplicação](service-fabric-application-secret-management.md).

Para a sintaxe JSON e propriedades para utilizar um modelo, consulte [referência de modelo Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
