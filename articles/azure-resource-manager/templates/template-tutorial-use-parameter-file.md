---
title: Tutorial – usar arquivo de parâmetro para implantar o modelo
description: Use arquivos de parâmetro que contenham os valores a serem usados para implantar seu modelo de Azure Resource Manager.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: abafa00febe0431acf5c678cc179c6c114fa9179
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472374"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Tutorial: usar arquivos de parâmetro para implantar o modelo do Resource Manager

Neste tutorial, você aprenderá a usar [arquivos de parâmetro](parameter-files.md) para armazenar os valores passados durante a implantação. Nos tutoriais anteriores, você usou parâmetros embutidos com o comando de implantação. Essa abordagem funcionou para testar seu modelo, mas ao automatizar implantações, pode ser mais fácil passar um conjunto de valores para seu ambiente. Os arquivos de parâmetro facilitam o pacote de valores de parâmetro para um ambiente específico. Neste tutorial, você criará arquivos de parâmetro para ambientes de desenvolvimento e produção. Leva cerca de **12 minutos** para ser concluída.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre marcas](template-tutorial-add-tags.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Modelo de revisão

Seu modelo tem muitos parâmetros que você pode fornecer durante a implantação. No final do tutorial anterior, seu modelo ficou como:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Esse modelo funciona bem, mas agora você deseja gerenciar facilmente os parâmetros que você passa para o modelo.

## <a name="add-parameter-files"></a>Adicionar arquivos de parâmetro

Os arquivos de parâmetro são arquivos JSON com uma estrutura semelhante ao seu modelo. No arquivo, você fornece os valores de parâmetro que deseja passar durante a implantação.

Em VS Code, crie um novo arquivo com o conteúdo a seguir. Salve o arquivo com o nome **azuredeploy. Parameters. dev. JSON**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Esse arquivo é o arquivo de parâmetro para o ambiente de desenvolvimento. Observe que ele usa Standard_LRS para a conta de armazenamento, os recursos de nomes com um prefixo de **desenvolvimento** e define a marca de **ambiente** como **dev**.

Novamente, crie um novo arquivo com o conteúdo a seguir. Salve o arquivo com o nome **azuredeploy. Parameters. Prod. JSON**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Esse arquivo é o arquivo de parâmetro para o ambiente de produção. Observe que ele usa Standard_GRS para a conta de armazenamento, os recursos de nomes com um prefixo **contoso** e define a marca de **ambiente** como **produção**. Em um ambiente de produção real, você também desejaria usar um serviço de aplicativo com uma SKU diferente de gratuita, mas continuaremos a usar essa SKU para este tutorial.

## <a name="deploy-template"></a>Implementar o modelo

Use CLI do Azure ou Azure PowerShell para implantar o modelo.

Como um teste final do modelo, vamos criar dois novos grupos de recursos. Um para o ambiente de desenvolvimento e outro para o ambiente de produção.

Primeiro, implantaremos no ambiente de desenvolvimento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

Agora, implantaremos no ambiente de produção.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando os grupos de recursos do portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Você verá os dois novos grupos de recursos implantados neste tutorial.
1. Selecione o grupo de recursos e exiba os recursos implantados. Observe que eles correspondem aos valores que você especificou em seu arquivo de parâmetro para esse ambiente.

## <a name="clean-up-resources"></a>Limpar recursos

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**. Se você tiver concluído esta série, terá três grupos de recursos para excluir-MyResource Group, myResourceGroupDev e myResourceGroupProd.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Parabéns, você concluiu esta introdução à implantação de modelos no Azure. Informe-nos se você tiver comentários e sugestões na seção de comentários. Obrigado!

Você está pronto para acessar conceitos mais avançados sobre modelos. O próximo tutorial apresenta mais detalhes sobre o uso da documentação de referência de modelo para ajudar na definição de recursos a serem implantados.

> [!div class="nextstepaction"]
> [Utilizar referência de modelo](template-tutorial-create-encrypted-storage-accounts.md)
