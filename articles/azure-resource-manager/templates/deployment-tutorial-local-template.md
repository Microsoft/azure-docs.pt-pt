---
title: Tutorial - Implementar um modelo local de gestor de recursos Azure
description: Saiba como implementar um modelo de Gestor de Recursos Azure (modelo ARM) a partir do computador local
ms.date: 02/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: d8d54acfa345994edcc401170e70495b3826bfdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384236"
---
# <a name="tutorial-deploy-a-local-arm-template"></a>Tutorial: Implementar um modelo arm local

Saiba como implementar um modelo de Gestor de Recursos Azure (modelo ARM) a partir da sua máquina local. Leva cerca de **8 minutos** para ser completado.

Este tutorial é o primeiro de uma série. À medida que progride através da série, modulará o modelo criando um modelo ligado, armazena o modelo ligado numa conta de armazenamento e protege o modelo ligado usando o token SAS, e aprende a criar um pipeline DevOps para implementar modelos. Esta série foca-se na implementação do modelo. Se quiser aprender o desenvolvimento do modelo, consulte os [tutoriais de principiante.](./template-tutorial-create-first-template.md)

## <a name="get-tools"></a>Obter ferramentas

Comecemos por ter a certeza de que tem as ferramentas necessárias para implementar modelos.

### <a name="command-line-deployment"></a>Implantação da linha de comando

Você precisa de Azure PowerShell ou Azure CLI para implementar o modelo. Para as instruções de instalação, consulte:

- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instale o Azure CLI no Linux](/cli/azure/install-azure-cli-linux)
- [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

Depois de instalar o Azure PowerShell ou o Azure CLI, certifique-se de que faz sê-lo pela primeira vez. Para obter ajuda, consulte [Iniciar s-se-in - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Iniciar sação - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (Opcional)

Os modelos são ficheiros JSON. Para rever/editar modelos, precisa de um bom editor JSON. Recomendamos o Código do Estúdio Visual com a extensão de Ferramentas do Gestor de Recursos. Se precisar de instalar estas ferramentas, consulte [Quickstart: Crie modelos ARM com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).

## <a name="review-template"></a>Modelo de revisão

O modelo implementa uma conta de armazenamento, plano de serviço de aplicações e aplicativo web. Se estiver interessado em criar o modelo, pode passar por [um tutorial sobre modelos Quickstart](template-tutorial-quickstart-template.md). No entanto, não é necessário completar este tutorial.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas. O nome tem de ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com **a loja** anexada, e o nome do projeto deve estar entre 3 e 11 caracteres. Assim, o nome do projeto deve satisfazer os requisitos de nome da conta de armazenamento e tem menos de 11 caracteres.

Guarde uma cópia do modelo para o seu computador local com a extensão _.json,_ por exemplo, _azuredeploy.jsligado_. Você implementa este modelo mais tarde no tutorial.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para começar a trabalhar com o Azure PowerShell/Azure CLI para implementar um modelo, inicie súmis com as suas credenciais Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias subscrições do Azure, selecione a subscrição que pretende utilizar. Substitua `[SubscriptionID/SubscriptionName]` e os suportes quadrados `[]` por informações de subscrição:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

Quando implementa um modelo, especifica um grupo de recursos que conterá os recursos. Antes de executar o comando de implementação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell. Selecione os separadores na seguinte secção de código para escolher entre Azure PowerShell e Azure CLI. Os exemplos do CLI neste artigo são escritos para a concha bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Implementar o modelo

Utilize uma ou ambas as opções de implementação para implementar o modelo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Para saber mais sobre a implementação do modelo utilizando o Azure PowerShell, consulte [implementar recursos com modelos ARM e Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Para saber mais sobre a implementação do modelo utilizando o Azure CLI, consulte [recursos de implantação com modelos ARM e Azure CLI](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos que implementou eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um modelo local. No tutorial seguinte, você separa o modelo em um modelo principal e um modelo ligado, e aprender a armazenar e proteger o modelo ligado.

> [!div class="nextstepaction"]
> [Implementar um modelo ligado](./deployment-tutorial-linked-template.md)
