---
title: Tutorial - Implementar um modelo local de Gestor de Recursos Azure
description: Saiba como implementar um modelo de Gestor de Recursos Azure a partir do seu computador local
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7f134bb836d05d006ef2e474ea48382a671957fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188829"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Tutorial: Implementar um modelo local de Gestor de Recursos Azure

Aprenda a implementar um modelo de Gestor de Recursos Azure a partir da sua máquina local. Leva cerca de **8 minutos** para ser concluído.

Este tutorial é o primeiro de uma série. À medida que progride através da série, modulará o modelo criando um modelo ligado, armazena o modelo ligado numa conta de armazenamento, e segura o modelo ligado usando token SAS, e aprende-se a criar um pipeline DevOp para implementar modelos. Esta série centra-se na implementação do modelo.  Se quiser aprender o desenvolvimento do modelo, consulte os [tutoriais de principiantes.](./template-tutorial-create-first-template.md)

## <a name="get-tools"></a>Obter ferramentas

Vamos começar por ter a certeza de que tem as ferramentas necessárias para implementar modelos.

### <a name="command-line-deployment"></a>Implantação da linha de comando

Precisa de Azure PowerShell ou Azure CLI para implementar o modelo. Para as instruções de instalação, consulte:

- [Instalar a PowerShell Azure](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instale o Azure CLI no Linux](/cli/azure/install-azure-cli-linux)

Depois de instalar o Azure PowerShell ou o Azure CLI, certifique-se de que faz o início. Para obter ajuda, consulte [Iniciar sessão - PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Iniciar sessão - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Editor (Opcional)

Os modelos são ficheiros JSON. Para rever/editar modelos, precisa de um bom editor JSON. Recomendamos o Código do Estúdio Visual com a extensão ferramentas de gestor de recursos. Se precisar de instalar estas ferramentas, consulte [o Código do Estúdio Visual para criar modelos](use-vs-code-to-create-template.md)de Gestor de Recursos Azure .

## <a name="review-template"></a>Modelo de revisão

O modelo implementa uma conta de armazenamento, plano de serviço de aplicações e aplicação web. Se você está interessado em criar o modelo, você pode passar por [tutorial sobre modelos Quickstart](template-tutorial-quickstart-template.md). No entanto, não é necessário completar este tutorial.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar números e letras minúsculas apenas. O nome deve ser único. No modelo, o nome da conta de armazenamento é o nome do projeto com "loja" anexado, e o nome do projeto deve estar entre 3 e 11 caracteres. Assim, o nome do projeto deve satisfazer os requisitos de nome da conta de armazenamento e tem menos de 11 caracteres.

Guarde uma cópia do modelo para o seu computador local com a extensão .json, por exemplo, azuredeploy.json. Você implanta este modelo mais tarde no tutorial.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para começar a trabalhar com o Azure PowerShell/Azure CLI para implementar um modelo, inscreva-se com as suas credenciais Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias subscrições do Azure, selecione a subscrição que pretende utilizar:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implementar um modelo, especifice um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com o Azure CLI ou o Azure PowerShell. Selecione os separadores na secção de código seguinte para escolher entre o Azure PowerShell e o Azure CLI. Os exemplos do CLI neste artigo são escritos para a concha bash.

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

Para saber mais sobre o modelo de implantação utilizando o Azure PowerShell, consulte [os recursos de implantação com modelos](./deploy-powershell.md)de Gestor de Recursos e Azure PowerShell .

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

Para saber mais sobre o modelo de implantação utilizando o Azure CLI, consulte [os recursos de implantação com modelos](./deploy-cli.md)de Gestor de Recursos e O CLI Azure .

---

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que implementou ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a implementar um modelo local. No tutorial seguinte, separa-se o modelo num modelo principal e num modelo ligado, e aprende a armazenar e a fixar o modelo ligado.

> [!div class="nextstepaction"]
> [Implementar um modelo ligado](./deployment-tutorial-linked-template.md)
