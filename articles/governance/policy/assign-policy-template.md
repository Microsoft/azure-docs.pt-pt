---
title: 'Quickstart: Nova atribuição de políticas com modelos'
description: Neste arranque rápido, você usa um modelo de Gestor de Recursos para criar uma atribuição de política para identificar recursos não conformes.
ms.date: 03/16/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 646e2e064618e70fd9e9e747926f0886d85fde26
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82187788"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Quickstart: Criar uma atribuição de políticas para identificar recursos não conformes utilizando um modelo de Gestor de Recursos

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos. No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste arranque rápido, cria-se uma atribuição de políticas e atribui-se uma definição de política incorporada chamada _VMs_de auditoria que não utilizam discos geridos . Para obter uma lista parcial das políticas disponíveis em butinsas, consulte [as amostras da Política Azure.](./samples/index.md)

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json" range="1-36" highlight="26-34":::

O recurso definido no modelo é:

- [Microsoft.Autorizações/políticas](/azure/templates/microsoft.authorization/policyassignments)

### <a name="deploy-the-template"></a>Implementar o modelo

> [!NOTE]
> O serviço De Política Azure é gratuito. Para mais informações, consulte [a visão geral da Política do Azure.](./overview.md)

1. Selecione a imagem seguinte para iniciar sessão no portal do Azure e abra o modelo:

   [![Implementar o modelo de política para Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores:

   | Nome | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | Grupo de recursos | Selecione **Criar novo,** especificar um nome e, em seguida, selecione **OK**. Na imagem, o nome do grupo de recursos é _mypolicyquickstart\<Date in\>MMDD rg_. |
   | Localização | selecione uma região. Por exemplo, **Centro dos EUA.** |
   | Nome de atribuição de políticas | Especifique um nome de atribuição de apólices. Pode utilizar o visor de definição de política, se quiser. Por exemplo, **VMs de auditoria que não utilizam discos geridos**. |
   | Nome Rg | Especifique um nome de grupo de recursos para onde pretende atribuir a apólice. Neste arranque rápido, utilize o valor predefinido [nome do Grupo de **recursos].** **[recursosGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** é uma função de modelo que recupera o grupo de recursos. |
   | ID de Definição de Política | Especificar **/fornecedores/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Concordo com os termos e condições acima referidos | (Selecione) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para encontrar mais modelos de amostras, consulte o [modelo Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência do modelo, vá à referência do [modelo Azure](/azure/templates/microsoft.authorization/allversions).
- Para aprender a desenvolver modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos do Azure.](../../azure-resource-manager/management/overview.md)
- Para aprender a implantação ao nível da subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Selecione **Compliance** no lado esquerdo da página. Em seguida, localize os **VMs** de auditoria que não utilizam a atribuição de políticas de discos geridos que criou.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Página geral de conformidade de política" border="false":::

Se existem recursos existentes que não estejam em conformidade com esta nova atribuição, eles aparecem sob **recursos não conformes.**

Para mais informações, consulte como funciona a [conformidade.](./how-to/get-compliance-data.md#how-compliance-works)

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição criada, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Clique direito nos **VMs de auditoria que não utilizam** a atribuição da política de discos geridos e selecione eliminar a **atribuição**.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Eliminar uma atribuição da página de síntese de conformidade" border="false":::

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, atribuiu uma definição de política incorporada a um âmbito e avaliou o seu relatório de conformidade. A definição de política valida que todos os recursos no âmbito são compatíveis e identifica quais não são.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)