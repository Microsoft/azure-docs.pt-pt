---
title: Compreender o bloqueio de recursos
description: Conheça as opções de bloqueio em Plantas Azure para proteger os recursos ao atribuir uma planta.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94ed8efd0d6c654cba129dfc69fbfe5add7a0824
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383597"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Compreenda o bloqueio de recursos em Plantas Azure

A criação de ambientes consistentes à escala só é verdadeiramente valiosa se houver um mecanismo para manter essa consistência. Este artigo explica como funciona o bloqueio de recursos em Plantas Azure. Para ver um exemplo de bloqueio de recursos e aplicação de _missões de negação,_ consulte a [proteção](../tutorials/protect-new-resources.md) de novos recursos tutoriais.

> [!NOTE]
> Os bloqueios de recursos implantados pela Azure Blueprints são aplicados apenas aos recursos implantados pela atribuição do projeto. Os recursos existentes, como os dos grupos de recursos que já existem, não têm fechaduras adicionadas a eles.

## <a name="locking-modes-and-states"></a>Modos e estados de bloqueio

O modo de bloqueio aplica-se à atribuição da planta e tem três opções: **Não bloquear,** **ler apenas**ou **não eliminar**. O modo de bloqueio é configurado durante a implantação do artefacto durante uma atribuição de plantas. Um modo de bloqueio diferente pode ser definido atualizando a atribuição da planta.
Os modos de bloqueio, no entanto, não podem ser alterados fora das Plantas Azure.

Os recursos criados por artefactos numa atribuição de plantas têm quatro estados: **Não bloqueado,** **Leia Apenas,** **Não Pode Editar / Excluir,** ou **Não Pode Excluir**. Cada tipo de artefacto pode estar no estado **não bloqueado.** A tabela a seguir pode ser utilizada para determinar o estado de um recurso:

|Modo|Tipo de recurso de artefacto|Estado|Descrição|
|-|-|-|-|
|Não tranque|*|Não trancado|Os recursos não estão protegidos por Plantas Azure. Este estado também é usado para recursos adicionados a um artefacto de grupo **de recursos Read Only** ou Not **Delete** from outside a blueprint assignment.|
|Só de Leitura|Grupo de recursos|Não pode Editar / Excluir|O grupo de recursos é lido apenas e as etiquetas no grupo de recursos não podem ser modificadas. Os recursos **bloqueados não** podem ser adicionados, movidos, alterados ou eliminados deste grupo de recursos.|
|Só de Leitura|Grupo sem recursos|Só de Leitura|O recurso não pode ser alterado de forma alguma.|
|Não apague|*|Não pode Excluir|Os recursos podem ser alterados, mas não podem ser apagados. Os recursos **bloqueados não** podem ser adicionados, movidos, alterados ou eliminados deste grupo de recursos.|

## <a name="overriding-locking-states"></a>Estados de bloqueio dominantes

É tipicamente possível que alguém com um controlo de acesso baseado [em funções](../../../role-based-access-control/overview.md) apropriado (RBAC) na subscrição, como a função 'Proprietário', seja autorizado a alterar ou apagar qualquer recurso. Este acesso não é o caso quando o Azure Blueprints aplica o bloqueio como parte de uma missão implementada. Se a atribuição foi definida com a opção **Ler Apenas** ou **Não Eliminar,** nem mesmo o proprietário da subscrição pode executar a ação bloqueada no recurso protegido.

Esta medida de segurança protege a consistência do projeto definido e do ambiente que foi concebido para criar a partir de supressão ou alteração acidental ou programática.

### <a name="assign-at-management-group"></a>Atribuição no grupo de gestão

Uma opção adicional para impedir que os proprietários de subscrições removam uma atribuição de plantas é atribuir o projeto a um grupo de gestão. Neste cenário, apenas os **proprietários** do grupo de gestão têm as permissões necessárias para remover a atribuição do projeto.

Para atribuir o projeto a um grupo de gestão em vez de uma subscrição, a chamada rest API muda para se parecer com este:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

O grupo de `{assignmentMG}` gestão definido deve estar dentro da hierarquia do grupo de gestão ou ser o mesmo grupo de gestão onde a definição de projeto é guardada.

