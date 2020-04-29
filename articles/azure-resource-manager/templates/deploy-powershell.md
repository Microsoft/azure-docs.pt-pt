---
title: Implementar recursos com PowerShell e modelo
description: Utilize o Azure Resource Manager e o Azure PowerShell para mobilizar recursos para o Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153272"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Implementar recursos com modelos ARM e Azure PowerShell

Aprenda a utilizar o Azure PowerShell com modelos de Gestor de Recursos Azure (ARM) para implantar os seus recursos no Azure. Para obter mais informações sobre os conceitos de implantação e gestão das suas soluções Azure, consulte a visão geral da implementação do [modelo.](overview.md)

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implantação para um grupo de recursos, subscrição, grupo de gestão ou inquilino. Na maioria dos casos, irá direcionar a implantação para um grupo de recursos. Para aplicar políticas e atribuições de funções num âmbito maior, utilize subscrições, grupos de gestão ou destacamentos de inquilinos. Ao implementar uma subscrição, pode criar um grupo de recursos e implementar recursos para o mesmo.

Dependendo do alcance da implantação, utiliza-se comandos diferentes.

Para implantar num grupo de **recursos,** utilize a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Para implementar uma **subscrição,** utilize a New-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Para obter mais informações sobre implementações de nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](deploy-to-subscription.md).

Para implantar num grupo de **gestão,** utilize a [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Para obter mais informações sobre as implementações a nível do grupo de gestão, consulte Criar recursos ao nível do grupo de [gestão.](deploy-to-management-group.md)

Para se deslocar a um **inquilino,** utilize a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Para obter mais informações sobre as implementações ao nível dos inquilinos, consulte [Criar recursos ao nível dos inquilinos.](deploy-to-tenant.md)

Os exemplos deste artigo utilizam as implantações de grupos de recursos.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um modelo para ser implantado. Se ainda não tiver um, descarregue e guarde um modelo de [exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) dos modelos Azure Quickstart repo. O nome de ficheiro local usado neste artigo é **c:\MyTemplates\azuredeploy.json**.

A menos que utilize a Casca de Nuvem Azure para implementar modelos, precisa de instalar o Azure PowerShell e ligar-se ao Azure:

- **Instale cmdlets Azure PowerShell no seu computador local.** Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Ligue-se ao Azure utilizando [o Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Se tiver várias subscrições azure, também poderá ser necessário executar [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para mais informações, consulte [Utilize várias subscrições azure.](/powershell/azure/manage-subscriptions-azureps)

## <a name="deploy-local-template"></a>Implementar modelo local

O exemplo seguinte cria um grupo de recursos e implanta um modelo da sua máquina local. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífenes e parênteses. Pode ser até 90 caracteres. Não pode acabar num período.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implementação pode demorar alguns minutos a concluir.

## <a name="deploy-remote-template"></a>Implementar modelo remoto

Em vez de armazenar modelos ARM na sua máquina local, pode preferir armazená-los num local externo. Pode armazenar modelos num repositório de controlo de fonte (como o GitHub). Ou pode guardá-los numa conta de armazenamento Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o parâmetro **TemplateUri.** Utilize o URI no exemplo para implementar o modelo de amostra do GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior requer um URI acessível ao público para o modelo, que funciona para a maioria dos cenários porque o seu modelo não deve incluir dados sensíveis. Se precisar especificar dados sensíveis (como uma senha de administração), passe esse valor como parâmetro seguro. No entanto, se não quiser que o seu modelo seja acessível ao público, pode protegê-lo armazenando-o num recipiente de armazenamento privado. Para obter informações sobre a implementação de um modelo que requer uma assinatura de acesso partilhado (SAS) ficha, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md). Para passar por um tutorial, consulte [Tutorial: Integre o Cofre chave Azure na implementação](template-tutorial-use-key-vault.md)do modelo ARM .

## <a name="deploy-from-azure-cloud-shell"></a>Desdobre a partir de Azure Cloud Shell

Pode utilizar a Casca de [Nuvem Azure](https://shell.azure.com) para implementar o seu modelo. Para implementar um modelo externo, forneça o URI do modelo. Para implementar um modelo local, primeiro deve carregar o seu modelo na conta de armazenamento da sua Cloud Shell. Para fazer o upload de ficheiros para a casca, selecione o ícone do menu **upload/download** de ficheiros a partir da janela da concha.

Para abrir a casca cloud, navegue para [https://shell.azure.com](https://shell.azure.com), ou selecione **Try-It** a partir da seguinte secção de código:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Para colar o código na casca, clique à direita dentro da concha e, em seguida, **selecione Pasta**.

## <a name="pass-parameter-values"></a>Valores do parâmetro de passagem

Para passar os valores dos parâmetros, pode utilizar parâmetros inline ou um ficheiro de parâmetro.

### <a name="inline-parameters"></a>Parâmetros inline

Para passar os parâmetros inline, forneça os `New-AzResourceGroupDeployment` nomes do parâmetro com o comando. Por exemplo, para passar uma corda e uma matriz para um modelo, use:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Também pode obter o conteúdo do ficheiro e fornecer esse conteúdo como parâmetro inline.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obter um valor de parâmetro de um ficheiro é útil quando precisa de fornecer valores de configuração. Por exemplo, pode fornecer [valores de init em nuvem para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

Se precisar de passar numa série de objetos, crie mesas de hash no PowerShell e adicione-as a uma matriz. Passe a matriz como parâmetro durante a implantação.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Ficheiros paraparâmetros

Em vez de passar parâmetros como valores inline no seu script, poderá ser mais fácil utilizar um ficheiro JSON que contenha os valores do parâmetro. O ficheiro parâmetro pode ser um ficheiro local ou um ficheiro externo com um URI acessível.

Para obter mais informações sobre o ficheiro do parâmetro, consulte o [ficheiro de parâmetro sinuoso Do Gestor de Recursos](parameter-files.md).

Para passar num ficheiro de parâmetro local, utilize o parâmetro **TemplateParameterFile:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar num ficheiro de parâmetro externo, utilize o parâmetro **TemplateParameterUri:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Implementações de modelos de teste

Para testar os valores do seu modelo e parâmetro sem realmente implementar quaisquer recursos, utilize o [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Se não forem detetados erros, o comando termina sem resposta. Se for detetado um erro, o comando devolve uma mensagem de erro. Por exemplo, passar um valor incorreto para a conta de armazenamento SKU, devolve o seguinte erro:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se o seu modelo tiver um erro de sintaxe, o comando devolve um erro indicando que não poderia analisar o modelo. A mensagem indica o número de linha e a posição do erro de análise.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Passos seguintes

- Para voltar a uma implementação bem sucedida quando tiver um erro, consulte [o Rollback no erro para uma implementação bem sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os modos de implementação do Gestor de [Recursos Do Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [Compreender a estrutura e a sintaxe dos modelos ARM](template-syntax.md).
- Para obter informações sobre a implementação de um modelo que requer uma ficha SAS, consulte [Implementar modelo privado com token SAS](secure-template-with-sas-token.md).
