---
title: Tutorial – adicionar marcas a recursos no modelo
description: Adicione marcas aos recursos que você implanta em seu modelo de Azure Resource Manager. As marcas permitem que você organize os recursos logicamente.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 078d85b3120baf5b8bea9c305fb1f97763f8df59
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406070"
---
# <a name="tutorial-add-tags-in-your-resource-manager-template"></a>Tutorial: adicionar marcas em seu modelo do Resource Manager

Neste tutorial, você aprenderá a adicionar marcas a recursos em seu modelo. As [marcas](resource-group-using-tags.md) ajudam a organizar seus recursos de forma lógica. Os valores de marcação aparecem em relatórios de custo. Este tutorial leva **8 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre modelos de início rápido](template-tutorial-quickstart-template.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Modelo de revisão

O modelo anterior implantou uma conta de armazenamento, um plano do serviço de aplicativo e um aplicativo Web.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json)]

Depois de implantar esses recursos, talvez seja necessário controlar os custos e localizar os recursos que pertencem a uma categoria. Você pode adicionar marcas para ajudar a resolver esses problemas.

## <a name="add-tags"></a>Adicionar etiquetas

Você marca os recursos para adicionar valores que ajudam a identificar seu uso. Por exemplo, você pode adicionar marcas que listam o ambiente e o projeto. Você pode adicionar marcas que identificam um centro de custo ou a equipe que possui o recurso. Adicione quaisquer valores que façam sentido para sua organização.

O exemplo a seguir realça as alterações no modelo. Copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json?range=1-118&highlight=46-52,64,78,100)]

## <a name="deploy-template"></a>Implementar o modelo

É hora de implantar o modelo e examinar os resultados.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando o grupo de recursos do portal do Azure.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Selecione o grupo de recursos no qual você implantou.
1. Selecione um dos recursos, como o recurso da conta de armazenamento. Você verá que agora ele tem marcas.

   ![Mostrar marcas](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você adicionou marcas aos recursos. No próximo tutorial, você aprenderá a usar arquivos de parâmetro para simplificar a passagem de valores para o modelo.

> [!div class="nextstepaction"]
> [Usar arquivo de parâmetro](template-tutorial-use-parameter-file.md)
