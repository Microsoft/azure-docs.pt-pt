---
title: 'Tutorial: Gerir a governação da tag'
description: Neste tutorial, você usa o efeito Modificar da Política Azure para criar e impor um modelo de governação de tags em recursos novos e existentes.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: 9efeb27151cd3a32741f1bdb6d1d90d3304c5874
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876282"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Tutorial: Gerir a governação com a Política Azure

[As etiquetas](../../../azure-resource-manager/management/tag-resources.md) são uma parte crucial da organização dos seus recursos Azure numa taxonomia. Ao seguir as [melhores práticas de gestão de etiquetas,](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)as etiquetas podem ser a base para aplicar as suas políticas de negócio com a Azure Policy ou [monitorizar custos com a Gestão de Custos.](../../../cost-management-billing/costs/cost-mgt-best-practices.md#tag-shared-resources)
Não importa como ou por que usa tags, é importante que possa adicionar, alterar e remover essas etiquetas nos seus recursos Azure. Para ver se o seu recurso Azure suporta a marcação, consulte [o suporte da Tag](../../../azure-resource-manager/management/tag-support.md).

O efeito [modificação](../concepts/effects.md#modify) da Política Azure destina-se a ajudar na governação das etiquetas, independentemente da fase da governação dos recursos em que se encontra. **Modificar** ajuda quando:

- És novo na nuvem e não tens uma governação de etiquetas.
- Já tem milhares de recursos sem governação
- Já tem uma taxonomia existente que precisa mudar

Neste tutorial, completará as seguintes tarefas:

> [!div class="checklist"]
> - Identifique os seus requisitos de negócio
> - Mapear cada requisito para uma definição de política
> - Agrupar as políticas de etiquetas numa iniciativa

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar requisitos

Como qualquer boa implementação dos controlos de governação, os requisitos devem vir das necessidades do seu negócio e ser bem compreendidos antes de criar controlos técnicos. Para este cenário tutorial, os seguintes itens são os nossos requisitos de negócio:

- Duas etiquetas necessárias em todos os recursos: _CostCenter_ e _Env_
- _CostCenter_ deve existir em todos os contentores e recursos individuais
  - Os recursos herdam do contentor em que estão, mas podem ser individualmente ultrapassados.
- _Env_ deve existir em todos os contentores e recursos individuais
  - Os recursos determinam o ambiente através de um esquema de nomeação de contentores e não podem ser ultrapassados
  - Todos os recursos num contentor fazem parte do mesmo ambiente

## <a name="configure-the-costcenter-tag"></a>Configure a etiqueta CostCenter

Em termos específicos de um ambiente Azure gerido pela Azure Policy, os requisitos de etiquetas _CostCenter_ exigem os seguintes resultados:

- Negar grupos de recursos que faltem à etiqueta _CostCenter_
- Modifique os recursos para adicionar a etiqueta _CostCenter_ do grupo de recursos-mãe quando faltar

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Negar grupos de recursos que faltem à etiqueta CostCenter

Uma vez que o _CostCenter_ para um grupo de recursos não pode ser determinado pelo nome do grupo de recursos, deve ter a etiqueta definida no pedido de criação do grupo de recursos. A seguinte regra política com o efeito [Deny](../concepts/effects.md#deny) impede a criação ou atualização de grupos de recursos que não têm a etiqueta _CostCenter:_

```json
"if": {
    "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "tags['CostCenter']",
            "exists": false
        }
    ]
},
"then": {
    "effect": "deny"
}
```

> [!NOTE]
> Como esta regra política visa um grupo de recursos, o _modo_ na definição de política deve ser "All" em vez de "Indexado".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modifique recursos para herdar a etiqueta CostCenter quando faltar

A segunda necessidade do _CostCenter_ é que quaisquer recursos herdem a etiqueta do grupo de recursos dos pais quando falta. Se a etiqueta já estiver definida no recurso, mesmo que diferente do grupo de recursos dos pais, deve ser deixada em paz. A seguinte regra de política utiliza [Modificação:](../concepts/effects.md#modify)

```json
"policyRule": {
    "if": {
        "field": "tags['CostCenter']",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "add",
                "field": "tags['CostCenter']",
                "value": "[resourcegroup().tags['CostCenter']]"
            }]
        }
    }
}
```

Esta regra de política usa a operação **de adição** em vez de **addOrReplace,** uma vez que não queremos alterar o valor da etiqueta se estiver presente na [remediação](../how-to/remediate-resources.md) dos recursos existentes. Também usa a `[resourcegroup()]` função de modelo para obter o valor da etiqueta do grupo de recursos dos pais.

> [!NOTE]
> Uma vez que esta regra política visa recursos que suportam etiquetas, o _modo_ na definição de política deve ser "Indexado". Esta configuração também garante que esta política ignora grupos de recursos.

## <a name="configure-the-env-tag"></a>Configure a etiqueta Env

Em termos específicos de um ambiente Azure gerido pela Azure Policy, os requisitos da etiqueta _Env_ exigem os seguintes resultados:

- Modifique a etiqueta _Env_ no grupo de recursos com base no esquema de nomeação do grupo de recursos
- Modifique a etiqueta _Env_ em todos os recursos do grupo de recursos para o mesmo que o grupo de recursos-mãe

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modificar grupos de recursos Env tag com base no nome

É necessária uma política [de modificação](../concepts/effects.md#modify) para cada ambiente que exista no seu ambiente Azure. A política de modificação para cada um se parece com esta definição de política:

```json
"policyRule": {
    "if": {
        "allOf": [{
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        {
            "field": "name",
            "like": "prd-*"
        },
        {
            "field": "tags['Env']",
            "notEquals": "Production"
        }

    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "Production"
            }]
        }
    }
}
```

> [!NOTE]
> Como esta regra política visa um grupo de recursos, o _modo_ na definição de política deve ser "All" em vez de "Indexado".

Esta política apenas corresponde aos grupos de recursos com o sistema de nomeação de amostras utilizado para os recursos de produção de `prd-` . Esquemas de nomeação mais complexos podem ser alcançados com várias condições de **jogo** em vez de um único **como** neste exemplo.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modificar recursos para herdar a etiqueta Env

A exigência do negócio exige que todos os recursos tenham a etiqueta _Env_ que o seu grupo de recursos-mãe faz. Esta etiqueta não pode ser ultrapassada, por isso vamos usar a operação **do addOrReplace** com o efeito [Modificar.](../concepts/effects.md#modify) A política de modificação da amostra parece a seguinte regra:

```json
"policyRule": {
    "if": {
        "anyOf": [{
            "field": "tags['Env']",
            "notEquals": "[resourcegroup().tags['Env']]"
        },
        {
            "field": "tags['Env']",
            "exists": false
        }
    ]
    },
    "then": {
        "effect": "modify",
        "details": {
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ],
            "operations": [{
                "operation": "addOrReplace",
                "field": "tags['Env']",
                "value": "[resourcegroup().tags['Env']]"
            }]
        }
    }
}
```

> [!NOTE]
> Uma vez que esta regra política visa recursos que suportam etiquetas, o _modo_ na definição de política deve ser "Indexado". Esta configuração também garante que esta política ignora grupos de recursos.

Esta regra de política procura qualquer recurso que não tenha o valor dos seus grupos de recursos-mãe para a etiqueta _Env_ ou que esteja a perder a etiqueta _Env._ Os recursos correspondentes têm a sua etiqueta _Env_ definida para o valor dos grupos de recursos-mãe, mesmo que a etiqueta já existisse no recurso mas com um valor diferente.

## <a name="assign-the-initiative-and-remediate-resources"></a>Atribuir a iniciativa e remediar recursos

Assim que as políticas de etiqueta acima forem criadas, junte-as numa única iniciativa para a governação do tag e atribua-as a um grupo de gestão ou subscrição. A iniciativa e as políticas incluídas avaliam então o cumprimento dos recursos existentes e altera os pedidos de recursos novos ou atualizados que **correspondam** ao se imóvel na regra política. No entanto, a política não atualiza automaticamente os recursos existentes não conformes com as alterações de etiqueta definidas.

Tal como implementar as [políticasifNotExists,](../concepts/effects.md#deployifnotexists) a política **de modificação** utiliza tarefas de reparação para alterar os recursos existentes não conformes. Siga as instruções sobre [como remediar recursos](../how-to/remediate-resources.md) para identificar os seus recursos de **modificação** não conformes e corrigir as etiquetas para a sua taxonomia definida.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já acabou de trabalhar com recursos deste tutorial, use os seguintes passos para eliminar qualquer uma das atribuições ou definições acima criadas:

1. Selecione **Definições** (ou **Atribuições** se estiver a tentar apagar uma atribuição) em **Autoria** no lado esquerdo da página Política Azure.

1. Procure a nova definição de iniciativa ou de política (ou atribuição) que acabou de remover.

1. Clique com o botão direito do rato na linha ou selecione as reticências no fim da definição (ou atribuição) e selecione **Eliminar definição** (ou **Eliminar atribuição**).

## <a name="review"></a>Revisão

Neste tutorial, aprendeu sobre as seguintes tarefas:

> [!div class="checklist"]
> - Identificou os seus requisitos de negócio
> - Mapeado cada requisito para uma definição de política
> - Agrupou as políticas de etiquetas numa iniciativa

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)