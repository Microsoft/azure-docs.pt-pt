---
title: Tutorial - use ficheiro de parâmetro para implementar modelo
description: Utilize ficheiros de parâmetros que contenham os valores a utilizar para a implementação do seu modelo de Gestor de Recursos Azure.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b91041b96a3819dbace3898d92226f0351f0f973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80411509"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Tutorial: Utilize ficheiros de parâmetros para implementar o seu modelo ARM

Neste tutorial, aprende-se a utilizar [ficheiros de parâmetros](parameter-files.md) para armazenar os valores que passa durante a implementação. Nos tutoriais anteriores, utilizou parâmetros inline com o seu comando de implantação. Esta abordagem funcionou para testar o seu modelo de Gestor de Recursos Azure (ARM), mas ao automatizar implementações pode ser mais fácil passar um conjunto de valores para o seu ambiente. Os ficheiros de parâmetros facilitam o pacote de valores de parâmetros para um ambiente específico. Neste tutorial, você vai criar arquivos de parâmetros para ambientes de desenvolvimento e produção. Leva cerca de **12 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre etiquetas,](template-tutorial-add-tags.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

O seu modelo tem muitos parâmetros que pode fornecer durante a implementação. No final do tutorial anterior, o seu modelo parecia:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Este modelo funciona bem, mas agora você quer gerir facilmente os parâmetros que você passa para o modelo.

## <a name="add-parameter-files"></a>Adicionar ficheiros de parâmetros

Os ficheiros do parâmetro são ficheiros JSON com uma estrutura semelhante ao seu modelo. No ficheiro, fornece os valores do parâmetro que pretende passar durante a implementação.

No Código VS, crie um novo ficheiro com o seguinte conteúdo. Guarde o ficheiro com o nome **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Este ficheiro é o seu ficheiro de parâmetros para o ambiente de desenvolvimento. Note que usa Standard_LRS para a conta de armazenamento, nomeia recursos com um prefixo **de v,** e define a etiqueta **Ambiente** para **Dev**.

Mais uma vez, crie um novo ficheiro com o seguinte conteúdo. Guarde o ficheiro com o nome **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Este ficheiro é o seu ficheiro de parâmetros para o ambiente de produção. Note que utiliza Standard_GRS para a conta de armazenamento, nomeia recursos com prefixo **contoso,** e define a etiqueta **Ambiente** para **produção.** Num ambiente de produção real, você também gostaria de usar um serviço de aplicações com um SKU que não seja gratuito, mas continuaremos a usar esse SKU para este tutorial.

## <a name="deploy-template"></a>Implementar o modelo

Utilize o Azure CLI ou o Azure PowerShell para implementar o modelo.

Como teste final do seu modelo, vamos criar dois novos grupos de recursos. Um para o ambiente de dev e outro para o ambiente de produção.

Primeiro, vamos para o ambiente de dev.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Agora, vamos para o ambiente de produção.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Se a implantação falhar, utilize o interruptor **de depuração** com o comando de implantação para mostrar os registos de depuração.  Também pode utilizar o interruptor **verboso** para mostrar os registos completos de depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando os grupos de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Vê os dois novos grupos de recursos que implantou neste tutorial.
1. Selecione um grupo de recursos e veja os recursos implantados. Note que correspondem aos valores especificados no seu ficheiro de parâmetros para esse ambiente.

## <a name="clean-up-resources"></a>Limpar recursos

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**. Se tiver concluído esta série, tem três grupos de recursos para eliminar - myResourceGroup, myResourceGroupDev e myResourceGroupProd.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Parabéns, terminou esta introdução para implementar modelos para o Azure. Informe-nos se tiver algum comentário e sugestões na secção de feedback. Obrigado!

A próxima série tutorial entra em mais detalhes sobre a implantação de modelos.

> [!div class="nextstepaction"]
> [Implementar um modelo local](./deployment-tutorial-local-template.md)
