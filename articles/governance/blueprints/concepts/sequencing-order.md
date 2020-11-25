---
title: Compreenda a ordem de sequência de implantação
description: Saiba mais sobre a ordem padrão em que os artefactos de planta são implantados durante uma atribuição de planta e como personalizar a ordem de implementação.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 8305e5d44caef0f35e5b4beb4b70be9736272fa7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996054"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Compreenda a sequência de implantação em Azure Blueprints

A Azure Blueprints usa uma **ordem de sequenciação** para determinar a ordem de criação de recursos ao processar a atribuição de uma definição de planta. Este artigo explica os seguintes conceitos:

- A ordem de sequenciação padrão que é usada
- Como personalizar a encomenda
- Como a encomenda personalizada é processada

Existem variáveis nos exemplos JSON que precisa de substituir pelos seus próprios valores:

- `{YourMG}` - substituir pelo nome do seu grupo de gestão

## <a name="default-sequencing-order"></a>Ordem de sequenciação predefinida

Se a definição de projeto não contiver nenhuma diretiva para a ordem de implantação de artefactos ou a diretiva for nula, então é utilizada a seguinte ordem:

- Artefactos de atribuição de **funções** de nível de subscrição classificados pelo nome de artefacto
- Artefactos **de atribuição de política** de nível de subscrição classificados pelo nome de artefacto
- Modelo de **gestor de recursos Azure de** nível de subscrição (modelos ARM) classificados pelo nome de artefacto
- **Artefactos de grupo** de recursos (incluindo artefactos infantis) classificados pelo nome de espaço reservado

Dentro de cada artefacto **de grupo de recursos,** é utilizada a seguinte ordem de sequência para a criação de artefactos dentro desse grupo de recursos:

- Artefactos de **atribuição de funções** de criança de grupo de recursos classificados por nome de artefacto
- Artefactos **de atribuição de política** infantil de grupo de recursos classificados pelo nome de artefacto
- Artefactos do **gestor de recursos** da criança de grupo de recursos de recursos de recursos (modelos ARM) classificados pelo nome do artefacto

> [!NOTE]
> A utilização de [artefactos()](../reference/blueprint-functions.md#artifacts) cria uma dependência implícita do artefacto a que se refere.

## <a name="customizing-the-sequencing-order"></a>Personalização da ordem de sequenciação

Ao compor grandes definições de plantas, pode ser necessário que os recursos sejam criados numa ordem específica. O padrão de uso mais comum deste cenário é quando uma definição de planta inclui vários modelos ARM. A Azure Blueprints lida com este padrão permitindo definir a ordem de sequenciação.

O pedido é realizado definindo um `dependsOn` imóvel no JSON. A definição de planta, para grupos de recursos, e objetos de artefactos suportam esta propriedade. `dependsOn` é uma cadeia de nomes de artefactos que o artefacto particular precisa de ser criado antes de ser criado.

> [!NOTE]
> Ao criar objetos de planta, cada recurso de artefacto obtém o seu nome a partir do nome de ficheiro, se utilizar [o PowerShell,](/powershell/module/az.blueprint/new-azblueprintartifact)ou o ponto final URL, se utilizar [a API REST](/rest/api/blueprints/artifacts/createorupdate). _as_ referências do grupo de recursos em artefactos devem corresponder às definidas na definição de planta.

### <a name="example---ordered-resource-group"></a>Exemplo - grupo de recursos ordenado

Esta definição de modelo de exemplo tem um grupo de recursos que definiu uma ordem de sequenciação personalizada declarando um valor para `dependsOn` , juntamente com um grupo de recursos padrão. Neste caso, o artefacto nomeado **DesignPolicyTags** será processado antes do grupo de recursos **ordenados.**
**standard-rg** será processado por ordem de sequenciação padrão.

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

### <a name="example---artifact-with-custom-order"></a>Exemplo - artefacto com ordem personalizada

Este exemplo é um artefacto de política que depende de um modelo ARM. Por encomenda por defeito, um artefacto de política seria criado antes do modelo ARM. Esta encomenda permite que o artefacto da política aguarde a criação do modelo ARM.

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

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemplo - artefacto do modelo de nível de subscrição dependendo de um grupo de recursos

Este exemplo é para um modelo ARM implantado ao nível de subscrição para depender de um grupo de recursos. Na encomenda por defeito, os artefactos de nível de subscrição seriam criados antes de quaisquer grupos de recursos e artefactos infantis nesses grupos de recursos. O grupo de recursos é definido na definição de planta como esta:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

O artefacto do modelo de nível de subscrição dependendo do grupo de recursos **de espera por mim** é definido assim:

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

## <a name="processing-the-customized-sequence"></a>Processamento da sequência personalizada

Durante o processo de criação, um tipo topológico é usado para criar o gráfico de dependência dos artefactos das plantas. A verificação garante que cada nível de dependência entre grupos de recursos e artefactos é suportado.

Se uma dependência de artefactos for declarada que não alteraria a ordem padrão, então não é feita nenhuma alteração.
Um exemplo é um grupo de recursos que depende de uma política de nível de subscrição. Outro exemplo é uma atribuição de política infantil 'standard-rg' do grupo de recursos que depende da atribuição de funções de crianças do grupo de recursos 'standard-rg'. Em ambos os casos, a `dependsOn` ordem de sequenciação por defeito não teria sido feita.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](./lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](./parameters.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](./resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).