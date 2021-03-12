---
title: Tutorial - Modelo JSON de exportação do portal Azure para o desenvolvimento de Bicep
description: Aprenda a usar um modelo JSON exportado para completar o seu desenvolvimento Bicep.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632554"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Tutorial: Use o modelo JSON exportado a partir do portal Azure

Nesta série tutorial, criou um ficheiro Bicep para implantar uma conta de armazenamento Azure. Nos dois seguintes tutoriais, você adiciona um *plano de Serviço de Aplicações* e um *site*. Em vez de criar modelos de raiz, você aprende a exportar modelos JSON a partir do portal Azure e como usar modelos de amostra a partir dos [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/). Personaliza esses modelos para o seu uso. Este tutorial centra-se em modelos de exportação e personalização do resultado para o seu ficheiro Bicep. Leva cerca de **14 minutos** para ser completado.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que complete o [tutorial sobre saídas,](bicep-tutorial-add-outputs.md)mas não é necessário.

Você deve ter Código de Estúdio Visual com a extensão Bicep, e ou Azure PowerShell ou Azure CLI. Para mais informações, consulte [as ferramentas Bicep.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Rever ficheiro Bicep

No final do tutorial anterior, o seu ficheiro Bicep tinha os seguintes conteúdos:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Este ficheiro Bicep funciona bem para a implementação de contas de armazenamento, mas talvez queira adicionar mais recursos. Você pode exportar um modelo JSON a partir de um recurso existente para obter rapidamente o JSON para esse recurso. E, em seguida, converta o JSON em Bicep antes de o poder adicionar ao seu ficheiro Bicep.

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

    ![Portal do modelo de exportação do modelo do gestor de recursos](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Selecione **Rever e criar**.
1. Selecione **Criar**. Leva alguns momentos para criar o recurso.

## <a name="export-template"></a>Exportar modelo

Atualmente, o portal Azure apenas suporta a exportação de modelos JSON. Existem ferramentas que pode usar para descompiler modelos JSON para ficheiros Bicep.

1. Selecione **Ir para recurso**.

    ![Ir para recurso](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecione **o modelo de exportação.**

    ![Modelo de exportação de modelo de gestor de recursos](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   A característica do modelo de exportação toma o estado atual de um recurso e gera um modelo para implementá-lo. Exportar um modelo pode ser uma forma útil de obter rapidamente o JSON que você precisa para implementar um recurso.

1. A `Microsoft.Web/serverfarms` definição e a definição de parâmetro são as partes de que precisa.

    ![Modelo de exportação de modelo de gestor de recursos modelo exportado](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Tipicamente, o modelo exportado é mais verboso do que você poderia querer ao criar um modelo. Por exemplo, o objeto SKU no modelo exportado tem cinco propriedades. Este modelo funciona, mas você poderia apenas usar a `name` propriedade. Pode começar com o modelo exportado e, em seguida, modificá-lo como quiser para se adaptar às suas necessidades.

1. Selecione **Transferir**.  O ficheiro zip descarregado contém uma **template.js** e uma **parameters.jsligado**. Desaperte os ficheiros.
1. Navegue para **https://bicepdemo.z22.web.core.windows.net/** , **selecione Decompile**, e, em seguida, abrir **template.jsem**. Obtém-se o modelo em Bicep.

## <a name="revise-existing-bicep-file"></a>Rever o ficheiro Bicep existente

O modelo exportado descomplisado dá-lhe a maior parte do Bicep que precisa, mas precisa personalizá-lo para o seu ficheiro Bicep. Preste especial atenção às diferenças de parâmetros e variáveis entre o seu ficheiro Bicep e o ficheiro Bicep exportado. Obviamente, o processo de exportação não conhece os parâmetros e variáveis que já definiu no seu ficheiro Bicep.

O exemplo a seguir mostra as adições ao seu ficheiro Bicep. Contém o código exportado mais algumas alterações. Primeiro, muda o nome do parâmetro para corresponder à sua convenção de nomeação. Em segundo lugar, utiliza o seu parâmetro de localização para a localização do plano de serviço de aplicações. Em terceiro lugar, remove algumas das propriedades onde o valor padrão é bom.

Copie todo o ficheiro e substitua o seu ficheiro Bicep pelo seu conteúdo.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Implementar ficheiro Bicep

Utilize o Azure CLI ou o Azure PowerShell para implementar um ficheiro Bicep.

Se não criou o grupo de recursos, consulte [criar grupo de recursos](bicep-tutorial-create-first-bicep.md#create-resource-group). O exemplo pressupõe que definiu a `bicepFile` variável para o caminho para o ficheiro Bicep, como mostra o [primeiro tutorial](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar este cmdlet de implementação, você deve ter a [versão mais recente](/powershell/azure/install-az-ps) de Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar este comando de implementação, tem de ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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

Você aprendeu a exportar um modelo JSON a partir do portal Azure, como converter o modelo JSON em um ficheiro Bicep, e como usar o modelo exportado para o seu desenvolvimento Bicep. Também pode usar os modelos Azure Quickstart para simplificar o desenvolvimento do Bicep.

> [!div class="nextstepaction"]
> [Use modelos Azure Quickstart](bicep-tutorial-quickstart-template.md)
