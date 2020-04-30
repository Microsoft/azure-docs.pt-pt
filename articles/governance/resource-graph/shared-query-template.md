---
title: 'Quickstart: Criar uma consulta partilhada com modelos'
description: Neste arranque rápido, você usa um modelo de Gestor de Recursos para criar uma consulta partilhada de Resource Graph que conta máquinas virtuais por OS.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234333"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Quickstart: Criar uma consulta partilhada usando um modelo de Gestor de Recursos

As consultas de gráfico de recursos podem ser guardadas como uma _consulta privada_ ou uma _consulta partilhada_. Uma consulta privada é guardada para o perfil do portal individual e não é visível para os outros. Uma consulta partilhada é um objeto de Gestor de Recursos que pode ser partilhado com outros através de permissões e acesso baseado em papéis. Uma consulta partilhada proporciona uma execução comum e consistente da descoberta de recursos. Este quickstart usa um modelo de Gestor de Recursos para criar uma consulta partilhada.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-shared-query"></a>Criar uma consulta partilhada

Neste arranque rápido, cria-se uma consulta partilhada chamada _Count VMs by OS_. Para experimentar esta consulta em SDK ou no portal com o Resource Graph Explorer, consulte [Amostras - Conte máquinas virtuais por tipo OS](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

O recurso definido no modelo é:

- [Microsoft.ResourceGraph/consultas](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Implementar o modelo

> [!NOTE]
> O serviço Azure Resource Graph é gratuito. Para mais informações, consulte a [visão geral do Gráfico de Recursos Azure](./overview.md).

1. Selecione a imagem seguinte para iniciar sessão no portal do Azure e abra o modelo:

   [![Implementar o modelo de política para Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores:

   | Nome | Valor |
   |------|-------|
   | Subscrição | Selecione a sua subscrição do Azure. |
   | Grupo de recursos | Selecione **Criar novo,** especificar um nome e, em seguida, selecione **OK**. |
   | Localização | selecione uma região. Por exemplo, **Centro dos EUA.** |
   | Nome de consulta | Deixe o valor predefinido **Count VMs por OS**. |
   | Código de consulta | Deixe o valor padrão`Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Descrição da consulta | Deixe o valor padrão **Esta consulta partilhada conta todos os recursos e resumos** da máquina virtual pelo tipo S. |
   | Concordo com os termos e condições acima referidos | (Selecione) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- Para encontrar mais modelos de amostras, consulte o [modelo Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência do modelo, vá à referência do [modelo Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Para aprender a desenvolver modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos do Azure.](../../azure-resource-manager/management/overview.md)
- Para aprender a implantação ao nível da subscrição, consulte [Criar grupos de recursos e recursos ao nível da subscrição](../../azure-resource-manager/templates/deploy-to-subscription.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Para executar a nova consulta partilhada, siga estes passos:

1. A partir da barra de pesquisa do portal, procure consultas de Gráfico de **Recursos** e selecione-as.

1. Selecione a consulta partilhada denominada **Count VMs por OS**e, em seguida, selecione o separador **Resultados** na página **'Visão Geral'.**

Alternativamente, a consulta partilhada pode ser aberta do Resource Graph Explorer:

1. A partir da barra de pesquisa do portal, procure o **Resource Graph Explorer** e selecione-o.

1. Selecione o botão **de consulta Aberto.**

1. Alterar **tipo** para _consultas partilhadas_. Se não vir os **VMs conde spor OS** na lista, utilize a caixa de filtro para limitar os resultados. Uma vez visível o **Conde VMs por consulta** partilhada do OS, selecione o seu nome.

1. Uma vez que a consulta esteja carregada, selecione o botão **de consulta Executar.** Os resultados são apresentados no separador **Resultados** abaixo.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a consulta partilhada criada, siga estes passos:

1. A partir da barra de pesquisa do portal, procure consultas de Gráfico de **Recursos** e selecione-as.

1. Desloque a caixa de verificação ao lado da consulta partilhada denominada **Count VMs by OS**.

1. Selecione o botão **Eliminar** ao longo da parte superior da página.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma consulta partilhada do Resource Graph.

Para saber mais sobre consultas partilhadas, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Gerir consultas no portal Azure](./tutorials/create-share-query.md)