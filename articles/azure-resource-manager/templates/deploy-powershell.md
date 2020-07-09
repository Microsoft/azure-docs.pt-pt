---
title: Implementar recursos com PowerShell e modelo
description: Utilize o Azure Resource Manager e a Azure PowerShell para mobilizar recursos para a Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: af255e0248c029f42c9c2999ae7c0389d60c58fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84431833"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Implementar recursos com modelos ARM e Azure PowerShell

Saiba como utilizar o Azure PowerShell com modelos Azure Resource Manager (ARM) para implantar os seus recursos no Azure. Para obter mais informações sobre os conceitos de implantação e gestão das suas soluções Azure, consulte [a visão geral da implementação do modelo.](overview.md)

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implementação para um grupo de recursos, subscrição, grupo de gestão ou inquilino. Na maioria dos casos, terás como alvo a implantação de um grupo de recursos. Para aplicar políticas e atribuições de papéis em um âmbito maior, use subscrição, grupo de gestão ou implantações de inquilinos. Ao implementar uma subscrição, pode criar um grupo de recursos e implementar recursos para o mesmo.

Dependendo do alcance da implantação, utiliza-se diferentes comandos.

* Para implementar num grupo de **recursos,** utilize [o New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Para implementar uma **subscrição,** utilize o New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Para obter mais informações sobre as implementações do nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição.](deploy-to-subscription.md)

* Para implantar num grupo de **gestão,** utilize [o New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Para obter mais informações sobre as implementações de nível de grupo de gestão, consulte [Criar recursos ao nível do grupo de gestão.](deploy-to-management-group.md)

* Para implantar num **inquilino,** utilize [o New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Para obter mais informações sobre as implementações de nível de inquilino, consulte [Criar recursos ao nível do arrendatário.](deploy-to-tenant.md)

Os exemplos deste artigo utilizam implementações de grupos de recursos.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um modelo para implementar. Se ainda não tiver um, faça o download e guarde um modelo de [exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do repo dos modelos Azure Quickstart. O nome do ficheiro local utilizado neste artigo é **c:\MyTemplates\azuredeploy.jsem**.

A menos que utilize a Azure Cloud Shell para implementar modelos, precisa instalar a Azure PowerShell e ligar-se ao Azure:

- **Instale cmdlets Azure PowerShell no seu computador local.** Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Ligue-se ao Azure utilizando [o Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Se tiver várias subscrições do Azure, também poderá ter de executar [o Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para obter mais informações, consulte [utilizar várias subscrições do Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Implementar modelo local

O exemplo a seguir cria um grupo de recursos e implementa um modelo a partir da sua máquina local. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Pode chegar a 90 caracteres. Não pode acabar num período.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implementação pode demorar alguns minutos a concluir.

## <a name="deploy-remote-template"></a>Implementar o modelo remoto

Em vez de armazenar modelos ARM na sua máquina local, pode preferir guardá-los num local externo. Pode armazenar modelos num repositório de controlo de origem (como o GitHub). Ou pode armazená-los numa conta de armazenamento Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o parâmetro **TemplateUri.** Utilize o URI no exemplo para implementar o modelo de amostra do GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior requer um URI acessível ao público para o modelo, que funciona para a maioria dos cenários porque o seu modelo não deve incluir dados sensíveis. Se precisar de especificar dados sensíveis (como uma palavra-passe de administração), passe esse valor como parâmetro seguro. No entanto, se não quiser que o seu modelo seja acessível ao público, pode protegê-lo armazenando-o num recipiente de armazenamento privado. Para obter informações sobre a implementação de um modelo que requer um token de assinatura de acesso partilhado (SAS), consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md). Para passar por um tutorial, consulte [Tutorial: Integre o Cofre da Chave Azul na implementação do modelo ARM](template-tutorial-use-key-vault.md).

## <a name="preview-changes"></a>Pré-visualizar alterações

Antes de implementar o seu modelo, pode visualizar as alterações que o modelo irá fazer para o seu ambiente. Utilize a [operação "e se"](template-deploy-what-if.md) para verificar se o modelo faz as alterações que espera. E se também valida o modelo para erros.

## <a name="deploy-from-azure-cloud-shell"></a>Implantação a partir de Azure Cloud Shell

Pode utilizar a [Azure Cloud Shell](https://shell.azure.com) para implementar o seu modelo. Para implementar um modelo externo, forneça o URI do modelo. Para implementar um modelo local, você deve primeiro carregar o seu modelo na conta de armazenamento para a sua Cloud Shell. Para enviar ficheiros para a concha, selecione o ícone do menu **de ficheiros upload/download** a partir da janela do invólucro.

Para abrir a Cloud Shell, navegue [https://shell.azure.com](https://shell.azure.com) para, ou selecione **Try-It** a partir da seguinte secção de código:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Para colar o código na casca, clique com o botão direito dentro da concha e, em seguida, **selecione Pasta**.

## <a name="pass-parameter-values"></a>Valores de parâmetros de passagem

Para passar valores de parâmetros, pode utilizar parâmetros inline ou um ficheiro de parâmetros.

### <a name="inline-parameters"></a>Parâmetros inline

Para passar parâmetros inline, forneça os nomes do parâmetro com o `New-AzResourceGroupDeployment` comando. Por exemplo, para passar uma corda e matriz para um modelo, use:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Também pode obter o conteúdo do ficheiro e fornecer esse conteúdo como um parâmetro inline.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obter um valor de parâmetro de um ficheiro é útil quando é necessário fornecer valores de configuração. Por exemplo, pode fornecer [valores de ineção de nuvem para uma máquina virtual Linux](../../virtual-machines/linux/using-cloud-init.md).

Se precisar de passar numa série de objetos, crie hash tables no PowerShell e adicione-os a uma matriz. Passe a matriz como parâmetro durante a implantação.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Ficheiros de parâmetros

Em vez de passar parâmetros como valores inline no seu script, pode ser mais fácil usar um ficheiro JSON que contenha os valores dos parâmetros. O ficheiro de parâmetros pode ser um ficheiro local ou um ficheiro externo com um URI acessível.

Para obter mais informações sobre o ficheiro parâmetro, consulte o [ficheiro de parâmetros Do Gestor de Recursos](parameter-files.md).

Para passar um ficheiro de parâmetro local, utilize o parâmetro **ModeloParameterFile:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar um ficheiro de parâmetro externo, utilize o parâmetro **ModeloParameterUri:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Próximos passos

- Para voltar a uma implementação bem sucedida quando tiver um erro, consulte [o Reversão do erro para uma implementação bem sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os [modos de implementação do Gestor de Recursos Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
- Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
