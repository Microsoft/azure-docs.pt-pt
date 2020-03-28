---
title: Tutorial - adicione tags aos recursos em modelo
description: Adicione tags aos recursos que implementa no seu modelo de Gestor de Recursos Azure. As etiquetas permitem-lhe organizar logicamente recursos.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 748a32d0ea8bfb0f23a99ce99d0aaf051118bc19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369886"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Tutorial: Adicione etiquetas no seu modelo ARM

Neste tutorial, aprende a adicionar tags aos recursos no seu modelo De gestor de recursos Azure (ARM). [As etiquetas](../management/tag-resources.md) ajudam-no logicamente a organizar os seus recursos. Os valores da etiqueta aparecem nos relatórios de custos. Este tutorial leva **8 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre modelos Quickstart,](template-tutorial-quickstart-template.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

O seu modelo anterior implementou uma conta de armazenamento, plano de serviço de aplicações e aplicação web.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Depois de implantar estes recursos, poderá ter de rastrear custos e encontrar recursos que pertençam a uma categoria. Pode adicionar tags para ajudar a resolver estes problemas.

## <a name="add-tags"></a>Adicionar etiquetas

Você marca recursos para adicionar valores que o ajudam a identificar o seu uso. Por exemplo, pode adicionar tags que listam o ambiente e o projeto. Pode adicionar etiquetas que identifiquem um centro de custos ou a equipa proprietária do recurso. Adicione quaisquer valores que façam sentido para a sua organização.

O exemplo seguinte realça as alterações ao modelo. Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Implementar o modelo

É hora de implementar o modelo e olhar para os resultados.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Selecione o grupo de recursos para o quais foi implantado.
1. Selecione um dos recursos, como o recurso da conta de armazenamento. Vê que agora tem etiquetas.

   ![Mostrar etiquetas](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou etiquetas aos recursos. No próximo tutorial, você aprenderá a usar ficheiros de parâmetros para simplificar a passagem de valores para o modelo.

> [!div class="nextstepaction"]
> [Utilize o ficheiro parâmetro](template-tutorial-use-parameter-file.md)
