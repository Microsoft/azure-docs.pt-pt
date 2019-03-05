---
title: Determinar as causas da não conformidade
description: Quando um recurso está em conformidade, existem muitas razões possíveis. Aprenda a saber o que causou a não conformidade.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: dba8d9413229a0fa236b082e2e11dbd1a9fe5a5f
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314131"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas da não conformidade

Quando um recurso do Azure é identificado como não conforme a uma regra de política, é útil compreender qual a parte da regra o recurso não está em conformidade com. Também é útil para entender quais alteração alterado um recurso anteriormente em conformidade para torná-lo em não conformidade. Existem duas formas de localizar estas informações:

> [!div class="checklist"]
> - [Detalhes de conformidade](#compliance-details)
> - [Histórico de alterações (pré-visualização)](#change-history-preview)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso está em conformidade, os detalhes de conformidade para esse recurso estão disponíveis a partir da **conformidade com a política** página. O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes do recurso, como o nome, tipo, localização e ID de recurso
- Estado de conformidade e timestamp da última avaliação para a atribuição de política atual
- Uma lista de _motivos_ para a recurso não conformidade

> [!IMPORTANT]
> Como os detalhes de conformidade para um _não compatível_ Recurso mostra o valor atual de propriedades esse recurso, o utilizador tem de ter **ler** operação para o **tipo** de recurso. Por exemplo, se o _não compatível_ recurso está **Microsoft.Compute/virtualMachines** , em seguida, o utilizador tem de ter o **Microsoft.Compute/virtualMachines/read** operação. Se o utilizador não tem a operação necessária, é apresentado um erro de acesso.

Para ver os detalhes de conformidade, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Sobre o **descrição geral** ou **conformidade** , selecione uma política num **estado de conformidade** ou seja _em não conformidade_.

1. Sob o **conformidade do recurso** separador da **conformidade com a política** página, faça duplo clique ou selecione as reticências de um recurso num **estado de conformidade** ou seja  _Não compatível_. Em seguida, selecione **ver os detalhes de conformidade**.

   ![Opção de exibição de detalhes de conformidade](../media/determine-non-compliance/view-compliance-details.png)

1. O **detalhes de conformidade** painel apresenta informações de avaliação mais recente do recurso para a atribuição de política atual. Neste exemplo, o campo **Microsoft.Sql/servers/version** for _12.0_ enquanto a definição de política esperada _14,0_. Se o recurso está em conformidade por vários motivos, cada um está listado neste painel.

   ![Painel de detalhes de conformidade](../media/determine-non-compliance/compliance-details-pane.png)

   Para uma **auditIfNotExists** ou **deployIfNotExists** definição de política, os detalhes incluem o **details.type** propriedade e todas as propriedades opcionais. Para obter uma lista, consulte [propriedades de auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [deployIfNotExists propriedades](../concepts/effects.md#deployifnotexists-properties). **Avaliado pela última vez resource** é um recurso relacionado do **detalhes** secção da definição.

   Exemplo parcial **deployIfNotExists** definição:

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

   ![Painel de detalhes de conformidade - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger os dados, quando um valor de propriedade é um _segredo_ o valor atual apresenta asteriscos.

Estes detalhes explicam por que um recurso está atualmente em não conformidade, mas não mostram quando a alteração foi feita para o recurso que provocou a se tornar não conforme sua. Para obter essas informações, consulte [(pré-visualização) de histórico de alterações](#change-history-preview) abaixo.

### <a name="compliance-reasons"></a>Motivos de conformidade

A matriz seguinte mapeia cada possível _motivo_ para o responsável [condição](../concepts/definition-structure.md#conditions) na definição de política:

|Razão | Condição |
|-|-|
|O valor atual tem de conter o valor de destino como uma chave. |containsKey ou **não** notContainsKey |
|O valor atual tem de conter o valor de destino. |contém ou **não** notContains |
|O valor atual tem de ser igual ao valor de destino. |é igual a ou **não** notEquals |
|O valor atual tem de existir. |Existe |
|O valor atual tem de estar no valor de destino. |em ou **não** notIn |
|O valor atual tem de ser como o valor de destino. |como ou **não** notLike |
|Valor atual tem o valor de destino de correspondência de maiúsculas e minúsculas. |corresponder ou **não** notMatch |
|Valor atual tem o valor de destino de correspondência de maiúsculas e minúsculas. |matchInsensitively ou **não** notMatchInsensitively |
|O valor atual não pode conter o valor de destino como uma chave. |notContainsKey ou **não** containsKey|
|O valor atual não pode conter o valor de destino. |notContains ou **não** contém |
|O valor atual não pode ser igual ao valor de destino. |notEquals ou **não** é igual a |
|O valor atual não pode existir. |**não** existe  |
|O valor atual não pode estar no valor de destino. |notIn ou **não** no |
|O valor atual não pode ser como o valor de destino. |notLike ou **não** como |
|Valor atual tem correspondência não diferencia maiúsculas de minúsculas, o valor de destino. |notMatch ou **não** corresponder |
|Valor atual tem correspondência não maiúsculas de minúsculas, o valor de destino. |notMatchInsensitively ou **não** matchInsensitively |
|Não existem recursos relacionados que correspondem aos detalhes de efeito na definição de política. |Um recurso do tipo definido na **then.details.type** e esteja relacionado com o recurso definido no **se** parte da regra de política não existe. |

## <a name="change-history-preview"></a>Histórico de alterações (pré-visualização)

Como parte de um novo **pré-visualização pública**, os últimos 14 dias de alteração está disponível para todos os recursos do Azure que suportam o histórico [concluir a eliminação de modo](../../../azure-resource-manager/complete-mode-deletion.md). Histórico de alterações fornece detalhes sobre o quando foi detetada uma alteração e um _visual diff_ para cada alteração. Uma deteção de alteração é acionada quando são adicionadas, remover ou alterar as propriedades do Gestor de recursos.

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Sobre o **descrição geral** ou **conformidade** , selecione uma política em qualquer **estado de conformidade**.

1. Sob o **conformidade do recurso** separador da **conformidade com a política** , selecione um recurso.

1. Selecione o **histórico de alterações (pré-visualização)** guia a **conformidade do recurso** página. Uma lista de detetou alterações, se qualquer exist, são apresentados.

   ![Histórico de alterações de política - separador](../media/determine-non-compliance/change-history-tab.png)

1. Selecione uma das alterações detetadas. O _visual diff_ para o recurso é apresentado no **histórico de alterações** página.

   ![Histórico de alterações de política - Visual Diff](../media/determine-non-compliance/change-history-visual-diff.png)

O _visual diff_ aides na identificação de alterações a um recurso. Foram detetadas as alterações podem não estar relacionados com o atual estado de conformidade do recurso.

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md)
- Reveja o [estrutura de definição de política](../concepts/definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](../concepts/effects.md)
- Compreender como [criar políticas programaticamente](programmatically-create.md)
- Saiba como [obter dados de conformidade](getting-compliance-data.md)
- Saiba como [remediar recursos não compatíveis](remediate-resources.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](../../management-groups/overview.md)