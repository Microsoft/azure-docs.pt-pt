---
title: Tutorial - Modelo de exportação do portal Azure
description: Aprenda a usar um modelo exportado para completar o desenvolvimento do seu modelo.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f95efbaedc2718c968062c47427ab7765756bde7
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408556"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Tutorial: Use modelo exportado do portal Azure

Nesta série tutorial, criou um modelo para implementar uma conta de armazenamento Azure. Nos dois tutoriais seguintes, você adiciona um plano de *Serviço de Aplicações* e um *site*. Em vez de criar modelos do zero, aprende-se a exportar modelos do portal Azure e a utilizar modelos de amostra a partir dos [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/). Personaliza esses modelos para o seu uso. Este tutorial centra-se em modelos de exportação e personalizao o resultado para o seu modelo. Leva cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre saídas,](template-tutorial-add-outputs.md)mas não é necessário.

Tem de ter o Código do Estúdio Visual com a extensão ferramentas do Gestor de Recursos e o Azure PowerShell ou o Azure CLI. Para mais informações, consulte [as ferramentas](template-tutorial-create-first-template.md#get-tools)do modelo.

## <a name="review-template"></a>Modelo de revisão

No final do tutorial anterior, o seu modelo tinha o seguinte JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

Este modelo funciona bem para a implementação de contas de armazenamento, mas é melhor adicionar mais recursos a ele. Você pode exportar um modelo a partir de um recurso existente para obter rapidamente o JSON para esse recurso.

## <a name="create-app-service-plan"></a>Criar plano do App Service

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso**.
1. Em **Search the Marketplace,** insira o plano de serviço de **aplicações**e, em seguida, selecione o plano de serviço de **aplicações.**  Não selecione o plano de serviço de **aplicações (clássico)**
1. Selecione **Criar**.
1. Introduza:

    - **Subscrição**: selecione a sua subscrição Azure.
    - **Grupo de Recursos**: Selecione **Criar novo** e, em seguida, especificar um nome. Forneça um nome de grupo de recursos diferente daquele que tem usado nesta série tutorial.
    - **Nome**: insira um nome para o plano de serviço da App.
    - **Sistema operativo**: selecione **Linux**.
    - **Região**: selecione uma localização Azure. Por exemplo, **Centro dos EUA.**
    - **Nível de preços:** para poupar custos, mude o SKU para **Basic B1** (em Dev/Teste).

    ![Portal de modelo de modelo de gestor de recursos](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecione **Rever e criar**.
1. Selecione **Criar**. Leva alguns momentos para criar o recurso.

## <a name="export-template"></a>Exportar modelo

1. Selecione **Ir para o recurso**.

    ![Ir para recurso](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecione **modelo de exportação**.

    ![Modelo de exportação de modelo de gestor de recursos](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   A função de modelo de exportação toma o estado atual de um recurso e gera um modelo para implantá-lo. Exportar um modelo pode ser uma forma útil de obter rapidamente o JSON que precisa para implementar um recurso.

1. Copie a definição **Microsoft.Web/serverfarms** e a definição de parâmetro para o seu modelo.

    ![Modelo de exportação de modelo de gestor de recursos](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Tipicamente, o modelo exportado é mais verboso do que você poderia querer ao criar um modelo. Por exemplo, o objeto SKU no modelo exportado tem cinco propriedades. Este modelo funciona, mas você poderia apenas usar a propriedade do **nome.** Pode começar com o modelo exportado e depois modificá-lo como quiser para se adaptar aos seus requisitos.

## <a name="revise-existing-template"></a>Rever o modelo existente

O modelo exportado dá-lhe a maior parte do JSON de que necessita, mas precisa personalizá-lo para o seu modelo. Preste especial atenção às diferenças de parâmetros e variáveis entre o seu modelo e o modelo exportado. Obviamente, o processo de exportação não conhece os parâmetros e variáveis que já definiu no seu modelo.

O exemplo que se segue realça as adições ao seu modelo. Contém o código exportado mais algumas alterações. Primeiro, muda o nome do parâmetro para combinar com a sua convenção de nomeação. Em segundo lugar, utiliza o seu parâmetro de localização para a localização do plano de serviço de aplicações. Em terceiro lugar, remove o **nome** dentro do objeto de **propriedades** porque este valor é redundante com a propriedade do **nome** ao nível do recurso.

Copie todo o ficheiro e substitua o seu modelo pelo seu conteúdo.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json" range="1-77" highlight="28-31,50-69":::

## <a name="deploy-template"></a>Implementar o modelo

Utilize o Azure CLI ou o Azure PowerShell para implementar um modelo.

Se ainda não criou o grupo de recursos, consulte o [grupo de recursos Create](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que definiu a variável **modeloFile** para o caminho para o ficheiro do modelo, como mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

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

Para executar este comando de implantação, deve ter a [versão mais recente](/cli/azure/install-azure-cli) do Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Se a implantação falhar, utilize o interruptor **de depuração** com o comando de implantação para mostrar os registos de depuração.  Também pode utilizar o interruptor **verboso** para mostrar os registos completos de depuração.

## <a name="verify-deployment"></a>Verificar a implementação

Pode verificar a implantação explorando o grupo de recursos a partir do portal Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. A partir do menu esquerdo, selecione **Grupos de Recursos**.
1. Selecione o grupo de recursos para o quais foi implantado.
1. O grupo de recursos contém uma conta de armazenamento e um plano de Serviço de Aplicações.

## <a name="clean-up-resources"></a>Limpar recursos

Se está saindo para o próximo tutorial, não precisa de apagar o grupo de recursos.

Se estás a parar agora, talvez queiras limpar os recursos que implantaste ao apagar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

## <a name="next-steps"></a>Passos seguintes

Aprendeu a exportar um modelo do portal Azure e como usar o modelo exportado para o desenvolvimento do seu modelo. Também pode usar os modelos Azure Quickstart para simplificar o desenvolvimento do modelo.

> [!div class="nextstepaction"]
> [Use modelos Azure Quickstart](template-tutorial-quickstart-template.md)
