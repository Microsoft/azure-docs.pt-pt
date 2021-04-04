---
title: Compreender o bloqueio de recursos
description: Saiba mais sobre as opções de bloqueio nas Plantas Azure para proteger os recursos ao atribuir uma planta.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920195"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Compreender o bloqueio de recursos em Azure Blueprints

A criação de ambientes consistentes à escala só é verdadeiramente valiosa se houver um mecanismo para manter essa consistência. Este artigo explica como funciona o bloqueio de recursos na Azure Blueprints. Para ver um exemplo de bloqueio de recursos e aplicação de _atribuições de negação,_ consulte o tutorial [de novos recursos.](../tutorials/protect-new-resources.md)

> [!NOTE]
> Os bloqueios de recursos implantados pela Azure Blueprints são aplicados apenas aos recursos utilizados pela atribuição do projeto. Os recursos existentes, como os de grupos de recursos que já existem, não têm fechaduras adicionadas a eles.

## <a name="locking-modes-and-states"></a>Modos e estados de bloqueio

O modo de bloqueio aplica-se à atribuição da planta e tem três opções: **Não bloquear,** **ler apenas,** ou **não excluir**. O modo de bloqueio é configurado durante a colocação do artefacto durante uma atribuição de planta. Um modo de bloqueio diferente pode ser definido atualizando a atribuição da planta.
Os modos de bloqueio, no entanto, não podem ser alterados fora das Plantas Azure.

Os recursos criados por artefactos numa atribuição de plantas têm quatro estados: **Não Bloqueados,** **Ler Apenas,** **Não Poder Editar / Excluir,** ou **Não Pode Excluir**. Cada tipo de artefacto pode estar no estado **não bloqueado.** Pode utilizar-se a tabela seguinte para determinar o estado de um recurso:

|Modo|Tipo de recurso de artefacto|Estado|Descrição|
|-|-|-|-|
|Não tranque|*|Não trancada|Os recursos não estão protegidos pela Azure Blueprints. Este estado também é utilizado para recursos adicionados a um artefacto do grupo **de recursos Read Only** ou Não **Eliminar** fora de uma atribuição de planta.|
|Só de Leitura|Grupo de recursos|Não é possível editar / Eliminar|O grupo de recursos é lido apenas e as etiquetas no grupo de recursos não podem ser modificadas. Os recursos **não bloqueados** podem ser adicionados, movidos, alterados ou eliminados deste grupo de recursos.|
|Só de Leitura|Grupo de não recursos|Só de Leitura|O recurso não pode ser alterado de forma alguma. Sem alterações e não pode ser apagado.|
|Não apagar|*|Não é possível apagar|Os recursos podem ser alterados, mas não podem ser apagados. Os recursos **não bloqueados** podem ser adicionados, movidos, alterados ou eliminados deste grupo de recursos.|

## <a name="overriding-locking-states"></a>Estados de bloqueio dominantes

É normalmente possível que alguém com [um controlo de acesso baseado em funções Azure (Azure RBAC)](../../../role-based-access-control/overview.md) na subscrição, como o papel de "Proprietário", possa alterar ou eliminar qualquer recurso. Este acesso não é o caso quando a Azure Blueprints aplica o bloqueio como parte de uma missão implantada. Se a atribuição tiver sido definida com a opção **Ler Apenas** ou **Não Eliminar,** nem mesmo o proprietário da subscrição pode executar a ação bloqueada no recurso protegido.

Esta medida de segurança protege a consistência da planta definida e do ambiente que foi concebido para criar a partir de eliminação ou alteração acidental ou programática.

### <a name="assign-at-management-group"></a>Atribuir no grupo de gestão

A única opção para impedir que os proprietários de subscrições retirem uma atribuição de projeto é atribuir o projeto a um grupo de gestão. Neste cenário, **apenas os proprietários** do grupo de gestão têm as permissões necessárias para remover a atribuição do projeto.

Para atribuir o projeto a um grupo de gestão em vez de uma subscrição, a chamada de API REST muda para se parecer com isto:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

O grupo de gestão definido deve `{assignmentMG}` ser dentro da hierarquia do grupo de gestão ou ser o mesmo grupo de gestão onde a definição de projeto é guardada.

O corpo de pedido da atribuição de plantas é assim:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

