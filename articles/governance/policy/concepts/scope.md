---
title: Compreender o âmbito na Política Azure
description: Descreve o conceito de âmbito no Azure Resource Manager e como se aplica à Azure Policy para controlar quais os recursos que a Azure Policy avalia.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 83eda330a35b007abfa37046a202b14728726849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984439"
---
# <a name="understand-scope-in-azure-policy"></a>Compreender o âmbito na Política Azure

Há uma série de configurações que determinam quais os recursos capazes de ser avaliados e quais os recursos que são avaliados pela Azure Policy. O conceito primordial para estes controlos é _o âmbito._
Para obter uma visão geral de alto nível, consulte [Scope in Azure Resource Manager](../../../azure-resource-manager/management/overview.md#understand-scope).
Este artigo explica o impacto do _âmbito_ na Política Azure e os seus objetos e propriedades relacionados.

## <a name="definition-location"></a>Localização de definição

O âmbito de primeira instância utilizado pela Política Azure é quando se cria uma definição de política. A definição pode ser guardada num grupo de gestão ou numa subscrição. O local determina o âmbito a que a iniciativa ou a política podem ser atribuídas. Os recursos devem estar dentro da hierarquia de recursos do local de definição a destino para a atribuição.

Se a localização da definição for:

- **Subscrição** - Apenas os recursos dentro dessa subscrição podem ser atribuídos a definição de política.
- **Grupo de gestão** - Apenas os recursos dentro de grupos de gestão infantil e assinaturas infantis podem ser atribuídos a definição de política. Se pretende aplicar a definição de política a várias subscrições, a localização deve ser um grupo de gestão que contenha cada subscrição.

A localização deve ser o recipiente de recursos partilhado por todos os recursos que pretende utilizar a definição de política existente. Este contentor de recursos é tipicamente um grupo de gestão perto do grupo de gestão de raiz.

## <a name="assignment-scopes"></a>Âmbitos de atribuição

Uma atribuição tem várias propriedades que definem um âmbito. A utilização destas propriedades determina qual o recurso para a Política Azure para avaliar e quais os recursos que contam para o cumprimento. Estas propriedades mapeiam para os seguintes conceitos:

- Inclusão - Uma hierarquia de recursos ou recurso individual deve ser avaliado para o cumprimento por definição. A `properties.scope` propriedade em um objeto de atribuição determina o que incluir e avaliar para conformidade. Para obter mais informações, consulte [a definição de Atribuição.](./assignment-structure.md)

- Exclusão - Uma hierarquia de recursos ou recurso individual não deve ser avaliada para o cumprimento pela definição. A propriedade `properties.notScopes` _de matriz_ em um objeto de atribuição determina o que excluir. Os recursos dentro destes âmbitos não são avaliados ou incluídos na contagem de conformidade. Para obter mais informações, consulte [a definição de atribuição - âmbitos excluídos](./assignment-structure.md#excluded-scopes).

Além dos imóveis na atribuição de políticas, é o objeto [de isenção de política.](./exemption-structure.md) As isenções melhoram a história do âmbito, fornecendo um método para identificar uma parte de uma atribuição a não ser avaliada.

- Isenção **(grátis em recurso de pré-visualização)** - Uma hierarquia de recursos ou recurso individual deve ser avaliada para o cumprimento por definição, mas não será avaliada por uma razão como ter uma renúncia ou ser atenuada através de outro método. Os recursos neste estado mostram-se **isentos** nos relatórios de conformidade para que possam ser rastreados. O objeto de isenção é criado na hierarquia de recursos ou recurso individual como objeto infantil, que determina o âmbito da isenção. Uma hierarquia de recursos ou recurso individual pode ser isenta de múltiplas atribuições. A isenção pode ser configurada para expirar em um horário através da utilização do `expiresOn` imóvel. Para mais informações, consulte [a definição de Isenção.](./exemption-structure.md)

  > [!NOTE]
  > Devido ao impacto da concessão de uma isenção para uma hierarquia de recursos ou recursos individuais, as isenções têm medidas de segurança adicionais. Além de exigir a `Microsoft.Authorization/policyExemptions/write` operação na hierarquia dos recursos ou no recurso individual, o criador de uma isenção deve ter o `exempt/Action` verbo na atribuição do destino.

## <a name="scope-comparison"></a>Comparação de âmbito

O quadro a seguir é uma comparação das opções de âmbito:

| | Inclusão | Exclusão (não Scópios) | Isenção |
|---|:---:|:---:|:---:|
|**Os recursos são avaliados** | &#10004; | - | - |
|**Objeto do Gestor de Recursos** | - | - | &#10004; |
|**Requer modificar o objeto de atribuição de políticas** | &#10004; | &#10004; | - |

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura de [definição de políticas.](./definition-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)