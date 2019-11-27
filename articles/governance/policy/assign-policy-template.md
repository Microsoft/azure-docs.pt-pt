---
title: 'Início rápido: nova atribuição de política com modelos'
description: Neste guia de início rápido, você usa um modelo do Resource Manager para criar uma atribuição de política para identificar recursos sem conformidade.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 61bffcdeb5d562fe18df98fda091d5d6aa4b4051
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482339"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Início rápido: criar uma atribuição de política para identificar recursos sem conformidade usando um modelo do Resource Manager

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, você cria uma atribuição de política e atribui uma definição de política interna chamada _VMs de auditoria que não usam discos gerenciados_. Para obter uma lista parcial de políticas internas disponíveis, consulte [exemplos de Azure Policy](./samples/index.md).

Há vários métodos para criar atribuições de política. Neste guia de início rápido, você usa um [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Aqui está uma cópia do modelo:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy serviço é gratuito. Para obter mais informações, consulte [visão geral do Azure Policy](./overview.md).

1. Selecione a imagem a seguir para entrar no portal do Azure e abrir o modelo:

   [![implantar o modelo de política no Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecione ou insira os seguintes valores:

   | Nome | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | Grupo de recursos | Selecione **criar novo**, especifique um nome e, em seguida, selecione **OK**. Na captura de tela, o nome do grupo de recursos é _mypolicyquickstart\<data em MMDD\>RG_. |
   | Localização | Selecione uma região. Por exemplo, **EUA Central**. |
   | Nome da atribuição de política | Especifique um nome de atribuição de política. Você pode usar a exibição definição de política se desejar. Por exemplo, **audite VMs que não usam discos gerenciados**. |
   | Nome RG | Especifique um nome de grupo de recursos ao qual você deseja atribuir a política. Neste guia de início rápido, use o valor padrão **[resourcegroup (). Name]** . **[resourcegroup ()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** é uma função de modelo que recupera o grupo de recursos. |
   | ID de definição de política | Especifique **/Providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4C19-B460-a2d36003525a**. |
   | Eu concordo com os termos e condições declarados acima | Não |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para encontrar mais modelos de exemplos, consulte [modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, vá para [referência de modelo do Azure](/azure/templates/microsoft.authorization/allversions).
- Para saber como desenvolver modelos do Resource Manager, consulte a [documentação do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
- Para aprender a implantação em nível de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identificar recursos não compatíveis

Selecione **conformidade** no lado esquerdo da página. Em seguida, localize as **VMs de auditoria que não usam a atribuição de política de discos gerenciados** que você criou.

![Página Visão geral de conformidade de política](./media/assign-policy-template/policy-compliance.png)

Se houver algum recurso existente que não esteja em conformidade com essa nova atribuição, eles aparecerão em **recursos sem conformidade**.

Para obter mais informações, consulte [como funciona a conformidade](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição de criado, siga estes passos:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página Azure Policy e localize a atribuição de política **Auditar VMs que não utilizam discos geridos** que criou.

1. Clique com o botão direito do mouse na atribuição **auditar VMs que não usam a política de discos gerenciados** e selecione **excluir atribuição**.

   ![Excluir uma atribuição da página Visão geral de conformidade](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você atribuiu uma definição de política interna a um escopo e avaliou seu relatório de conformidade. A definição de política valida que todos os recursos no âmbito estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, avance para o tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)