A principal diferença neste órgão de pedido e uma delas a ser atribuída a uma subscrição é a `properties.scope` propriedade. Esta propriedade requerida deve ser definida para a subscrição a que a atribuição do projeto se aplica. A subscrição deve ser uma criança direta da hierarquia do grupo de gestão onde a atribuição do projeto está armazenada.

> [!NOTE]
> Um projeto atribuído ao âmbito do grupo de gestão ainda funciona como uma atribuição de projeto de nível de subscrição. A única diferença é onde a atribuição do projeto é armazenada para impedir que os proprietários de subscrição retirem a atribuição e as fechaduras associadas.

## <a name="removing-locking-states"></a>Remoção de estados de bloqueio

Se for necessário modificar ou eliminar um recurso protegido por uma atribuição, existem duas formas de o fazer.

- Atualizar a atribuição da planta para um modo de bloqueio de **Don't Lock**
- Eliminar a atribuição de plantas

Quando a atribuição é removida, as fechaduras criadas pela Azure Blueprints são removidas. No entanto, o recurso é deixado para trás e teria de ser eliminado através de meios normais.

## <a name="how-blueprint-locks-work"></a>Como funcionam as fechaduras de plantas

Um RBAC [Azure nega que as atribuições](../../../role-based-access-control/deny-assignments.md) neguem que a ação seja aplicada aos recursos de artefactos durante a atribuição de um projeto se a atribuição selecionar a opção Read **Only** ou **Not Delete.** A ação de negação é adicionada pela identidade gerida da atribuição do projeto e só pode ser removida dos recursos do artefacto pela mesma identidade gerida. Esta medida de segurança impõe o mecanismo de bloqueio e impede a remoção do bloqueio da planta fora das Plantas Azure.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Screenshot da página Access control (I A M) e do separador de atribuições Deny para um grupo de recursos." border="false":::

As propriedades de [atribuição de negação](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) de cada modo são as seguintes:

|Modo |Permissões.Ações |Permissões.NotActions |Diretores[i]. Tipo |Excluir os Príncipes[i]. ID | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Só de Leitura |**\** _ |_ *\* /ler **<br />** Microsoft.Authorization/locks/delete **<br />** Microsoft.Network/virtualNetwork/subnets/join/action** |SystemDefined (Todos) |design de design e definido pelo utilizador **emPriciplinas excluídos** |Grupo de recursos - _verdadeiro;_ Recurso - _falso_ |
|Não apagar |**\*/eliminar** | **Microsoft.Autorização/bloqueios/exclusão**<br />**Microsoft.Network/virtualNetwork/sub-redes/join/action** |SystemDefined (Todos) |design de design e definido pelo utilizador **emPriciplinas excluídos** |Grupo de recursos - _verdadeiro;_ Recurso - _falso_ |

> [!IMPORTANT]
> Azure Resource Manager caches detalhes de atribuição de funções por até 30 minutos. Como resultado, negar atribuições que negam que as ações sobre recursos de planta possam não estar imediatamente em vigor. Durante este período de tempo, poderá ser possível eliminar um recurso destinado a ser protegido por fechaduras de plantas.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Excluir um principal de uma atribuição de negação

Em alguns cenários de design ou segurança, pode ser necessário excluir um principal da atribuição de [negação](../../../role-based-access-control/deny-assignments.md) que a atribuição de projeto cria. Este passo é feito na REST API, somando até cinco valores à matriz **dePrincipals excluída** na propriedade **de fechaduras** [ao criar a atribuição.](/rest/api/blueprints/assignments/createorupdate) A seguinte definição de atribuição é um exemplo de um organismo de pedido que inclui **osprincipales excluídos:**

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Excluir uma ação de uma atribuição de negação

Tal como [excluir um principal](#exclude-a-principal-from-a-deny-assignment) numa atribuição de [negação](../../../role-based-access-control/deny-assignments.md) numa atribuição de um projeto, pode excluir [operações específicas do fornecedor de recursos Azure](../../../role-based-access-control/resource-provider-operations.md). Dentro do bloco **properties.locks,** no mesmo local que **osPrincipals excluídos,** pode ser adicionado um **excluindoActions:**

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Embora **os Prncipals excluídos** devam ser explícitos, as entradas **excluídas deacções** podem utilizar `*` para a correspondência de wildcard das operações do fornecedor de recursos.

## <a name="next-steps"></a>Passos seguintes

- Siga o tutorial [de novos recursos.](../tutorials/protect-new-resources.md)
- Saiba mais sobre o [ciclo de vida do esquema](./lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](./sequencing-order.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).
