---
title: Tutorial - Modelo de exportação do portal Azure
description: Aprenda a usar um modelo exportado para completar o desenvolvimento do seu modelo.
author: mumian
ms.date: 09/09/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ba1797da5a78eeebd25f5df1b6e37eb92470f584
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106925"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Tutorial: Use modelo exportado a partir do portal Azure

Nesta série tutorial, criou um modelo para implantar uma conta de armazenamento Azure. Nos dois seguintes tutoriais, você adiciona um *plano de Serviço de Aplicações* e um *site*. Em vez de criar modelos de raiz, você aprende a exportar modelos a partir do portal Azure e como usar modelos de amostra a partir dos [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/). Personaliza esses modelos para o seu uso. Este tutorial foca-se em modelos de exportação e personalização do resultado para o seu modelo. Leva cerca de **14 minutos** para ser completado.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre saídas,](template-tutorial-add-outputs.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão de Ferramentas gestor de recursos, e ou Azure PowerShell ou Azure CLI. Para obter mais informações, consulte [as ferramentas do modelo.](template-tutorial-create-first-template.md#get-tools)

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Este modelo funciona bem para implementar contas de armazenamento, mas é melhor adicionar mais recursos. Você pode exportar um modelo a partir de um recurso existente para obter rapidamente o JSON para esse recurso.

## <a name="create-app-service-plan"></a>Criar plano do App Service

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso**.
1. Em **Search the Marketplace**, insira o plano de Serviço de **Aplicações** e, em seguida, selecione **o plano de Serviço de Aplicações**.  Não selecione **o plano de Serviço de Aplicações (clássico)**
1. Selecione **Criar**.
1. Introduza:

    - **Subscrição**: selecione a sua subscrição Azure.
    - **Grupo de Recursos**: Selecione **Criar novo** e, em seguida, especificar um nome. Forneça um nome de grupo de recursos diferente daquele que tem usado nesta série tutorial.
    - **Nome**: insira um nome para o plano de serviço da App.
    - **Sistema operativo**: selecione **Linux**.
    - **Região**: selecione uma localização Azure. Por exemplo, **E.U.A. Central**.
    - **Nível de preços**: para poupar custos, altere o SKU para **Basic B1** (em Dev/Test).

    ![Portal do modelo de exportação do modelo do gestor de recursos](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecione **Rever e criar**.
1. Selecione **Criar**. Leva alguns momentos para criar o recurso.

## <a name="export-template"></a>Exportar modelo

1. Selecione **Ir para recurso**.

    ![Ir para recurso](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecione **o modelo de exportação.**

    ![Modelo de exportação de modelo de gestor de recursos](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   A característica do modelo de exportação toma o estado atual de um recurso e gera um modelo para implementá-lo. Exportar um modelo pode ser uma forma útil de obter rapidamente o JSON que você precisa para implementar um recurso.

1. Veja a `Microsoft.Web/serverfarms` definição e a definição de parâmetro no modelo exportado. Não precisas de copiar estas secções. Você pode apenas usar este modelo exportado como um exemplo de como você quer adicionar este recurso ao seu modelo.

    ![Modelo de exportação de modelo de gestor de recursos modelo exportado](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Tipicamente, o modelo exportado é mais verboso do que você poderia querer ao criar um modelo. Por exemplo, o objeto SKU no modelo exportado tem cinco propriedades. Este modelo funciona, mas você poderia apenas usar a `name` propriedade. Pode começar com o modelo exportado e, em seguida, modificá-lo como quiser para se adaptar às suas necessidades.

## <a name="revise-existing-template"></a>Rever o modelo existente

O modelo exportado dá-lhe a maior parte do JSON que você precisa, mas você precisa personalizá-lo para o seu modelo. Preste especial atenção às diferenças de parâmetros e variáveis entre o seu modelo e o modelo exportado. Obviamente, o processo de exportação não conhece os parâmetros e variáveis que já definiu no seu modelo.

O exemplo a seguir destaca as adições ao seu modelo. Contém o código exportado mais algumas alterações. Primeiro, muda o nome do parâmetro para corresponder à sua convenção de nomeação. Em segundo lugar, utiliza o seu parâmetro de localização para a localização do plano de serviço de aplicações. Em terceiro lugar, remove algumas das propriedades onde o valor padrão é bom.

Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Implementar o modelo

Utilize o Azure CLI ou o Azure PowerShell para implementar um modelo.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a `templateFile` variável para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Se a implementação falhar, utilize o `verbose` interruptor para obter informações sobre os recursos que estão a ser criados. Use o `debug` interruptor para obter mais informações para depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implementação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos.**
1. Selecione o grupo de recursos para o quais foi implantado.
1. O grupo de recursos contém uma conta de armazenamento e um plano de Serviço de Aplicações.

## <a name="clean-up-resources"></a>Limpar os recursos

Se está a avançar para o próximo tutorial, não precisa de eliminar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que mobilizaste, eliminando o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a exportar um modelo a partir do portal Azure, e como usar o modelo exportado para o desenvolvimento do seu modelo. Também pode usar os modelos Azure Quickstart para simplificar o desenvolvimento do modelo.

> [!div class="nextstepaction"]
> [Use modelos Azure Quickstart](template-tutorial-quickstart-template.md)
