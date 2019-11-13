---
title: Entender a ordem de sequência de implantação
description: Saiba mais sobre o ciclo de vida que uma definição do plano gráfico passa e detalhes sobre cada estágio.
ms.date: 08/22/2019
ms.topic: conceptual
ms.openlocfilehash: 87eff48f977f0308fea563090a44ff0b301909a9
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960441"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Entender a sequência de implantação em plantas do Azure

Os planos gráficos do Azure usam uma **ordem de sequenciamento** para determinar a ordem de criação de recursos ao processar a atribuição de uma definição de plano gráfico. Este artigo explica os seguintes conceitos:

- A ordem de sequenciamento padrão usada
- Como personalizar o pedido
- Como a ordem personalizada é processada

Há variáveis nos exemplos JSON que você precisa substituir pelos seus próprios valores:

- `{YourMG}` - substituir pelo nome do seu grupo de gestão

## <a name="default-sequencing-order"></a>Ordem de sequenciamento padrão

Se a definição de Blueprint não contiver nenhuma diretiva para a ordem de implantação de artefatos ou se a diretiva for nula, a seguinte ordem será usada:

- Artefatos de **atribuição de função** de nível de assinatura classificados por nome de artefato
- Artefatos de **atribuição de política** de nível de assinatura classificados pelo nome do artefato
- Nível de assinatura Azure Resource Manager artefatos de **modelo** classificados por nome de artefato
- Artefatos do **grupo de recursos** (incluindo artefatos filho) classificados por nome do espaço reservado

Dentro de cada artefato do **grupo de recursos** , a ordem de sequência a seguir é usada para que os artefatos sejam criados dentro desse grupo de recursos:

- Artefatos de **atribuição de função** filho do grupo de recursos classificados pelo nome do artefato
- Artefatos de **atribuição de política** filho do grupo de recursos classificados pelo nome do artefato
- Artefatos do **modelo de Azure Resource Manager** filho do grupo de recursos classificados pelo nome do artefato

> [!NOTE]
> O uso de [artefatos ()](../reference/blueprint-functions.md#artifacts) cria uma dependência implícita no artefato que está sendo referenciado.

## <a name="customizing-the-sequencing-order"></a>Personalizando a ordem de sequenciamento

Ao compor grandes definições de Blueprint, pode ser necessário que os recursos sejam criados em uma ordem específica. O padrão de uso mais comum desse cenário é quando uma definição de Blueprint inclui vários modelos de Azure Resource Manager. Os planos gráficos manipulam esse padrão, permitindo que a ordem de sequenciamento seja definida.

A ordenação é realizada definindo uma propriedade `dependsOn` no JSON. A definição do Blueprint, para grupos de recursos e objetos de artefato dão suporte a essa propriedade. `dependsOn` é uma matriz de cadeia de caracteres de nomes de artefatos que o artefato específico precisa ser criado antes de ser criado.

> [!NOTE]
> Ao criar objetos BluePrints, cada recurso de artefato obtém seu nome do nome de arquivo, se estiver usando o [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)ou o ponto de extremidade de URL, se estiver usando a [API REST](/rest/api/blueprints/artifacts/createorupdate).
> as referências de _resourcegroup_ em artefatos devem corresponder àquelas definidas na definição do Blueprint.

### <a name="example---ordered-resource-group"></a>Exemplo-grupo de recursos ordenados

Este exemplo de definição de Blueprint tem um grupo de recursos que definiu uma ordem de sequenciamento personalizada declarando um valor para `dependsOn`, juntamente com um grupo de recursos padrão. Nesse caso, o artefato chamado **assignPolicyTags** será processado antes do grupo de recursos **ordered-RG** .
**Standard-RG** será processado de acordo com a ordem de sequenciamento padrão.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Exemplo – artefato com ordem personalizada

Este exemplo é um artefato de política que depende de um modelo de Azure Resource Manager. Por padrão, a ordenação de um artefato de política seria criado antes do modelo de Azure Resource Manager. Essa ordenação permite que o artefato da política aguarde até que o modelo de Azure Resource Manager seja criado.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemplo-artefato de modelo de nível de assinatura dependendo de um grupo de recursos

Este exemplo é para um modelo do Resource Manager implantado no nível de assinatura para depender de um grupo de recursos. Na ordenação padrão, os artefatos de nível de assinatura seriam criados antes de qualquer grupo de recursos e artefatos filho nesses grupos de recursos. O grupo de recursos é definido na definição do Blueprint da seguinte maneira:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

O artefato do modelo de nível de assinatura, dependendo do grupo de recursos **Wait-for-me,** é definido da seguinte maneira:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Processando a sequência personalizada

Durante o processo de criação, uma classificação topológica é usada para criar o grafo de dependência dos artefatos de plantas. A verificação verifica se há suporte para cada nível de dependência entre grupos de recursos e artefatos.

Se uma dependência de artefato for declarada que não alteraria a ordem padrão, nenhuma alteração será feita. Um exemplo é um grupo de recursos que depende de uma política de nível de assinatura. Outro exemplo é uma atribuição de política filho ' Standard-RG ' do grupo de recursos que depende da atribuição de função filho ' Standard-RG ' do grupo de recursos. Em ambos os casos, o `dependsOn` não teria alterado a ordem de sequenciamento padrão e nenhuma alteração seria feita.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).