O corpo de pedido da atribuição da planta é assim:

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

A diferença fundamental neste órgão de pedido e uma `properties.scope` a ser atribuída a uma subscrição é a propriedade. Este imóvel necessário deve ser definido para a subscrição a que a atribuição do projeto se aplica. A subscrição deve ser uma criança direta da hierarquia do grupo de gestão onde a atribuição do projeto está armazenada.

> [!NOTE]
> Um projeto atribuído ao âmbito do grupo de gestão ainda funciona como uma atribuição de modelo de nível de subscrição. A única diferença é onde a atribuição do projeto é armazenada para impedir que os proprietários de subscrições removam a atribuição e fechaduras associadas.

## <a name="removing-locking-states"></a>Remoção de estados de bloqueio

Se for necessário modificar ou eliminar um recurso protegido por uma atribuição, existem duas formas de o fazer.

- Atualizar a atribuição da planta para um modo de bloqueio de **Don't Lock**
- Eliminar a atribuição da planta

Quando a atribuição é removida, as fechaduras criadas por Plantas Azure são removidas. No entanto, o recurso é deixado para trás e teria de ser apagado através de meios normais.

## <a name="how-blueprint-locks-work"></a>Como funcionam as fechaduras de plantas

Um RBAC [nega que as atribuições](../../../role-based-access-control/deny-assignments.md) negem que a ação é aplicada aos recursos de artefactos durante a atribuição de um projeto se a atribuição selecionou a opção **Ler Apenas** ou **Não Excluir.** A ação de negação é adicionada pela identidade gerida da atribuição do projeto e só pode ser removida dos recursos do artefacto pela mesma identidade gerida. Esta medida de segurança aplica o mecanismo de bloqueio e impede a remoção do bloqueio de plantas fora das plantas azure.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Projeto negar atribuição no grupo de recursos" border="false":::

As propriedades de [atribuição de negar](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) de cada modo são as seguintes:

|Modo |Permissões.Ações |Permissões.NotActions |Diretores[i]. Tipo |Excluir os Diretores[i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Só de Leitura |**\*** |**\*/ler** |SystemDefined (Todos) |atribuição de plantas e definida pelo utilizador em **diretores excluídos** |Grupo de recursos - _verdadeiro;_ Recurso - _falso_ |
|Não apague |**\*/excluir** | |SystemDefined (Todos) |atribuição de plantas e definida pelo utilizador em **diretores excluídos** |Grupo de recursos - _verdadeiro;_ Recurso - _falso_ |

> [!IMPORTANT]
> O Gestor de Recursos Azure caches detalhes de atribuição de funções por um máximo de 30 minutos. Como resultado, negar atribuições negam que as ações sobre os recursos de projeto podem não estar imediatamente em vigor. Durante este período de tempo, poderá ser possível eliminar um recurso destinado a ser protegido por fechaduras de plantas.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Excluir um diretor de uma atribuição de negação

Em alguns cenários de conceção ou segurança, pode ser necessário excluir um diretor da atribuição de [negação](../../../role-based-access-control/deny-assignments.md) que a atribuição de plantas cria. Este passo é feito na API REST adicionando até cinco **valores** à matriz excluída dos Principais na propriedade **locks** ao [criar a atribuição](/rest/api/blueprints/assignments/createorupdate). A seguinte definição de atribuição é um exemplo de um órgão de pedido que inclui **os diretores excluídos:**

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

Semelhante a [excluir um principal](#exclude-a-principal-from-a-deny-assignment) numa atribuição de [negação](../../../role-based-access-control/deny-assignments.md) numa atribuição de projeto, pode excluir [operações rBAC específicas](../../../role-based-access-control/resource-provider-operations.md). Dentro do bloco **de propriedades.fechaduras,** no mesmo local que os **Diretores excluídos,** pode ser adicionada uma **ação excluída:**

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

Embora **os Diretores excluídos sejam explícitos,** as entradas **excluídas podem** utilizar para a correspondência wildcard `*` das operações RBAC.

## <a name="next-steps"></a>Passos seguintes

- Siga o tutorial [de novos recursos protegidos.](../tutorials/protect-new-resources.md)
- Conheça o ciclo de vida da [planta.](lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).