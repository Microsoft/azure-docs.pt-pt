---
title: Tutorial – exportar modelo de Azure Resource Manager da portal do Azure
description: Saiba como usar um modelo exportado para concluir o desenvolvimento do modelo.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0564763b05dd44eb908ec00f98a97e9d4970099f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177587"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Tutorial: Usar modelo exportado do portal do Azure

Nesta série de tutoriais, você criou um modelo para implantar uma conta de armazenamento do Azure. Nos próximos dois tutoriais, você adiciona um *plano do serviço de aplicativo* e um *site*. Em vez de criar modelos do zero, você aprende a exportar modelos do portal do Azure e a usar modelos de exemplo dos modelos de [início rápido do Azure](/resources/templates/). Você personaliza esses modelos para seu uso. Este tutorial se concentra na exportação de modelos e na personalização do resultado do modelo. Demora cerca de **14 minutos** para ser concluída.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre saídas](template-tutorial-add-outputs.md), mas isso não é necessário.

Você deve ter Visual Studio Code com a extensão de ferramentas do Resource Manager e Azure PowerShell ou CLI do Azure. Para obter mais informações, consulte [ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examine seu modelo

No final do tutorial anterior, seu modelo tinha o seguinte JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Esse modelo funciona bem para a implantação de contas de armazenamento, mas talvez você queira adicionar mais recursos a ela. Você pode exportar um modelo de um recurso existente para obter rapidamente o JSON para esse recurso.

## <a name="create-app-service-plan"></a>Criar plano do App Service

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **criar um recurso**.
1. Em **Pesquisar no Marketplace**, insira **plano do serviço de aplicativo**e selecione **plano do serviço de aplicativo**.  Não selecionar **plano do serviço de aplicativo (clássico)**
1. Selecione **Criar**.
1. Digita

    - **Subscrição**: selecione a sua subscrição do Azure.
    - **Grupo de recursos**: Selecione **criar novo** e especifique um nome. Forneça um nome de grupo de recursos diferente daquele que você está usando nesta série de tutoriais.
    - **Nome**: Insira um nome para o plano do serviço de aplicativo.
    - **Sistema operacional**: selecione **Linux**.
    - **Região**: selecione um local do Azure. Por exemplo, **E.U.A. Central**.
    - **Tipo de preço**: para economizar custos, altere a SKU para **básico B1** (em desenvolvimento/teste).

    ![Portal do modelo de exportação de modelos do Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecione **revisar e criar**.
1. Selecione **Criar**. Leva alguns minutos para criar o recurso.

## <a name="export-the-template"></a>Exportar o modelo

1. Selecione **ir para o recurso**.

    ![Ir para recurso](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecione **Exportar modelo**.

    ![Modelo de exportação de modelo do Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   O recurso exportar modelo usa o estado atual de um recurso e gera um modelo para implantá-lo. Exportar um modelo pode ser uma maneira útil de obter rapidamente o JSON de que você precisa para implantar um recurso.

1. Copie a definição **Microsoft. Web/serverfarms** e a definição de parâmetro para seu modelo.

    ![Modelo exportado do modelo de exportação do modelo do Resource Manager](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Normalmente, o modelo exportado é mais detalhado do que você pode desejar ao criar um modelo. Por exemplo, o objeto SKU no modelo exportado tem cinco Propriedades. Esse modelo funciona, mas você poderia simplesmente usar a propriedade **Name** . Você pode começar com o modelo exportado e, em seguida, modificá-lo como desejar para atender às suas necessidades.

## <a name="revise-the-existing-template"></a>Revisar o modelo existente

O modelo exportado fornece a você a maior parte do JSON que você precisa, mas você precisa personalizá-lo para seu modelo. Preste atenção especial às diferenças em parâmetros e variáveis entre o modelo e o modelo exportado. Obviamente, o processo de exportação não conhece os parâmetros e as variáveis que você já definiu em seu modelo.

O exemplo a seguir realça as adições ao seu modelo. Ele contém o código exportado, além de algumas alterações. Primeiro, ele altera o nome do parâmetro para corresponder à sua Convenção de nomenclatura. Em segundo lugar, ele usa o parâmetro Location para o local do plano do serviço de aplicativo. Em terceiro lugar, ele remove o **nome** dentro do objeto de **Propriedades** porque esse valor é redundante com a propriedade **Name** no nível de recurso.

Copie o arquivo inteiro e substitua o modelo pelo seu conteúdo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-the-template"></a>Implementar o modelo

Use CLI do Azure ou Azure PowerShell para implantar um modelo.

Se você não tiver criado o grupo de recursos, consulte [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo supõe que você definiu a variável **TemplateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Verificar a implementação

Você pode verificar a implantação explorando o grupo de recursos do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **grupos de recursos**.
1. Selecione o grupo de recursos no qual você implantou.
1. O grupo de recursos contém uma conta de armazenamento e um plano do serviço de aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu como exportar um modelo do portal do Azure e como usar o modelo exportado para o desenvolvimento do modelo. Você também pode usar os modelos de início rápido do Azure para simplificar o desenvolvimento de modelos.

> [!div class="nextstepaction"]
> [Usar modelos de início rápido do Azure](template-tutorial-quickstart-template.md)
