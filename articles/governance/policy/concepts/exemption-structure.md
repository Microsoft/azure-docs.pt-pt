---
title: Detalhes da estrutura de isenção de políticas
description: Descreve a definição de isenção de política utilizada pela Azure Policy para isentar os recursos da avaliação de iniciativas ou definições.
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1fd14d31824dc86dcd3788607030f28f978f5801
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968052"
---
# <a name="azure-policy-exemption-structure"></a>Estrutura de isenção de política Azure

A funcionalidade de isenções da Política Azure (pré-visualização) é utilizada para _isentar_ uma hierarquia de recursos ou um recurso individual da avaliação de iniciativas ou definições. Os recursos que estão _isentos_ contam para o cumprimento geral, mas não podem ser avaliados ou ter uma renúncia temporária. Para mais informações, consulte [o âmbito de aplicação da Política Azure.](./scope.md) As isenções da Política Azure apenas funcionam com [os modos gestor de recursos](./definition-structure.md#resource-manager-modes) e não funcionam com os **modos fornecedores de recursos**.

> [!IMPORTANT]
> Esta funcionalidade é gratuita durante **a pré-visualização**. Para obter detalhes sobre os preços, consulte [os preços da Política Azure](https://azure.microsoft.com/pricing/details/azure-policy/). Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Usas o JSON para criar uma isenção de política. A isenção de política contém elementos para:

- nome de exibição
- descrição
- do IdP
- atribuição de política
- definições de política dentro de uma iniciativa
- categoria de isenção
- expiração

> [!NOTE]
> Uma isenção de política é criada como um objeto infantil na hierarquia de recursos ou no recurso individual concedido a isenção, pelo que o alvo não está incluído na definição de isenção.

Por exemplo, o seguinte JSON mostra uma isenção de política na categoria de **isenção** de recurso para uma atribuição de iniciativa denominada `resourceShouldBeCompliantInit` . O recurso está _isento_ de apenas duas das definições políticas da iniciativa, a `customOrgPolicy` definição de política personalizada `requiredTags` (referência) e a definição de política incorporada "Locais Permitidos" (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c` , `allowedLocations` referência):

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

Snippet da iniciativa conexa com a correspondência `policyDefinitionReferenceIds` utilizada pela isenção de política:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>Nome e descrição do visor

Utiliza o nome e **a descrição** **do displayName** para identificar a isenção de política e fornecer contexto para a sua utilização com o recurso específico. **displayName** tem um comprimento máximo de _128_ caracteres e **descrição** de um comprimento máximo de _512_ caracteres.

## <a name="metadata"></a>Metadados

A propriedade **metadados** permite criar qualquer propriedade infantil necessária para armazenar informações relevantes. No exemplo acima, os imóveis **solicitados Por**exemplo, **aprovados,** aprovados Em **E,** e **ticketRef** contém valores de cliente para fornecer informações sobre quem solicitou a isenção, quem a aprovou e quando, e um bilhete de rastreio interno para o pedido. Estas propriedades **de metadados** são exemplos, mas não são necessárias e **os metadados** não se limitam a estas propriedades infantis.

## <a name="policy-assignment-id"></a>ID de atribuição de políticas

Este campo deve ser o nome completo de uma atribuição de política ou de uma atribuição de iniciativa.
`policyAssignmentId` é uma corda e não uma matriz. Esta propriedade define de que atribuição a hierarquia de recursos parentais ou recurso individual está _isenta._

## <a name="policy-definition-ids"></a>IDs de definição de política

Se `policyAssignmentId` for para uma atribuição de iniciativa, o `policyDefinitionReferenceIds` imóvel pode ser usado para especificar qual a definição de política(s) na iniciativa a que o recurso sujeito tem uma isenção. Como o recurso pode ser isento de uma ou mais definições de política incluídas, esta propriedade é uma _matriz_. Os valores devem corresponder aos valores da definição de iniciativa nos `policyDefinitions.policyDefinitionReferenceId` campos.

## <a name="exemption-category"></a>Categoria de isenção

Existem duas categorias de isenção e são utilizadas para agrupar isenções:

- **Atenuado**: A isenção é concedida porque a intenção política é cumprida através de outro método.
- **Renúncia**: A isenção é concedida porque o estado de incumprimento do recurso é temporariamente aceite. Outra razão para usar esta categoria é para uma hierarquia de recursos ou recursos que deve ser excluída de uma ou mais definições numa iniciativa, mas não deve ser excluída de toda a iniciativa.

## <a name="expiration"></a>Expiração

Para definir quando uma hierarquia de recursos ou um recurso individual já não está _isento_ de uma atribuição, desaprote o `expiresOn` imóvel. Esta propriedade opcional deve estar no formato Universal ISO 8601 DateTime `yyyy-MM-ddTHH:mm:ss.fffffffZ` .

> [!NOTE]
> As isenções de política não são eliminadas quando a `expiresOn` data é alcançada. O objeto é preservado para manutenção de registos, mas a isenção já não é honrada.

## <a name="required-permissions"></a>Permissões obrigatórias

As permissões Azure RBAC necessárias para gerir os objetos de isenção de política estão no `Microsoft.Authorization/policyExemptions` grupo de operação. As funções incorporadas [Contribuinte de Política](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) de Recursos e [Administração de Segurança](../../../role-based-access-control/built-in-roles.md#security-admin) têm as `read` `write` permissões e permissões e [Policy Insights Data Writer (Preview)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) tem a `read` permissão.

As isenções têm medidas de segurança adicionais devido ao impacto da concessão de uma isenção. Além de exigir a `Microsoft.Authorization/policyExemptions/write` operação na hierarquia dos recursos ou no recurso individual, o criador de uma isenção deve ter o `exempt/Action` verbo na atribuição do destino.

## <a name="next-steps"></a>Passos seguintes

- Conheça a estrutura de [definição de políticas.](./definition-structure.md)
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)