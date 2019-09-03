---
title: Entender o bloqueio de recursos
description: Saiba mais sobre as opções de bloqueio para proteger recursos ao atribuir um plano gráfico.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 8d3cee73d8614c4aea2d2883cdcf2f049b1b8f67
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232943"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Entender o bloqueio de recursos em plantas do Azure

A criação de ambientes consistentes em escala só será verdadeiramente valiosa se houver um mecanismo para manter essa consistência. Este artigo explica como o bloqueio de recursos funciona em plantas do Azure. Para ver um exemplo de bloqueio de recursos e aplicaçãode atribuições de negação, consulte o tutorial [protegendo novos recursos](../tutorials/protect-new-resources.md) .

## <a name="locking-modes-and-states"></a>Modos de bloqueio e Estados

O modo de bloqueio se aplica à atribuição Blueprint e tem três opções: **Não bloqueie**, **somente leitura**ou **não exclua**. O modo de bloqueio é configurado durante a implantação do artefato durante uma atribuição de Blueprint. Um modo de bloqueio diferente pode ser definido atualizando a atribuição Blueprint.
No entanto, os modos de bloqueio não podem ser alterados fora dos planos gráficos.

Os recursos criados por artefatos em uma atribuição Blueprint têm quatro Estados: **Não bloqueado**, **somente leitura**, **não é possível editar/Excluir**ou **não pode excluir**. Cada tipo de artefato pode estar no estado **não bloqueado** . A tabela a seguir pode ser usada para determinar o estado de um recurso:

|Modo|Tipo de recurso de artefato|State|Descrição|
|-|-|-|-|
|Não bloquear|*|Não bloqueado|Os recursos não são protegidos por plantas. Esse estado também é usado para recursos adicionados a um artefato **somente leitura** ou **não excluir** do grupo de recursos de fora de uma atribuição Blueprint.|
|Só de Leitura|Resource group|Não é possível editar/excluir|O grupo de recursos é somente leitura e as marcas no grupo de recursos não podem ser modificadas. Recursos **não bloqueados** podem ser adicionados, movidos, alterados ou excluídos deste grupo de recursos.|
|Só de Leitura|Grupo de não recursos|Só de Leitura|O recurso não pode ser alterado de nenhuma forma, sem alterações e não pode ser excluído.|
|Não Eliminar|*|Não é possível excluir|Os recursos podem ser alterados, mas não podem ser excluídos. Recursos **não bloqueados** podem ser adicionados, movidos, alterados ou excluídos deste grupo de recursos.|

## <a name="overriding-locking-states"></a>Substituindo Estados de bloqueio

Normalmente, é possível que alguém com RBAC ( [controle de acesso baseado em função](../../../role-based-access-control/overview.md) ) apropriado na assinatura, como a função ' proprietário ', tenha permissão para alterar ou excluir qualquer recurso. Esse acesso não é o caso quando os planos gráficos aplicam bloqueio como parte de uma atribuição implantada. Se a atribuição tiver sido definida com a opção **somente leitura** ou **não excluir** , nem mesmo o proprietário da assinatura poderá executar a ação bloqueada no recurso protegido.

Essa medida de segurança protege a consistência do plano gráfico definido e do ambiente que ele foi projetado para criar de uma exclusão acidental ou programática ou alteração.

## <a name="removing-locking-states"></a>Removendo Estados de bloqueio

Se for necessário modificar ou excluir um recurso protegido por uma atribuição, há duas maneiras de fazer isso.

- Atualizar a atribuição Blueprint para um modo de bloqueio de **não bloquear**
- Excluir a atribuição de Blueprint

Quando a atribuição é removida, os bloqueios criados por plantas são removidos. No entanto, o recurso é deixado para trás e precisa ser excluído por meio de meios normais.

## <a name="how-blueprint-locks-work"></a>Como os bloqueios de plantas funcionam

Uma ação [](../../../role-based-access-control/deny-assignments.md) negar atribuições de negação de RBAC será aplicada aos recursos de artefato durante a atribuição de um plano gráfico se a atribuição tiver selecionado a opção **somente leitura** ou **não excluir** . A ação Deny é adicionada pela identidade gerenciada da atribuição Blueprint e só pode ser removida dos recursos de artefato pela mesma identidade gerenciada. Essa medida de segurança impõe o mecanismo de bloqueio e impede a remoção do bloqueio Blueprint fora dos planos gráficos.

![Atribuição de negação de plano gráfico no grupo de recursos](../media/resource-locking/blueprint-deny-assignment.png)

As [Propriedades de atribuição](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) de negação de cada modo são as seguintes:

|Modo |Permissões. ações |Permissões. \ ações |Entidades de segurança [i]. Escreva |ExcludePrincipals [i]. Sessão | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Só de Leitura |**\*** |**\*/read** |SystemDefined (todos) |atribuição de Blueprint e definida pelo usuário em **excludedPrincipals** |Grupo de recursos- _verdadeiro_; Recurso- _falso_ |
|Não Eliminar |**\*/delete** | |SystemDefined (todos) |atribuição de Blueprint e definida pelo usuário em **excludedPrincipals** |Grupo de recursos- _verdadeiro_; Recurso- _falso_ |

> [!IMPORTANT]
> Azure Resource Manager armazena em cache detalhes de atribuição de função por até 30 minutos. Como resultado, as atribuições de negação da ação de negação dos recursos do Blueprint podem não estar imediatamente em pleno efeito. Durante esse período de tempo, pode ser possível excluir um recurso destinado a ser protegido por bloqueios do Blueprint.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Excluir uma entidade de segurança de uma atribuição de negação

Em alguns cenários de design ou segurança, pode ser necessário excluir uma entidade da atribuição de [negação](../../../role-based-access-control/deny-assignments.md) criada pela atribuição Blueprint. Isso é feito na API REST adicionando até cinco valores à matriz **excludedPrincipals** na propriedade Locks ao [criar a atribuição](/rest/api/blueprints/assignments/createorupdate).
Este é um exemplo de um corpo de solicitação que inclui **excludedPrincipals**:

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

## <a name="next-steps"></a>Passos Seguintes

- Siga o tutorial [proteger novos recursos](../tutorials/protect-new-resources.md) .
- Saiba mais sobre o [ciclo de vida do plano gráfico](lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](sequencing-order.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).