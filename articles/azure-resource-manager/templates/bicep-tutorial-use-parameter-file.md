---
title: Tutorial - use o arquivo de parâmetros para implementar o ficheiro Bicep do Gestor de Recursos Azure
description: Utilize ficheiros de parâmetros que contenham os valores a utilizar para implantar o seu ficheiro Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca3a73cde9549bfcdfd47bc4f1955904fac69d1c
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632361"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Tutorial: Utilize ficheiros de parâmetros para implementar o ficheiro Bicep do Gestor de Recursos Azure

Neste tutorial, aprende-se a usar [ficheiros de parâmetros](parameter-files.md) para armazenar os valores que transmite durante a implementação. Nos tutoriais anteriores, utilizou parâmetros inline com o seu comando de implantação. Esta abordagem funcionou para testar o seu ficheiro Bicep, mas ao automatizar implementações pode ser mais fácil passar um conjunto de valores para o seu ambiente. Os ficheiros de parâmetros facilitam a embalagem dos valores dos parâmetros para um ambiente específico. Utilize o mesmo ficheiro de parâmetro JSON ao implementar um modelo JSON. Neste tutorial, criará ficheiros de parâmetros para ambientes de desenvolvimento e produção. Leva cerca de **12 minutos** para ser completado.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre tags,](bicep-tutorial-add-tags.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

O seu ficheiro Bicep tem muitos parâmetros que pode fornecer durante a implementação. No final do tutorial anterior, o seu ficheiro Bicep parecia:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Este ficheiro Bicep funciona bem, mas agora quer gerir facilmente os parâmetros que passa para o ficheiro Bicep.

## <a name="add-parameter-files"></a>Adicionar ficheiros de parâmetros

Os ficheiros de parâmetros são ficheiros JSON com uma estrutura semelhante aos modelos JSON. No ficheiro, fornece os valores de parâmetro que pretende transmitir durante a implementação.

Dentro do ficheiro de parâmetros, fornece valores para os parâmetros no seu ficheiro Bicep. O nome de cada parâmetro no seu ficheiro de parâmetro deve corresponder ao nome de um parâmetro no seu ficheiro Bicep. O nome é insensível ao caso, mas para ver facilmente os valores correspondentes recomendamos que corresponda ao invólucro do ficheiro Bicep.

Não tens de fornecer um valor para cada parâmetro. Se um parâmetro não especificado tiver um valor padrão, esse valor é utilizado durante a implantação. Se um parâmetro não tiver um valor padrão e não estiver especificado no ficheiro do parâmetro, é solicitado que forneça um valor durante a implementação.

Não é possível especificar um nome de parâmetro no seu ficheiro de parâmetro que não corresponda a um nome de parâmetro no ficheiro Bicep. Obtém-se um erro quando são fornecidos parâmetros desconhecidos.

No Código do Estúdio Visual, crie um novo ficheiro com o seguinte conteúdo. Guarde o ficheiro com o nome _azuredeploy.parameters.dev.jsligado_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Este ficheiro é o seu arquivo de parâmetros para o ambiente de desenvolvimento. Note que utiliza **Standard_LRS** para a conta de armazenamento, nomeia recursos com um prefixo **dev,** e define a `Environment` etiqueta para **Dev**.

Mais uma vez, crie um novo ficheiro com o seguinte conteúdo. Guarde o ficheiro com o nome _azuredeploy.parameters.prod.jsligado_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Este ficheiro é o seu arquivo de parâmetros para o ambiente de produção. Note que utiliza **Standard_GRS** para a conta de armazenamento, nomeia recursos com um prefixo **contoso,** e define a `Environment` etiqueta para a **Produção**. Num ambiente de produção real, você também gostaria de usar um serviço de aplicações com um SKU diferente de gratuito, mas continuaremos a usar esse SKU para este tutorial.

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Utilize o Azure CLI ou o Azure PowerShell para implantar o ficheiro Bicep.

Neste tutorial, vamos criar dois novos grupos de recursos. Um para o ambiente dev e outro para o ambiente de produção.

Para as variáveis do modelo e dos parâmetros, `{path-to-the-bicep-file}` substitua, , , , e os `{path-to-azuredeploy.parameters.dev.json}` `{path-to-azuredeploy.parameters.prod.json}` aparelhos encaracolados `{}` com os seus percursos de ficheiro Bicep e parâmetros.

Primeiro, vamos para o ambiente dev.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

Agora, vamos implantar-nos no ambiente de produção.

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
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando os grupos de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Vê os dois novos grupos de recursos que implementou neste tutorial.
1. Selecione qualquer um dos grupos de recursos e veja os recursos implantados. Note que correspondem aos valores especificados no seu ficheiro de parâmetros para esse ambiente.

## <a name="clean-up-resources"></a>Limpar os recursos

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilize um ficheiro de parâmetros para implementar o seu ficheiro Bicep. No próximo tutorial, aprenderás a modular os teus ficheiros Bicep.

> [!div class="nextstepaction"]
> [Adicionar módulos](./bicep-tutorial-add-modules.md)
