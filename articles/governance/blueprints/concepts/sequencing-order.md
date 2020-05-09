---
title: Compreenda a ordem da sequência de implantação
description: Saiba mais sobre a ordem padrão em que os artefactos da planta são implantados durante uma atribuição de plantas e como personalizar a ordem de implementação.
ms.date: 05/06/2020
ms.topic: conceptual
ms.openlocfilehash: 91e11f8127ba2532ad48362de1689f4be2b6f935
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864526"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Compreenda a sequência de implantação em Plantas Azure

A Azure Blueprints utiliza uma ordem de **sequenciação** para determinar a ordem de criação de recursos ao processar a atribuição de uma definição de projeto. Este artigo explica os seguintes conceitos:

- A ordem de sequenciação padrão que é usada
- Como personalizar a encomenda
- Como a encomenda personalizada é processada

Existem variáveis nos exemplos json que precisa substituir com os seus próprios valores:

- `{YourMG}` - substituir pelo nome do seu grupo de gestão

## <a name="default-sequencing-order"></a>Ordem de sequenciação padrão

Se a definição de projeto não contiver nenhuma diretiva para a instalação de artefactos ou a diretiva for nula, então é utilizada a seguinte ordem:

- Artefactos de atribuição de funções de nível de **subscrição** classificados pelo nome do artefacto
- Artefactos de atribuição de políticas de nível de **subscrição** classificados pelo nome do artefacto
- Artefactos de modelo de gestor de **recursos de** nível de subscrição classificados pelo nome do artefacto
- **Artefactos** do grupo de recursos (incluindo artefactos infantis) classificados pelo nome do espaço reservado

Dentro de cada artefacto do **grupo de recursos,** é utilizada a seguinte ordem de sequência para a criação de artefactos dentro desse grupo de recursos:

- Artefactos de atribuição de **funções** de grupo de recursos classificados pelo nome do artefacto
- Artefactos de atribuição de **políticas** para crianças do grupo de recursos classificados pelo nome do artefacto
- Artefactos de modelo de gestor de recursos de grupo de recursos **azure** classificados pelo nome do artefacto

> [!NOTE]
> A utilização de [artefactos](../reference/blueprint-functions.md#artifacts) cria uma dependência implícita do artefacto a que se refere.

## <a name="customizing-the-sequencing-order"></a>Personalização da ordem de sequenciação

Ao compor grandes definições de plantas, pode ser necessário que os recursos sejam criados numa ordem específica. O padrão de utilização mais comum deste cenário é quando uma definição de planta inclui vários modelos do Gestor de Recursos Azure. As Plantas Azure lidam com este padrão permitindo definir a ordem de sequenciação.

A encomenda é realizada definindo uma `dependsOn` propriedade no JSON. A definição de planta, para grupos de recursos, e objetos de artefacto supor esta propriedade. `dependsOn`é uma série de nomes de artefactos que o artefacto em particular precisa de ser criado antes de ser criado.

> [!NOTE]
> Ao criar objetos de plantas, cada recurso de artefacto obtém o seu nome a partir do nome de ficheiro, se utilizar [powerShell](/powershell/module/az.blueprint/new-azblueprintartifact), ou o ponto final do URL, se utilizar [a API REST](/rest/api/blueprints/artifacts/createorupdate). _recursos As_ referências do grupo em artefactos devem corresponder às definidas na definição de projeto.

### <a name="example---ordered-resource-group"></a>Exemplo - grupo de recursos encomendados

Esta definição de modelo de exemplo tem um grupo de recursos que `dependsOn`definiu uma ordem de sequenciação personalizada declarando um valor para, juntamente com um grupo de recursos padrão. Neste caso, o artefacto denominado **AtribuirTags policytags** será processado antes do grupo de recursos **encomendado-rg.**
**standard-rg** será processado de acordo com a ordem de sequenciação padrão.

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

### <a name="example---artifact-with-custom-order"></a>Exemplo - artefacto com encomenda personalizada

Este exemplo é um artefacto político que depende de um modelo de Gestor de Recursos Azure. Por ordem padrão, um artefacto de política seria criado antes do modelo do Gestor de Recursos Azure. Esta encomenda permite que o artefacto político aguarde a criação do modelo do Gestor de Recursos Azure.

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

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Exemplo - artefacto de modelo de nível de subscrição dependendo de um grupo de recursos

Este exemplo é para um modelo de Gestor de Recursos implantado ao nível de subscrição para depender de um grupo de recursos. Na encomenda predefinida, os artefactos de nível de subscrição seriam criados antes de quaisquer grupos de recursos e artefactos infantis nesses grupos de recursos. O grupo de recursos é definido na definição de planta como esta:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

O artefacto do modelo de nível de subscrição, dependendo do grupo de recursos **de espera para mim,** é definido assim:

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

Durante o processo de criação, um tipo topológico é usado para criar o gráfico de dependência dos artefactos das plantas. O controlo assegura que cada nível de dependência entre grupos de recursos e artefactos é suportado.

Se uma dependência de artefactos for declarada que não alteraria a ordem padrão, então não se faz qualquer alteração.
Um exemplo é um grupo de recursos que depende de uma política de nível de subscrição. Outro exemplo é uma atribuição de política infantil "standard-rg" do grupo de recursos que depende da atribuição de funções para crianças do grupo de recursos "standard-rg". Em ambos os `dependsOn` casos, não teria alterado a ordem de sequenciação padrão e não seriam feitas alterações.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [ciclo de vida do esquema](lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
- Resolva problemas durante a atribuição de um esquema com a [resolução de problemas gerais](../troubleshoot/general.md).