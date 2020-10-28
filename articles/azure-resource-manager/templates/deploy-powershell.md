---
title: Implementar recursos com PowerShell e modelo
description: Utilize o Azure Resource Manager e a Azure PowerShell para mobilizar recursos para a Azure. Os recursos são definidos num modelo do Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 5266aa51422dce6dfa4b82238e905f4f630ccf48
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668564"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Implementar recursos com modelos ARM e Azure PowerShell

Este artigo explica como usar a Azure PowerShell com modelos de Gestor de Recursos Azure (modelos ARM) para implantar os seus recursos no Azure. Se não está familiarizado com os conceitos de implantação e gestão das suas soluções Azure, consulte a [visão geral da implementação do modelo](overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de um modelo para implementar. Se ainda não tiver um, faça o download e guarde um modelo de [exemplo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) do repo dos modelos Azure Quickstart. O nome do ficheiro local utilizado neste artigo é **c:\MyTemplates\azuredeploy.jsem** .

Tem de instalar o Azure PowerShell e ligar-se ao Azure:

- **Instale cmdlets Azure PowerShell no seu computador local.** Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
- **Ligue-se ao Azure utilizando [o Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Se tiver várias subscrições do Azure, também poderá ter de executar [o Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Para obter mais informações, consulte [utilizar várias subscrições do Azure](/powershell/azure/manage-subscriptions-azureps).

Se não tiver o PowerShell instalado, pode utilizar a Cloud Shell. Para obter mais informações, consulte [os modelos ARM da Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Âmbito de implantação

Pode direcionar a sua implementação para um grupo de recursos, subscrição, grupo de gestão ou inquilino. Dependendo do alcance da implantação, utiliza-se diferentes comandos.

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

Para cada âmbito, o utilizador que implementa o modelo deve ter as permissões necessárias para criar recursos.

## <a name="deploy-local-template"></a>Implementar um modelo local

Pode implementar um modelo a partir da sua máquina local ou um que seja armazenado externamente. Esta secção descreve a implementação de um modelo local.

Se estiver a implantar para um grupo de recursos que não existe, crie o grupo de recursos. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Pode chegar a 90 caracteres. O nome não pode acabar num período.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Para implementar um modelo local, utilize o `-TemplateFile` parâmetro no comando de implantação. O exemplo a seguir também mostra como definir um valor de parâmetro que vem do modelo.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

A implementação pode demorar alguns minutos a concluir.

## <a name="deploy-remote-template"></a>Implementar o modelo remoto

Em vez de armazenar modelos ARM na sua máquina local, pode preferir guardá-los num local externo. Pode armazenar modelos num repositório de controlo de código fonte (como o GitHub). Em alternativa, pode armazená-los numa conta de armazenamento do Azure para acesso partilhado na sua organização.

Se estiver a implantar para um grupo de recursos que não existe, crie o grupo de recursos. O nome do grupo de recursos só pode incluir caracteres alfanuméricos, períodos, sublinhados, hífens e parênteses. Pode chegar a 90 caracteres. O nome não pode acabar num período.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

Para implementar um modelo externo, utilize o parâmetro `-TemplateUri`.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

O exemplo anterior requer um URI acessível ao público para o modelo, que funciona para a maioria dos cenários porque o seu modelo não deve incluir dados sensíveis. Se precisar de especificar dados sensíveis (como uma palavra-passe de administração), passe esse valor como parâmetro seguro. No entanto, se quiser gerir o acesso ao modelo, considere usar [as especificações do modelo.](#deploy-template-spec)

## <a name="deployment-name"></a>Nome de implantação

Ao colocar um modelo ARM, pode dar um nome à implantação. Este nome pode ajudá-lo a recuperar a implantação do histórico de implantação. Se não fornecer um nome para a implementação, o nome do ficheiro de modelo é usado. Por exemplo, se implementar um modelo nomeado `azuredeploy.json` e não especificar um nome de implantação, a implementação é nomeada `azuredeploy` .

Sempre que executa uma implantação, uma entrada é adicionada ao histórico de implantação do grupo de recursos com o nome de implantação. Se executar outra implantação e lhe der o mesmo nome, a entrada anterior é substituída pela implementação atual. Se pretender manter entradas únicas no histórico de implantação, dê a cada implementação um nome único.

Para criar um nome único, pode atribuir um número aleatório.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Ou adicionar um valor de data.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Se executar implementações simultâneas para o mesmo grupo de recursos com o mesmo nome de implantação, apenas a última implementação é concluída. Quaisquer implementações com o mesmo nome que não tenham terminado são substituídas pela última implantação. Por exemplo, se executar uma implantação com o nome `newStorage` de uma conta de armazenamento chamada , e ao mesmo tempo executar outra `storage1` implantação com o nome de uma `newStorage` conta de armazenamento chamada , implementa `storage2` apenas uma conta de armazenamento. A conta de armazenamento resultante é `storage2` nomeada.

No entanto, se executar uma implantação com o nome `newStorage` de uma conta de armazenamento chamada , e imediatamente após a sua `storage1` conclusão, executar outra implantação com o nome `newStorage` de uma conta de armazenamento chamada , `storage2` então tem duas contas de armazenamento. Um tem o nome `storage1` , e o outro chama-se `storage2` . Mas só tens uma entrada na história da implantação.

Quando especificar um nome único para cada implantação, pode executá-los simultaneamente sem conflitos. Se executar uma implantação com o nome `newStorage1` de uma conta de armazenamento chamada , e ao mesmo tempo executar outra `storage1` implantação com o nome de uma `newStorage2` conta de armazenamento , `storage2` então tem duas contas de armazenamento e duas entradas no histórico de implantação.

Para evitar conflitos com implementações simultâneas e para garantir entradas únicas no histórico de implantação, dê a cada implementação um nome único.

## <a name="deploy-template-spec"></a>Implementar especificação de modelo

Em vez de implementar um modelo local ou remoto, pode criar uma [especificação de modelo](template-specs.md). A especificação do modelo é um recurso na sua subscrição Azure que contém um modelo ARM. Torna-se fácil partilhar o modelo de forma segura com os utilizadores da sua organização. Você usa o controlo de acesso baseado em funções Azure (Azure RBAC) para conceder acesso à especificação do modelo. Esta funcionalidade encontra-se atualmente em pré-visualização.

Os exemplos a seguir mostram como criar e implementar uma especificação de modelo. Estes comandos só estão disponíveis se se [ter inscrito para a pré-visualização](https://aka.ms/templateSpecOnboarding).

Em primeiro lugar, crie a especificação do modelo fornecendo o modelo ARM.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Em seguida, obtenha o ID para especificação de modelo e implemente-o.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Para obter mais informações, consulte [as especificações do modelo do Gestor de Recursos Azure (Preview)](template-specs.md).

## <a name="preview-changes"></a>Pré-visualizar alterações

Antes de implementar o seu modelo, pode visualizar as alterações que o modelo irá fazer para o seu ambiente. Utilize a [operação "e se"](template-deploy-what-if.md) para verificar se o modelo faz as alterações que espera. E se também valida o modelo para erros.

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

Em vez de transmitir parâmetros como valores inline no seu script, poderá considerar mais fácil utilizar um ficheiro JSON que contenha os valores dos parâmetros. O ficheiro de parâmetros pode ser um ficheiro local ou um ficheiro externo com um URI acessível.

Para obter mais informações sobre o ficheiro de parâmetros, veja [Criar ficheiro de parâmetros do Resource Manager](parameter-files.md).

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

## <a name="next-steps"></a>Passos seguintes

- Para voltar a uma implementação bem sucedida quando tiver um erro, consulte [o Reversão do erro para uma implementação bem sucedida](rollback-on-error.md).
- Para especificar como lidar com os recursos que existem no grupo de recursos mas não estão definidos no modelo, consulte os [modos de implementação do Gestor de Recursos Azure](deployment-modes.md).
- Para entender como definir parâmetros no seu modelo, consulte [a estrutura e sintaxe dos modelos ARM](template-syntax.md).
- Para obter informações sobre a implementação de um modelo que requer um token SAS, consulte [implementar o modelo privado com o token SAS](secure-template-with-sas-token.md).
