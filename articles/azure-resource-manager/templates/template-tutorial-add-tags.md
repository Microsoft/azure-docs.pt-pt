---
title: Tutorial - adicione tags aos recursos no modelo
description: Adicione tags aos recursos que implementa no seu modelo de Gestor de Recursos Azure (modelo ARM). As etiquetas permitem organizar logicamente recursos.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 625a88c0ee946b1ca67737d9cc67b638699d12f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107010"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Tutorial: Adicione etiquetas no seu modelo ARM

Neste tutorial, você aprende a adicionar tags aos recursos no seu modelo de Gestor de Recursos Azure (modelo ARM). [As etiquetas](../management/tag-resources.md) ajudam-no a organizar logicamente os seus recursos. Os valores da etiqueta aparecem nos relatórios de custos. Este tutorial leva **8 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre modelos Quickstart,](template-tutorial-quickstart-template.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Modelo de revisão

O seu modelo anterior implementou uma conta de armazenamento, plano de Serviço de Aplicações e aplicação web.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

Depois de implementar estes recursos, poderá ter de rastrear os custos e encontrar recursos que pertençam a uma categoria. Pode adicionar tags para ajudar a resolver estes problemas.

## <a name="add-tags"></a>Adicionar etiquetas

Coloca etiquetas nos recursos para adicionar valores que o ajudam a identificar a sua utilização. Por exemplo, pode adicionar tags que listam o ambiente e o projeto. Pode adicionar tags que identifiquem um centro de custos ou a equipa proprietária do recurso. Adicione os valores que façam sentido para a sua organização.

O exemplo a seguir destaca as alterações ao modelo. Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Implementar o modelo

É hora de implementar o modelo e olhar para os resultados.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a `templateFile` variável para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. Selecione um dos recursos, como o recurso da conta de armazenamento. Vê que agora tem etiquetas.

   ![Mostrar tags](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, adicionou etiquetas aos recursos. No próximo tutorial, aprenderá a usar ficheiros de parâmetros para simplificar a passagem de valores ao modelo.

> [!div class="nextstepaction"]
> [Use o arquivo de parâmetros](template-tutorial-use-parameter-file.md)
