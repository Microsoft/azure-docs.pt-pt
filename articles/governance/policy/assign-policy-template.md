---
title: 'Quickstart: Nova atribuição de políticas com modelos'
description: Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma atribuição de política para identificar recursos não conformes.
ms.date: 03/31/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: b61198fecf0c3a6ba9aad19abc60bc042b68c29c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096619"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes usando um modelo ARM

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este quickstart passos através do processo de utilização de um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma atribuição de política para identificar máquinas virtuais que não estão a usar discos geridos. No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o modelo ARM para atribuir uma Política de Azure ao Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

Neste arranque rápido, cria-se uma atribuição de política e atribui-se uma definição de política incorporada chamada _Audit VMs que não utilizam discos geridos_. Para obter uma lista parcial das políticas incorporadas disponíveis, consulte [as amostras da Política Azure](./samples/index.md).

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

O recurso definido no modelo é:

- [Microsoft.Autorização/políticaAssins de assinaturas](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>Implementar o modelo

> [!NOTE]
> O serviço de política Azure é gratuito. Para mais informações, consulte [a Visão Geral da Política Azure.](./overview.md)

1. Selecione a imagem seguinte para iniciar sessão no portal do Azure e abra o modelo:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Botão para implantar o modelo ARM para atribuir uma Política de Azure ao Azure." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. Selecione ou introduza os seguintes valores:

   | Name | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | O grupo de recursos | **Selecione Criar novo,** especifique um nome e, em seguida, selecione **OK**. Na imagem, o nome do grupo de recursos é _mypolicyquickstart \<Date in MMDD\> rg_. |
   | Localização | selecione uma região. Por exemplo, **E.U.A. Central**. |
   | Nome de atribuição de políticas | Especifique um nome de atribuição de política. Pode utilizar o visor de definição de política, se quiser. Por exemplo, _Audit VMs que não utilizam discos geridos_. |
   | Nome Rg | Especifique um nome de grupo de recursos para onde pretende atribuir a apólice. Neste arranque rápido, utilize o valor predefinido **[grupo de recursos().nome]**. **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** é uma função de modelo que recupera o grupo de recursos. |
   | ID de definição de política | Especificar **/fornecedores/Microsoft.Autorização/políticaDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Concordo com os termos e condições acima indicados | (Selecione) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para encontrar mais modelos de amostras, consulte [o modelo Azure Quickstart.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)
- Para ver a referência do modelo, vá à [referência do modelo Azure](/azure/templates/microsoft.authorization/allversions).
- Para aprender a desenvolver modelos ARM, consulte a [documentação do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md).
- Para obter a implementação ao nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize os _VMs de auditoria que não utilizem_ a atribuição de política de discos geridos que criou.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Screenshot dos detalhes de conformidade na página De Conformidade de Política." border="false":::

Se houver recursos existentes que não estejam em conformidade com esta nova atribuição, eles aparecem sob **recursos não conformes.**

Para mais informações, consulte [como funciona a conformidade.](./how-to/get-compliance-data.md#how-compliance-works)

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a atribuição criada, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política _Auditar VMs que não utilizam discos geridos_ que criou.

1. Clique com o direito nos _VMs de auditoria que não utilizam_ a atribuição de política de discos geridos e selecione a atribuição de **Eliminar**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Screenshot de usar o menu de contexto para eliminar uma atribuição da página Compliance." border="false":::

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, atribuiu uma definição de política incorporada a um âmbito e avaliou o seu relatório de conformidade. A definição de política valida que todos os recursos no âmbito são compatíveis e identifica quais não são.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)