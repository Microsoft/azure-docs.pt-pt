---
title: Implantar recursos com o PowerShell e o modelo
description: Use Azure Resource Manager e Azure PowerShell para implantar recursos no Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 6857d7d41cb05fd168d451ed3a955107acb4ec93
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477905"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementar recursos com modelos do Resource Manager e do Azure PowerShell

Saiba como usar Azure PowerShell com modelos do Resource Manager para implantar seus recursos no Azure. Para obter mais informações sobre os conceitos de implantação e gerenciamento de suas soluções do Azure, consulte [visão geral da implantação de modelo](overview.md).

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para uma assinatura do Azure ou um grupo de recursos em uma assinatura. Na maioria dos casos, você direcionará a implantação para um grupo de recursos. Use implantações de assinatura para aplicar políticas e atribuições de função na assinatura. Você também usa implantações de assinatura para criar um grupo de recursos e implantar recursos nele. Dependendo do escopo da implantação, você usará comandos diferentes.

Para implantar em um **grupo de recursos**, use [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Para implantar em uma **assinatura**, use [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Para obter mais informações sobre implantações de nível de assinatura, consulte [criar grupos de recursos e recursos no nível de assinatura](deploy-to-subscription.md).

Atualmente, as implantações de grupo de gerenciamento só têm suporte por meio da API REST. Para obter mais informações sobre implantações de nível de grupo de gerenciamento, consulte [criar recursos no nível do grupo de gerenciamento](deploy-to-management-group.md).

Os exemplos neste artigo usam implantações de grupo de recursos.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um modelo para implantar. Se você ainda não tiver um, baixe e salve um [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do repositório de modelos de início rápido do Azure. O nome do arquivo local usado neste artigo é **c:\MyTemplates\azuredeploy.JSON**.

A menos que você use o Azure cloud Shell para implantar modelos, você precisa instalar Azure PowerShell e conectar-se ao Azure:

- **Instale os cmdlets Azure PowerShell no computador local.** Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Conecte-se ao Azure usando [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Se você tiver várias assinaturas do Azure, talvez também precise executar [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para obter mais informações, consulte [usar várias assinaturas do Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Implantar modelo local

O exemplo a seguir cria um grupo de recursos e implanta um modelo de seu computador local. O nome do grupo de recursos pode incluir apenas caracteres alfanuméricos, pontos, sublinhados, hifens e parênteses. Pode ter até 90 caracteres. Ele não pode terminar em um ponto.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implementação pode demorar alguns minutos a concluir.

## <a name="deploy-remote-template"></a>Implantar modelo remoto

Em vez de armazenar modelos do Resource Manager em seu computador local, você pode preferir armazená-los em um local externo. Você pode armazenar modelos em um repositório de controle do código-fonte (como o GitHub). Ou você pode armazená-los em uma conta de armazenamento do Azure para acesso compartilhado em sua organização.

Para implantar um modelo externo, use o parâmetro **TemplateUri** . Use o URI no exemplo para implantar o modelo de exemplo do GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior requer um URI acessível publicamente para o modelo, que funciona para a maioria dos cenários porque o modelo não deve incluir dados confidenciais. Se você precisar especificar dados confidenciais (como uma senha de administrador), passe esse valor como um parâmetro seguro. No entanto, se você não quiser que seu modelo seja acessível publicamente, você pode protegê-lo armazenando-o em um contêiner de armazenamento privado. Para obter informações sobre como implantar um modelo que requer um token SAS (assinatura de acesso compartilhado), consulte [implantar modelo privado com o token SAS](secure-template-with-sas-token.md). Para percorrer um tutorial, consulte [tutorial: integrar Azure Key Vault no Resource Manager implantação de modelo](template-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Implantar do Azure cloud Shell

Você pode usar o [Azure cloud Shell](https://shell.azure.com) para implantar o modelo. Para implantar um modelo externo, forneça o URI do modelo. Para implantar um modelo local, você deve primeiro carregar o modelo na conta de armazenamento para seu Cloud Shell. Para carregar arquivos no Shell, selecione o ícone de menu **carregar/baixar arquivos** na janela do Shell.

Para abrir o Cloud Shell, navegue até [https://shell.azure.com](https://shell.azure.com)ou selecione **Try-it** na seguinte seção de código:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Para colar o código no Shell, clique com o botão direito do mouse dentro do Shell e selecione **colar**.

## <a name="pass-parameter-values"></a>Passar valores de parâmetro

Para passar valores de parâmetro, você pode usar parâmetros embutidos ou um arquivo de parâmetro.

### <a name="inline-parameters"></a>Parâmetros embutidos

Para passar parâmetros embutidos, forneça os nomes do parâmetro com o comando `New-AzResourceGroupDeployment`. Por exemplo, para passar uma cadeia de caracteres e uma matriz para um modelo, use:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Você também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro embutido.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obter um valor de parâmetro de um arquivo é útil quando você precisa fornecer valores de configuração. Por exemplo, você pode fornecer [valores de Cloud-init para uma máquina virtual do Linux](../../virtual-machines/linux/using-cloud-init.md).

Se você precisar passar uma matriz de objetos, crie tabelas de hash no PowerShell e adicione-as a uma matriz. Passe essa matriz como um parâmetro durante a implantação.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Arquivos de parâmetro

Em vez de passar parâmetros como valores embutidos em seu script, você pode achar mais fácil usar um arquivo JSON que contenha os valores de parâmetro. O arquivo de parâmetro pode ser um arquivo local ou um arquivo externo com um URI acessível.

Para obter mais informações sobre o arquivo de parâmetro, consulte [criar arquivo de parâmetro do Resource Manager](parameter-files.md).

Para passar um arquivo de parâmetro local, use o parâmetro **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar um arquivo de parâmetro externo, use o parâmetro **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Implantações de modelo de teste

Para testar seus valores de modelo e parâmetro sem realmente implantar nenhum recurso, use [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Se nenhum erro for detectado, o comando será concluído sem uma resposta. Se um erro for detectado, o comando retornará uma mensagem de erro. Por exemplo, a passagem de um valor incorreto para o SKU da conta de armazenamento retorna o seguinte erro:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se o modelo tiver um erro de sintaxe, o comando retornará um erro indicando que ele não pôde analisar o modelo. A mensagem indica o número de linha e a posição do erro de análise.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Passos seguintes

- Para reverter para uma implantação bem-sucedida quando você receber um erro, consulte [reverter em caso de erro para a implantação bem-sucedida](rollback-on-error.md).
- Para especificar como lidar com recursos que existem no grupo de recursos, mas que não estão definidos no modelo, consulte [Azure Resource Manager modos de implantação](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](template-syntax.md).
- Para obter informações sobre como implantar um modelo que requer um token SAS, consulte [implantar o modelo privado com o token SAS](secure-template-with-sas-token.md).
