---
title: Criar um cluster de tecido de serviço Azure
description: Aprenda a configurar um cluster de tecido de serviço seguro em Azure utilizando o Gestor de Recursos Azure.  Pode criar um cluster usando um modelo predefinido ou usando o seu próprio modelo de cluster.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 8cf14230f3abd37d91f1ec369f597ee594876100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624117"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Criar um cluster de tecido de serviço usando o Gestor de Recursos Azure 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portal Azure](service-fabric-cluster-creation-via-portal.md)
>
>

Um [cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas virtuais ligadas à rede para as quais os seus microserviços são implantados e geridos.  Um cluster de tecido de serviço em funcionamento em Azure é um recurso Azure e é implantado usando o Gestor de Recursos Azure. Este artigo descreve como implementar um cluster de tecido de serviço seguro em Azure usando o Gestor de Recursos. Pode utilizar um modelo de cluster padrão ou um modelo personalizado.  Se ainda não tem um modelo personalizado, pode [aprender a criar um](service-fabric-cluster-creation-create-template.md).

O tipo de segurança escolhido para proteger o cluster (isto é, identidade do Windows, X509, etc.) deve ser especificado para a criação inicial do cluster, e não pode ser alterado posteriormente. Antes de configurar um cluster, leia [os cenários][service-fabric-cluster-security]de segurança do cluster Service Fabric . No Azure, o Service Fabric utiliza certificado x509 para proteger o seu cluster e os seus pontos finais, autenticar clientes e encriptar dados. O Azure Ative Directory também é recomendado para garantir o acesso aos pontos finais de gestão. Para mais informações, leia [Configurar a Azure AD para autenticar clientes.](service-fabric-cluster-creation-setup-aad.md)

Se está a criar um cluster de produção para executar cargas de trabalho de produção, recomendamos que leia pela primeira vez a lista de verificação de prontidão de [produção.](service-fabric-production-readiness-checklist.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos 
Neste artigo, utilize os módulos Powershell RM de tecido de serviço ou azure CLI para implantar um cluster:

* [Azure PowerShell 4.1 e acima][azure-powershell]
* [Versão Azure CLI 2.0 e acima][azure-CLI]

Pode encontrar aqui a documentação de referência para os módulos de Tecido de Serviço:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [módulo Az SF CLI](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Antes de executar qualquer um dos comandos neste artigo, primeiro inscreva-se no Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Criar um novo cluster usando um certificado auto-assinado gerado pelo sistema

Utilize os seguintes comandos para criar um cluster protegido com um certificado auto-assinado gerado pelo sistema. Este comando estabelece um certificado de cluster primário que é utilizado para a segurança do cluster e para configurar o acesso administrativo para executar operações de gestão utilizando esse certificado.  Os certificados auto-assinados são úteis para assegurar clusters de teste.  Os clusters de produção devem ser assegurados com um certificado de uma autoridade de certificados (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Use o modelo de cluster padrão que os navios no módulo

Utilize o seguinte comando para criar um cluster rapidamente, especificando parâmetros mínimos, utilizando o modelo predefinido.

O modelo que é usado está disponível nas amostras de modelo de tecido de [serviço Azure : modelo de janelas](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e [modelo Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

O comando seguinte pode criar clusters Windows ou Linux, precisa especificar o SISTEMA em conformidade. Os comandos PowerShell/CLI também descontam o certificado na pasta de saída de *certificados*especificada; no entanto, certifique-se de que a pasta de certificado já foi criada. O comando também acolhe outros parâmetros, como o VM SKU.

> [!NOTE]
> O comando PowerShell seguinte só funciona `Az` com o módulo PowerShell Azure. Para verificar a versão atual da versão PowerShell do Gestor de Recursos do Azure, execute o seguinte comando PowerShell "Get-Module Az". Siga [este link](/powershell/azure/install-Az-ps) para atualizar a versão PowerShell do Gestor de Recursos Do Azure. 
>
>

Implementar o cluster utilizando o PowerShell:

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

Implementar o cluster utilizando o Azure CLI:

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

### <a name="use-your-own-custom-template"></a>Use o seu próprio modelo personalizado

Se precisar de autoria de um modelo personalizado para atender às suas necessidades, é altamente recomendável que comece com um dos modelos disponíveis nas amostras do modelo de tecido de [serviço Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Aprenda a [personalizar o seu modelo][customize-your-cluster-template]de cluster .

Se já tiver um modelo personalizado, verifique duas vezes se todos os três parâmetros relacionados com o certificado no modelo e o ficheiro do parâmetro são nomeados da seguinte forma e os valores são nulos da seguinte forma:

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

Implementar o cluster utilizando o PowerShell:

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

Implementar o cluster utilizando o Azure CLI:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Crie um novo cluster usando o seu próprio certificado X.509

Utilize o seguinte comando para criar cluster, se tiver um certificado que pretende utilizar para proteger o seu cluster.

Se este é um certificado assinado pela AC que você vai acabar por usar para outros fins também, então é recomendado que você forneça um grupo de recursos distinto especificamente para o seu cofre chave. Recomendamos que coloque o cofre chave no seu próprio grupo de recursos. Esta ação permite remover os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o seu cluster Service Fabric, sem perder as chaves e segredos. **O grupo de recursos que contém o seu cofre chave *deve estar na mesma região* que o cluster que o está a usar.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Use o nó padrão de cinco, um modelo de tipo nó que envia no módulo
O modelo que é usado está disponível nas [amostras Azure : modelo de windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) e modelo [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Implementar o cluster utilizando o PowerShell:

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

Implementar o cluster utilizando o Azure CLI:

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

### <a name="use-your-own-custom-cluster-template"></a>Use o seu próprio modelo de cluster personalizado
Se precisar de autoria de um modelo personalizado para atender às suas necessidades, é altamente recomendável que comece com um dos modelos disponíveis nas amostras do modelo de tecido de [serviço Azure](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Aprenda a [personalizar o seu modelo][customize-your-cluster-template]de cluster .

Se já tem um modelo personalizado, certifique-se de verificar duas vezes se os três parâmetros relacionados com o certificado no modelo e o ficheiro do parâmetro são nomeados da seguinte forma e os valores são nulos da seguinte forma.

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

Implementar o cluster utilizando o PowerShell:

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

Implementar o cluster utilizando o Azure CLI:

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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Use um ponteiro para um segredo enviado para um cofre chave

Para utilizar um cofre chave existente, o cofre-chave deve ser [ativado para a implantação para](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) permitir que o fornecedor de recursos computacionais obtenha certificados do mesmo e instale-o em nós de cluster.

Implementar o cluster utilizando o PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Implementar o cluster utilizando o Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Passos seguintes
Neste momento, tem um aglomerado seguro em Funcionamento em Azure. Em seguida, [ligue-se ao seu cluster](service-fabric-connect-to-secure-cluster.md) e aprenda a gerir os segredos da [aplicação.](service-fabric-application-secret-management.md)

Para que a sintaxe e as propriedades da JSON utilizem um modelo, consulte a referência do [modelo Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
