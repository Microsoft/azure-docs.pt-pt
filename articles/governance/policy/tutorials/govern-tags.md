---
title: 'Tutorial: Gerir a governação da etiqueta'
description: Neste tutorial, utiliza-se o efeito Modificar da Política Azure para criar e impor um modelo de governação de etiquetas sobre recursos novos e existentes.
ms.date: 04/21/2020
ms.topic: tutorial
ms.openlocfilehash: 6319bbde2fdc8f78e2743dd5f1565c8680433fea
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759061"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Tutorial: Gerir a governação de etiquetas com a Política Azure

[As etiquetas](../../../azure-resource-manager/management/tag-resources.md) são uma parte crucial da organização dos seus recursos Azure numa taxonomia. Ao seguir [as melhores práticas de gestão de etiquetas,](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)as etiquetas podem ser a base para aplicar as suas políticas de negócio com a Política Azure ou [acompanhar os custos com](../../../cost-management-billing/costs/cost-mgt-best-practices.md#organize-and-tag-your-resources)a Cost Management.
Não importa como ou por que usar etiquetas, é importante que possa adicionar, alterar e remover rapidamente essas etiquetas nos seus recursos Azure. Para ver se o seu recurso Azure suporta a marcação, consulte [o suporte da Tag](../../../azure-resource-manager/management/tag-support.md).

O efeito Modificado da Política Azure [destina-se](../concepts/effects.md#modify) a ajudar na governação das etiquetas, independentemente da fase da governação dos recursos em que se encontra. **Modificar** ajuda quando:

- Énovo na nuvem e não tem governação de etiquetas.
- Já temos milhares de recursos sem governação de etiquetas
- Já tem uma taxonomia existente que precisa de ser alterada

Neste tutorial, completará as seguintes tarefas:

> [!div class="checklist"]
> - Identifique os seus requisitos de negócio
> - Mapear cada requisito para uma definição de política
> - Agrupar as políticas de etiquetas numa iniciativa

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar requisitos

Como qualquer boa implementação dos controlos de governação, os requisitos devem vir das suas necessidades de negócio e ser bem compreendidos antes de criar controlos técnicos. Para este cenário tutorial, os seguintes itens são os nossos requisitos de negócio:

- Duas etiquetas necessárias em todos os recursos: _CostCenter_ e _Env_
- _CostCenter_ deve existir em todos os recipientes e recursos individuais
  - Recursos herdados do contentor em que estão, mas podem ser individualmente ultrapassados
- _Env_ deve existir em todos os recipientes e recursos individuais
  - Os recursos determinam o ambiente através do sistema de nomeação de contentores e não podem ser ultrapassados
  - Todos os recursos num contentor fazem parte do mesmo ambiente

## <a name="configure-the-costcenter-tag"></a>Configure a etiqueta CostCenter

Em termos específicos de um ambiente Azure gerido pela Política Azure, os requisitos de etiqueta _costCenter_ exigem o seguinte:

- Negar grupos de recursos que faltam à etiqueta _CostCenter_
- Modifique os recursos para adicionar a etiqueta _CostCenter_ do grupo de recursos parentais quando falta

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Negar grupos de recursos que faltam à etiqueta CostCenter

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
> Dado que esta regra de política visa um grupo de recursos, o _modo_ de definição de política deve ser "All" em vez de "Indexado".

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modificar recursos para herdar a etiqueta CostCenter quando faltar

A segunda necessidade do _CostCenter_ é que quaisquer recursos herdem a etiqueta do grupo de recursos-mãe quando está desaparecida. Se a etiqueta já estiver definida no recurso, mesmo que diferente do grupo de recursos-mãe, deve ser deixada em paz. A seguinte regra de política utiliza [Modificar:](../concepts/effects.md#modify)

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

Esta regra de política utiliza a operação **addOrReplace,** uma vez que não queremos alterar o valor da etiqueta se estiver presente na [reparação](../how-to/remediate-resources.md) dos recursos existentes. **add** Também usa `[resourcegroup()]` a função do modelo para obter o valor da etiqueta do grupo de recursos parentais.

> [!NOTE]
> Uma vez que esta regra de política visa os recursos que suportam as etiquetas, o _modo_ de definição de política deve ser "Indexado". Esta configuração também garante que esta política ignora os grupos de recursos.

## <a name="configure-the-env-tag"></a>Configure a etiqueta Env

Em termos específicos de um ambiente Azure gerido pela Política Azure, os requisitos da etiqueta _Env_ exigem o seguinte:

- Modificar a etiqueta _Env_ no grupo de recursos com base no esquema de nomeação do grupo de recursos
- Modificar a etiqueta _Env_ em todos os recursos do grupo de recursos para o mesmo que o grupo de recursos-mãe

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modificar grupos de recursos Etiqueta Env com base no nome

É necessária uma política [de modificação](../concepts/effects.md#modify) para cada ambiente que exista no seu ambiente Azure. A política de modificar para cada um se parece com esta definição de política:

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
> Dado que esta regra de política visa um grupo de recursos, o _modo_ de definição de política deve ser "All" em vez de "Indexado".

Esta política apenas corresponde aos grupos de `prd-`recursos com o sistema de nomeação da amostra utilizado para os recursos de produção de . Esquemas de nomeação mais complexos podem ser alcançados com várias condições de **correspondência** em vez de um único **como** neste exemplo.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modificar recursos para herdar a etiqueta Env

O requisito do negócio exige que todos os recursos tenham a etiqueta _Env_ que o seu grupo de recursos-mãe faz. Esta etiqueta não pode ser sobremontada, por isso vamos usar a operação **addOrReplace** com o efeito [Modificar.](../concepts/effects.md#modify) A política de modificação da amostra parece a seguinte regra:

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
> Uma vez que esta regra de política visa os recursos que suportam as etiquetas, o _modo_ de definição de política deve ser "Indexado". Esta configuração também garante que esta política ignora os grupos de recursos.

Esta regra de política procura qualquer recurso que não tenha o valor dos seus grupos de recursos-mãe para a etiqueta _Env_ ou que esteja a perder a etiqueta _Env._ Os recursos correspondentes têm a sua etiqueta _Env_ definida para o valor dos grupos de recursos-mãe, mesmo que a etiqueta já existisse no recurso, mas com um valor diferente.

## <a name="assign-the-initiative-and-remediate-resources"></a>Atribuir a iniciativa e remediar recursos

Uma vez criadas as políticas de etiquetaacima, junte-se a eles numa única iniciativa de governação de etiquetas e atribua-as a um grupo de gestão ou subscrição. A iniciativa e as políticas incluídas avaliam então o cumprimento dos recursos existentes e altera os pedidos de recursos novos ou atualizados que correspondam ao **se** imóvel na regra política. No entanto, a política não atualiza automaticamente os recursos não conformes existentes com as alterações de etiquetadefinidas.

Tal como as políticas [do IfNotExists,](../concepts/effects.md#deployifnotexists) a política **Modificar** utiliza tarefas de reparação para alterar os recursos não conformes existentes. Siga as instruções sobre [como remediar os recursos](../how-to/remediate-resources.md) para identificar os seus recursos **modificadores** não conformes e corrigir as etiquetas à sua taxonomia definida.

## <a name="clean-up-resources"></a>Limpar recursos

Se terminar de trabalhar com recursos deste tutorial, use os seguintes passos para eliminar qualquer uma das atribuições ou definições acima criadas:

1. Selecione **Definições** (ou **Atribuições** se estiver a tentar eliminar uma atribuição) ao abrigo da **Autoria** no lado esquerdo da página Política Azure.

1. Procure a nova definição de iniciativa ou de política (ou atribuição) que acabou de remover.

1. Clique com o botão direito do rato na linha ou selecione as reticências no fim da definição (ou atribuição) e selecione **Eliminar definição** (ou **Eliminar atribuição**).

## <a name="review"></a>Rever

Neste tutorial, aprendeu sobre as seguintes tarefas:

> [!div class="checklist"]
> - Identificou os seus requisitos de negócio
> - Mapeou cada exigência para uma definição de política
> - Agrupara as políticas de etiquetas numa iniciativa

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)