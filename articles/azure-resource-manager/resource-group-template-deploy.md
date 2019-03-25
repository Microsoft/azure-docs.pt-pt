---
title: Implementar recursos com o PowerShell e o modelo | Documentos da Microsoft
description: Utilize o Azure Resource Manager e o Azure PowerShell para implementar recursos no Azure. Os recursos são definidos num modelo do Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: tomfitz
ms.openlocfilehash: 8005b187f300375b62c254516a61f4993675b0b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403113"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementar recursos com modelos do Resource Manager e do Azure PowerShell

Saiba como utilizar o Azure PowerShell com modelos do Resource Manager para implementar os seus recursos no Azure. Para obter mais informações sobre os conceitos de implantar e gerenciar suas soluções do Azure, consulte [descrição geral do Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Escopo da implantação

Pode direcionar a sua implementação para uma subscrição do Azure ou um grupo de recursos numa subscrição. Na maioria dos casos, será o direcionamento de implementação para um grupo de recursos. Utilize implementações de subscrição para aplicar políticas e as atribuições de funções a subscrição. Também utilizar implementações de subscrição para criar um grupo de recursos e implementar recursos no mesmo. Dependendo do escopo da implantação, é usar comandos diferentes.

Para implementar um **grupo de recursos**, utilize [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Para implementar um **subscrição**, utilize [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Os exemplos neste artigo utilizam implementações do grupo de recursos. Para obter mais informações sobre implementações de subscrição, veja [criar grupos de recursos e recursos ao nível da subscrição](deploy-to-subscription.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um modelo para implementar. Se ainda não tiver uma, transferir e guardar um [modelo de exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do repositório de modelos de início rápido do Azure. O nome de ficheiro local utilizado neste artigo é **c:\MyTemplates\azuredeploy.json**.

A menos que utilize o [do Azure Cloud shell](#deploy-templates-from-azure-cloud-shell) para implementar modelos, tem de instalar o Azure PowerShell e ligue-se para o Azure:

- **Instale cmdlets Azure PowerShell no seu computador local.** Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Ligar ao Azure, utilizando [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Se tiver várias subscrições do Azure, também poderá ter de ser executado [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para obter mais informações, consulte [utilizar várias subscrições do Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Implementar o modelo de local

O exemplo seguinte cria um grupo de recursos e implementa um modelo a partir do seu computador local. O nome do grupo de recursos só pode incluir carateres alfanuméricos, pontos finais, carateres de sublinhado, hífenes e parênteses. Pode ser até 90 carateres. Não pode terminar com um período.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implementação pode demorar alguns minutos a concluir.

## <a name="deploy-remote-template"></a>Implementar modelo remoto

Em vez de armazenar modelos do Resource Manager no seu computador local, pode armazená-las num local externo. Pode armazenar modelos num repositório de controle de origem (por exemplo, o GitHub). Em alternativa, pode armazená-los numa conta de armazenamento do Azure para acesso partilhado na sua organização.

Para implementar um modelo externo, utilize o **TemplateUri** parâmetro. Utilize o URI no exemplo para implementar o modelo de exemplo do GitHub.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior exige um URI publicamente acessível para o modelo, que funciona na maioria dos cenários, porque o modelo não deve incluir dados confidenciais. Se tiver de especificar os dados confidenciais (como uma palavra-passe de administrador), passe esse valor como um parâmetro seguro. No entanto, se não pretender que o modelo para estar acessível publicamente, pode protegê-los armazenando-os num contentor do armazenamento privado. Para obter informações sobre como implementar um modelo que precisa de um token de assinatura (SAS) de acesso partilhado, consulte [implementar modelo privado com o SAS token](resource-manager-powershell-sas-token.md). Para seguir um tutorial, veja [Tutorial: Integrar o Azure Key Vault na implementação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Implementar a partir do Azure Cloud shell

Pode utilizar o [Azure Cloud Shell](https://shell.azure.com) para implementar o modelo. Para implementar um modelo externo, forneça o URI do modelo. Para implementar um modelo de local, primeiro tem de carregar o modelo para a conta de armazenamento para o Cloud Shell. Para carregar ficheiros para o shell, selecione o **carregar/transferir ficheiros** ícone de menu da janela do shell.

Para abrir o Cloud shell, navegue até [ https://shell.azure.com ](https://shell.azure.com), ou selecione **Try It** da seção de código seguinte:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Colar o código no shell, clique com o botão direito dentro do shell e, em seguida, selecione **colar**.

## <a name="redeploy-when-deployment-fails"></a>Implementar novamente quando ocorre uma falha de implementação

Quando uma implementação falhar, pode implementar automaticamente novamente uma implementação anterior, com êxito do seu histórico de implementação. Para especificar a nova implementação, utilize o `-RollbackToLastDeployment` ou `-RollBackDeploymentName` parâmetro no comando de implementação.

Para utilizar esta opção, as suas implementações tem de ter nomes exclusivos para que eles podem ser identificados na história. Se não tem nomes exclusivos, a falha na implementação atual poderá substituir a implementação bem-sucedida anterior na história. Só pode utilizar esta opção com implementações de nível de raiz. Implementações a partir de um modelo aninhado não estão disponíveis para reimplantação.

Para Reimplementar a última implementação efetuada com êxito, adicione o `-RollbackToLastDeployment` parâmetro como um sinalizador.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Para Reimplementar uma implementação específica, utilize o `-RollBackDeploymentName` parâmetro e forneça o nome da implementação.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

A implementação especificada tem de ter foi concluída com êxito.

## <a name="pass-parameter-values"></a>Transmita os valores de parâmetro

Para passar valores de parâmetros, pode utilizar parâmetros inline ou um ficheiro de parâmetros. Os exemplos anteriores neste artigo mostram parâmetros inline.

### <a name="inline-parameters"></a>Parâmetros inline

Para passar parâmetros inline, forneça os nomes de parâmetro com o `New-AzResourceGroupDeployment` comando. Por exemplo, para passar uma cadeia de caracteres e a matriz a um modelo, utilize:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Também pode obter o conteúdo do arquivo e fornecer esse conteúdo como um parâmetro de inline.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Obtendo um valor de parâmetro de um arquivo é útil quando tem de fornecer valores de configuração. Por exemplo, pode fornecer [valores de cloud-init para uma máquina virtual Linux](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Ficheiros de parâmetros

Em vez de passar parâmetros como valores de inline no seu script, talvez ache mais fácil de utilizar um ficheiro JSON que contém os valores de parâmetro. O ficheiro de parâmetros pode ser um ficheiro local ou um arquivo externo com um URI acessível.

O ficheiro de parâmetros tem de estar no seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observe que a secção de parâmetros inclui um nome de parâmetro que corresponda ao parâmetro definido no seu modelo (storageAccountType). O ficheiro de parâmetros contém um valor para o parâmetro. Este valor é automaticamente passado para o modelo durante a implementação. Pode criar mais do que um ficheiro de parâmetros e, em seguida, passar o ficheiro de parâmetros adequada para o cenário.

Copie o exemplo anterior e guarde-o como um arquivo chamado `storage.parameters.json`.

Para passar um ficheiro de parâmetros local, utilize o **TemplateParameterFile** parâmetro:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para passar um ficheiro de parâmetros externo, utilize o **TemplateParameterUri** parâmetro:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Precedência de parâmetro

Pode usar parâmetros inline e um ficheiro de parâmetros local na mesma operação de implementação. Por exemplo, pode especificar alguns valores no ficheiro de parâmetros local e adicionar outros embutidos valores durante a implementação. Se fornecer valores para um parâmetro no ficheiro de parâmetros local e inline, o valor de inline tem precedência.

No entanto, quando utiliza um ficheiro de parâmetros externo, não poder passar os outros valores de qualquer inline ou a partir de um ficheiro local. Se especificar um ficheiro de parâmetros nos **TemplateParameterUri** parâmetro, inline todos os parâmetros são ignorados. Fornece todos os valores de parâmetros no ficheiro externo. Se o seu modelo inclui um valor confidencial que não pode incluir no ficheiro de parâmetros, adicione esse valor para um cofre de chaves ou fornecer dinamicamente tudo embutido de valores de parâmetro.

### <a name="parameter-name-conflicts"></a>Conflitos de nomes de parâmetro

Se o seu modelo inclui um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell, PowerShell apresenta o parâmetro a partir do seu modelo com o sufixo **FromTemplate**. Por exemplo, um parâmetro denominado **ResourceGroupName** em seu conflitos de modelo com o **ResourceGroupName** parâmetro no [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet. Lhe for pedido para fornecer um valor para **ResourceGroupNameFromTemplate**. Em geral, deve evitar essa confusão ao não nomear os parâmetros com o mesmo nome como parâmetros utilizados para operações de implementação.

## <a name="test-template-deployments"></a>Implementações do modelo de teste

Para testar seus valores de modelo e o parâmetro sem ter de implementar, na verdade, todos os recursos, utilize [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Se não são detetados erros, o comando terminar sem uma resposta. Se for detetado um erro, o comando devolve uma mensagem de erro. Por exemplo, passando um valor incorreto para a conta de armazenamento SKU, devolve o erro seguinte:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Se o seu modelo tem um erro de sintaxe, o comando devolve um erro que indica que não foi possível analisar o modelo. A mensagem indica o número de linha e a posição de erro de análise.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Passos Seguintes

- Para com segurança implementar o seu serviço mais do que uma região, veja [Gestor de implementação do Azure](deployment-manager-overview.md).
- Para especificar como lidar com recursos de que existem no grupo de recursos, mas não estão definidos no modelo, consulte [modos de implementação Azure Resource Manager](deployment-modes.md).
- Para compreender como definir parâmetros no seu modelo, veja [compreender a estrutura e a sintaxe de modelos Azure Resource Manager](resource-group-authoring-templates.md).
- Para obter informações sobre como implementar um modelo que necessita de um token SAS, consulte [implementar modelo privado com o SAS token](resource-manager-powershell-sas-token.md).
