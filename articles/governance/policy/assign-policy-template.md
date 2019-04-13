---
title: Criar uma atribuição de política com um modelo do Resource Manager
description: Este artigo orienta-o pelos passos para utilizar um modelo do Resource Manager para criar uma atribuição de política para identificar recursos incompatíveis.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1a811d3d0796b37053ffbabf56b63b4e4f68e203
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524077"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Criar uma atribuição de política para identificar recursos incompatíveis com um modelo do Resource Manager

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em *não conformidade* com a atribuição de política.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, criar uma atribuição de política e atribuir uma definição de política incorporada chamada *VMs de auditoria que não utilizam discos geridos*. Para obter uma lista parcial de políticas incorporadas disponíveis, consulte [exemplos de política](./samples/index.md).

Existem vários métodos para a criação de atribuições de política. Neste início rápido, vai utilizar um [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Esta é uma cópia do modelo:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Serviço de política do Azure é gratuito.  Para obter mais informações, consulte [descrição geral do Azure Policy](./overview.md).

1. Selecione a imagem seguinte para iniciar sessão no portal do Azure e abrir o modelo:

   [![Implementar o modelo de política para o Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores:

   | Name | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | Grupo de recursos | Selecione **criar novo**, especifique um nome e, em seguida, selecione **OK**. Na captura de ecrã, é o nome do grupo de recursos *mypolicyquickstart\<data no MMDD > rg*. |
   | Localização | Selecione uma região. Por exemplo, **E.U.A. Central**. |
   | Nome da atribuição de política | Especifique um nome de atribuição de política. Se desejar, pode usar a exibição de definição de política. Por exemplo, **VMs de auditoria que não utilizam discos geridos**. |
   | Nome de RG | Especifique um nome de grupo de recursos em que pretende atribuir a política. Neste início rápido, utilize o valor predefinido **[resourceGroup () name]**. **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)**  é uma função de modelo que obtém o grupo de recursos. |
   | ID de definição de política | Especifique **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Aceito os termos e condições acima apresentados | (Selecionar) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para obter mais modelos de exemplos, veja [modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, aceda à [referência de modelo do Azure](/azure/templates/microsoft.authorization/allversions).
- Para saber como desenvolver modelos do Resource Manager, veja [documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para obter a implementação de nível de assinatura, consulte [criar grupos de recursos e recursos ao nível da subscrição](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Selecione **conformidade** no lado esquerdo da página. Em seguida, localize a **VMs de auditoria que não utilizam discos geridos** atribuição de política que criou.

![Página de descrição geral de conformidade de política](./media/assign-policy-template/policy-compliance.png)

Se existirem quaisquer recursos existentes que não estão em conformidade com esta nova atribuição, aparecem em **recursos não compatíveis**.

Para obter mais informações, consulte [como funciona a conformidade](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição de criado, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Com o botão direito a **VMs de auditoria que não utilizam discos geridos** atribuição de política e selecione **eliminar atribuição**.

   ![Eliminar uma atribuição a partir da página de descrição geral de conformidade](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, atribuiu uma definição de política incorporada a um âmbito e avaliados o respetivo relatório de conformidade. A definição de política valida que todos os recursos no âmbito estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)