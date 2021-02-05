---
title: 'Quickstart: Criar uma consulta partilhada com modelos'
description: Neste arranque rápido, utiliza-se um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma consulta partilhada de Gráfico de Recurso que conta com máquinas virtuais por SISTEMA.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 8d631ffcb14af93f10e578097470efc6156287d5
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594321"
---
# <a name="quickstart-create-a-shared-query-by-using-an-arm-template"></a>Quickstart: Crie uma consulta partilhada utilizando um modelo ARM

As consultas de gráfico de recurso podem ser guardadas como uma _consulta privada_ ou uma _consulta partilhada._ Uma consulta privada é guardada para o perfil do portal dos indivíduos e não é visível para os outros. Uma consulta partilhada é um objeto do Gestor de Recursos que pode ser partilhado com outros através de permissões e acesso baseado em funções. Uma consulta partilhada proporciona uma execução comum e consistente da descoberta de recursos. Este quickstart usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar uma consulta partilhada.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implemente o modelo ARM para criar uma consulta partilhada ao Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

Neste quickstart, você cria uma consulta partilhada chamada _Conde VMs by OS_. Para experimentar esta consulta em SDK ou em portal com o Resource Graph Explorer, consulte [Samples - Conte máquinas virtuais por tipo OS](./samples/starter.md#count-os).

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json":::

O recurso definido no modelo é:

- [Microsoft.ResourceGraph/consultas](/azure/templates/microsoft.resourcegraph/queries)

## <a name="deploy-the-template"></a>Implementar o modelo

> [!NOTE]
> O serviço Azure Resource Graph é gratuito. Para mais informações, consulte [a visão geral do Gráfico de Recursos Azure.](./overview.md)

1. Selecione a imagem seguinte para iniciar sessão no portal do Azure e abra o modelo:

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Implemente o modelo ARM para criar uma consulta partilhada ao Azure" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json":::

1. Selecione ou introduza os seguintes valores:

   | Name | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | O grupo de recursos | **Selecione Criar novo,** especifique um nome e, em seguida, selecione **OK**. |
   | Localização | selecione uma região. Por exemplo, **E.U.A. Central**. |
   | Nome de consulta | Deixe o valor predefinido: **Conde VMs por OS**. |
   | Código de consulta | Deixe o valor predefinido: `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Descrição de consultas | Deixe o valor predefinido: **Esta consulta partilhada conta todos os recursos da máquina virtual e resume pelo tipo de SO.** |
   | Concordo com os termos e condições acima indicados | (Selecione) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para encontrar mais modelos de amostras, consulte [o modelo Azure Quickstart.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)
- Para ver a referência do modelo, vá à [referência do modelo Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Para aprender a desenvolver modelos ARM, consulte a [documentação do Gestor de Recursos Azure](../../azure-resource-manager/management/overview.md).
- Para obter a implementação ao nível de subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Para executar a nova consulta partilhada, siga estes passos:

1. A partir da barra de pesquisa do portal, procure consultas de **Gráfico de Recurso** e selecione-a.

1. Selecione a consulta partilhada denominada **Count VMs por OS** e, em seguida, selecione o **separador Resultados** na página **'Vista Geral'.**

A consulta partilhada também pode ser aberta a partir do Explorador de Gráficos de Recursos:

1. A partir da barra de pesquisa do portal, procure **o Explorador de Gráficos de Recursos** e selecione-o.

1. Selecione o botão **de consulta Abrir.**

1. Alterar **tipo** para _consultas partilhadas_. Se não vir os **VMs do Conde por OS** na lista, utilize a caixa de filtro para limitar os resultados. Uma vez visíveis os **VMs do Conde por OS,** selecione o seu nome.

1. Uma vez carregada a consulta, selecione o botão **de consulta 'Executar'.** Os resultados são apresentados no separador **Resultados.**

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a consulta partilhada criada, siga estes passos:

1. A partir da barra de pesquisa do portal, procure consultas de **Gráfico de Recurso** e selecione-a.

1. Coloque a caixa de verificação ao lado da consulta partilhada denominada **Count VMs por OS**.

1. Selecione o botão **Eliminar** ao longo da parte superior da página.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma consulta partilhada de Gráfico de Recurso.

Para saber mais sobre consultas partilhadas, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Gerir consultas no portal Azure](./tutorials/create-share-query.md)