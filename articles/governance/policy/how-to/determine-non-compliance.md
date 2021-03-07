---
title: Determinar as causas da não conformidade
description: Quando um recurso não é conforme, existem muitas razões possíveis. Aprenda a descobrir o que causou o incumprimento.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: a8168bf22aceaf5cbdec4b1346801aa62b7aa4ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439838"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas da não conformidade

Quando um recurso Azure é determinado a não cumprir uma regra política, é útil entender com que parte da regra o recurso não está em conformidade. Também é útil entender que mudança alterou um recurso previamente conforme para torná-lo incompatível. Há duas maneiras de encontrar esta informação:

- [Detalhes de conformidade](#compliance-details)
- [Alterar história (Pré-visualização)](#change-history)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não está em conformidade, os detalhes de conformidade desse recurso estão disponíveis na página de conformidade da **Política.** O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes de recursos como nome, tipo, localização e ID de recursos
- Estado de conformidade e timetamp da última avaliação para a atual atribuição de políticas
- Uma lista de _razões_ para o incumprimento dos recursos

> [!IMPORTANT]
> Como os detalhes _de_ conformidade de um recurso não conforme mostram o valor atual das propriedades nesse recurso, o utilizador deve ter **lido** a operação para o **tipo** de recurso. Por exemplo, se o recurso _não conforme_ for **o Microsoft.Compute/virtualMachines,** então o utilizador deve ter a operação **Microsoft.Compute/virtualMachines/read.** Se o utilizador não tiver a operação necessária, é apresentado um erro de acesso.

Para ver os detalhes de conformidade, siga estes passos:

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

1. Na página **'Visão Geral'** ou **Compliance,** selecione uma política num estado de **conformidade** que _não esteja em conformidade_.

1. No separador **de conformidade de recursos** da página de conformidade da **Política,** clique à direita ou selecione a elipse de um recurso num estado de **conformidade** que não está _em conformidade._ Em seguida, **selecione Ver detalhes de conformidade**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Screenshot do link 'Ver detalhes de conformidade' no separador de conformidade de recursos." border="false":::

1. O painel **de detalhes do Compliance** exibe informações da última avaliação do recurso à atribuição de políticas em curso. Neste exemplo, o campo **Microsoft.Sql/servidors/versão** é encontrado como _sendo 12.0,_ enquanto a definição de política esperava _14.0_. Se o recurso não for conforme por várias razões, cada um está listado neste painel.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Screenshot do painel de detalhes de conformidade e razões para o incumprimento que o valor atual é doze e o valor-alvo é de catorze." border="false":::

   Para uma **auditoriaIfNotExists** ou implementar a definição de **políticaifNotExists,** os detalhes incluem os **detalhes.digitar** propriedade e quaisquer propriedades opcionais. Para obter uma lista, consulte [as propriedades auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [implementar propriedadesIfNotExists](../concepts/effects.md#deployifnotexists-properties). **O último recurso avaliado** é um recurso relacionado a partir da secção de **detalhes** da definição.

   Exemplo **de implementação parcial DefiniçãoIfNotExists:**

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Screenshot of Compliance details pane for ifNotExists including evaluated resource count." border="false":::

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é _um segredo,_ o valor atual exibe asteriscos.

Estes detalhes explicam por que um recurso é atualmente incompatível, mas não mostram quando a alteração foi feita ao recurso que o fez tornar-se incompatível. Para obter esta informação, consulte [alterar a história (Preview)](#change-history) abaixo.

### <a name="compliance-reasons"></a>Razões de conformidade

A seguinte matriz mapeia cada _razão_ possível para a [condição](../concepts/definition-structure.md#conditions) responsável na definição de política:

|Razão | Condição |
|-|-|
|O valor atual deve conter o valor-alvo como chave. |contémKey  ou nãoContainsKey |
|O valor atual deve conter o valor-alvo. |contém  ou nãoContains |
|O valor atual deve ser igual ao valor-alvo. |é igual  ou nãoQuals |
|O valor atual deve ser inferior ao valor-alvo. |menos ou **menos** grandes Equadores |
|O valor atual deve ser superior ou igual ao valor-alvo. |maiorOrEquals ou **não** menos |
|O valor atual deve ser superior ao valor-alvo. |maior ou **não** lessOrQuals |
|O valor atual deve ser inferior ou igual ao valor-alvo. |lessOrEquals ou **não** maior |
|O valor atual deve existir. |existe |
|O valor atual deve estar no valor-alvo. |em ou **não** |
|O valor atual deve ser igual ao valor-alvo. |gosto ou **não** Como |
|O valor atual deve corresponder ao valor-alvo. |corresponder ou **nãoMatch** |
|O valor atual deve corresponder ao valor-alvo. |matchInsensitively  ou nãoMatchInsensitively |
|O valor atual não deve conter o valor-alvo como chave. |nãoContainsKey ou **não** contémKey|
|O valor atual não deve conter o valor-alvo. |não Contém ou **não** |
|O valor atual não deve ser igual ao valor-alvo. |não Iguais ou **não** |
|O valor atual não deve existir. |**não** existe  |
|O valor atual não deve estar no valor-alvo. |não Entrar ou **não** em |
|O valor atual não deve ser como o valor-alvo. |não gostar ou **não** |
|O valor atual não deve corresponder ao valor-alvo sensível ao caso. |não Jogo ou **não** |
|O valor atual não deve corresponder ao valor-alvo por casos insensíveis. |notMatchInsensitively ou **não** corresponderInsensitively |
|Nenhum dos recursos relacionados corresponde aos detalhes do efeito na definição de política. |Um recurso do tipo definido em **então.details.type** e relacionado com o recurso definido na **parte** da regra política não existe. |

## <a name="component-details-for-resource-provider-modes"></a>Detalhes dos componentes para os modos fornecedores de recursos

Para atribuições com [um modo Fornecedor de Recursos,](../concepts/definition-structure.md#resource-manager-modes)selecione o recurso não _conforme_ para abrir uma visão mais profunda. No separador Conformidade do **Componente** _encontra-se_ informações adicionais específicas do modo Fornecedor de Recursos na política atribuída que mostra o **Componente** e **O ID do Componente** Não Conforme.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Screenshot do separador de conformidade de componentes e detalhes de conformidade para uma atribuição do modo fornecedor de recursos." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade da Configuração de Convidado

Para as políticas _de auditIfNotExists_ na categoria _Configuração_ de Convidados, pode haver várias configurações avaliadas dentro da máquina virtual e você precisará ver detalhes por definição. Por exemplo, se estiver a auditar uma lista de políticas de passwords e apenas uma delas tiver estatuto Não conforme, terá de saber quais as políticas específicas de _palavra-passe_ que estão fora de conformidade e porquê.

Também pode não ter acesso a iniciar sômedíssia diretamente na máquina virtual, mas precisa de informar por que razão a máquina virtual não _está em conformidade_.

### <a name="azure-portal"></a>Portal do Azure

Comece por seguir os mesmos passos na secção acima para visualizar detalhes de conformidade da política.

Na vista de detalhes da Conformidade, selecione o link **Último recurso avaliado.**

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Screenshot de visualização da auditoriaIfNotExists definiu detalhes de conformidade." border="false":::

A página **de Atribuição de Hóspedes** exibe todos os detalhes de conformidade disponíveis. Cada linha na vista representa uma avaliação que foi realizada dentro da máquina. Na coluna **Reason,** é mostrada uma frase que descreve por que a atribuição de hóspedes não _é compatível_. Por exemplo, se estiver a auditar políticas de palavra-passe, a coluna **Reason** apresentaria texto, incluindo o valor atual para cada definição.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Screenshot dos detalhes de conformidade da atribuição de hóspedes." border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Alterar história (Pré-visualização)

Como parte de uma nova **pré-visualização pública,** os últimos 14 dias de história da mudança estão disponíveis para todos os recursos Azure que suportam [a eliminação completa do modo](../../../azure-resource-manager/templates/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma mudança foi detetada e um _diff visual_ para cada mudança. Uma deteção de alterações é desencadeada quando as propriedades do Gestor de Recursos Azure são adicionadas, removidas ou alteradas.

1. Lançar o serviço Azure Policy no portal Azure selecionando **todos os serviços,** procurando e selecionando **a Política**.

1. Na página **'Visão Geral'** ou **Compliance,** selecione uma política em qualquer **estado de conformidade**.

1. No separador **de conformidade de recursos** da página de conformidade da **Política,** selecione um recurso.

1. Selecione o separador Alterar Histórico **(pré-visualização)** na página **'Conformidade com recursos'.** É apresentada uma lista de alterações detetadas, caso existam.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Screenshot do separador Alterar Histórico e detetado tempos de alteração na página de Conformidade de Recursos." border="false":::

1. Selecione uma das alterações detetadas. O _diff visual_ para o recurso é apresentado na página **de história da Mudança.**

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Screenshot do Diff Visual Change History do estado de propriedades antes e depois da página de história da Mudança." border="false":::

Os assessores _de difusão visual_ na identificação de alterações a um recurso. As alterações detetadas podem não estar relacionadas com o estado de conformidade atual do recurso.

Os dados de histórico de alteração são fornecidos pelo [Azure Resource Graph](../../resource-graph/overview.md). Para consultar estas informações fora do portal Azure, consulte [Obter alterações de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [obter dados de conformidade.](get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
