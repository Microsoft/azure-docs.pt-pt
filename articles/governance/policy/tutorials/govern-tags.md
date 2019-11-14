---
title: Gerir governação de etiquetas
description: Use o efeito modificar de Azure Policy para criar e impor um modelo de governança de marca em recursos novos e existentes.
ms.date: 11/04/2019
ms.topic: tutorial
ms.openlocfilehash: edb74bce5758ae040a6170a8e73be75fc228b001
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069664"
---
# <a name="tutorial-manage-tag-governance-with-azure-policy"></a>Tutorial: gerenciar o controle de marca com Azure Policy

As [marcas](../../../azure-resource-manager/resource-group-using-tags.md) são uma parte crucial da organização dos recursos do Azure em uma taxonomia. Ao seguir as [práticas recomendadas para o gerenciamento de marca](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources), as marcas podem ser a base para aplicar suas políticas de negócios com Azure Policy ou [controlar os custos com o gerenciamento de custos](../../../cost-management/cost-mgt-best-practices.md#organize-and-tag-your-resources).
Não importa como ou por que você usa marcas, é importante que você possa adicionar, alterar e remover rapidamente essas marcas nos recursos do Azure.

O efeito de [modificação](../concepts/effects.md#modify) de Azure Policy foi projetado para auxiliar na governança de marcas, independentemente do estágio da governança de recursos em que você está. **Modificar** ajuda quando:

- Você é novo na nuvem e não tem governança de marca
- Já tem milhares de recursos sem controle de marca
- Já tem uma taxonomia existente que você precisa alterar

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar requisitos

Como qualquer boa implementação de controles de governança, os requisitos devem vir de suas necessidades de negócios e ser bem compreendidos antes da criação de controles técnicos. Para este tutorial de cenário, os seguintes itens são nossos requisitos de negócios:

- Duas marcas obrigatórias em todos os recursos: _CostCenter_ e _env_
- _CostCenter_ deve existir em todos os contêineres e recursos individuais
  - Os recursos herdam do contêiner no qual estão, mas podem ser substituídos individualmente
- _Env_ deve existir em todos os contêineres e recursos individuais
  - Os recursos determinam ambiente por esquema de nomenclatura de contêiner e não podem ser substituídos
  - Todos os recursos em um contêiner fazem parte do mesmo ambiente

## <a name="configure-the-costcenter-tag"></a>Configurar a marca CostCenter

Em termos específicos de um ambiente do Azure gerenciado pelo Azure Policy, os requisitos de marca _CostCenter_ exigem o seguinte:

- Grupos de recursos de negação ausentes na marca _CostCenter_
- Modificar recursos para adicionar a marca _CostCenter_ do grupo de recursos pai quando ausente

### <a name="deny-resource-groups-missing-the-costcenter-tag"></a>Grupos de recursos de negação ausentes na marca CostCenter

Como o _CostCenter_ de um grupo de recursos não pode ser determinado pelo nome do grupo de recursos, ele deve ter a marca definida na solicitação para criar o grupo de recursos. A regra de política a seguir com o efeito de [negação](../concepts/effects.md#deny) impede a criação ou a atualização de grupos de recursos que não têm a marca _CostCenter_ :

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
> Como essa regra de política se destina a um grupo de recursos, o _modo_ na definição de política deve ser ' todos ' em vez de ' indexado '.

### <a name="modify-resources-to-inherit-the-costcenter-tag-when-missing"></a>Modificar recursos para herdar a marca CostCenter quando ausente

A segunda _CostCenter_ precisa ser para qualquer recurso que herde a marca do grupo de recursos pai quando ela estiver ausente. Se a marca já estiver definida no recurso, mesmo se for diferente do grupo de recursos pai, ela deverá ser mantida sozinha. A regra de política a seguir usa [Modificar](../concepts/effects.md#modify):

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

Essa regra de política usa a operação **Adicionar** em vez de **addOrReplace** , pois não queremos alterar o valor da marca se ela estiver presente ao [corrigir](../how-to/remediate-resources.md) os recursos existentes. Ele também usa a função de modelo `[resourcegroup()]` para obter o valor de marca do grupo de recursos pai.

> [!NOTE]
> Como essa regra de política tem como destino recursos que dão suporte a marcas, o _modo_ na definição de política deve ser ' indexado '. Essa configuração também garante que essa política ignore grupos de recursos.

## <a name="configure-the-env-tag"></a>Configurar a marcação env

Em termos específicos de um ambiente do Azure gerenciado pelo Azure Policy, os requisitos de marcação _env_ chamam o seguinte:

- Modificar a marca _env_ no grupo de recursos com base no esquema de nomenclatura do grupo de recursos
- Modifique a marca _env_ em todos os recursos no grupo de recursos para o mesmo que o grupo de recursos pai

### <a name="modify-resource-groups-env-tag-based-on-name"></a>Modificar a marca env dos grupos de recursos com base no nome

Uma política de [modificação](../concepts/effects.md#modify) é necessária para cada ambiente existente em seu ambiente do Azure. A política de modificação para cada uma é semelhante a esta definição de política:

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
> Como essa regra de política se destina a um grupo de recursos, o _modo_ na definição de política deve ser ' todos ' em vez de ' indexado '.

Essa política só corresponde a grupos de recursos com o esquema de nomenclatura de exemplo usado para recursos de produção de `prd-`. Um esquema de nomenclatura mais complexo pode ser obtido com várias condições de **correspondência** em vez do único, **como** neste exemplo.

### <a name="modify-resources-to-inherit-the-env-tag"></a>Modificar recursos para herdar a marca env

O requisito de negócios chama todos os recursos para ter a marca _env_ que seu grupo de recursos pai faz. Essa marca não pode ser substituída, então usaremos a operação **addOrReplace** com o efeito [Modificar](../concepts/effects.md#modify) . A política de modificação de exemplo é semelhante à seguinte regra:

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
> Como essa regra de política tem como destino recursos que dão suporte a marcas, o _modo_ na definição de política deve ser ' indexado '. Essa configuração também garante que essa política ignore grupos de recursos.

Esta regra de política procura qualquer recurso que não tenha seu valor de grupos de recursos pai para a marca _env_ ou que não tenha a marca _env_ . Os recursos correspondentes têm sua marca _env_ definida como o valor de grupos de recursos pai, mesmo que a marca já exista no recurso, mas com um valor diferente.

## <a name="assign-the-initiative-and-remediate-resources"></a>Atribuir a iniciativa e corrigir recursos

Depois que as políticas de marca acima forem criadas, junte-as a uma única iniciativa para governança de marca e atribua-as a um grupo de gerenciamento ou assinatura. A iniciativa e as políticas incluídas avaliam a conformidade dos recursos existentes e altera as solicitações de recursos novos ou atualizados que correspondem à propriedade **If** na regra de política. No entanto, a política não atualiza automaticamente os recursos existentes sem conformidade com as alterações de marca definidas.

Como as políticas de [deployIfNotExists](../concepts/effects.md#deployifnotexists) , a política de **modificação** usa tarefas de correção para alterar os recursos existentes sem conformidade. Siga as instruções em [como corrigir recursos](../how-to/remediate-resources.md) para identificar seus recursos de **modificação** sem conformidade e corrigir as marcas para sua taxonomia definida.

## <a name="review"></a>Rever

Neste tutorial, você aprendeu sobre as seguintes tarefas:

> [!div class="checklist"]
> - Identificado seus requisitos de negócios
> - Mapeado cada requisito para uma definição de política
> - Agrupadas as políticas de marca em uma iniciativa

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)
