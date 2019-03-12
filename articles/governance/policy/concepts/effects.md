---
title: Compreender como funcionam os efeitos
description: Definição de política do Azure tem vários efeitos que determinam como conformidade é gerenciada e comunicada.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6c6fbde8ff803a053f8c34765ce95d3981a57c52
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551268"
---
# <a name="understand-azure-policy-effects"></a>Compreenda os efeitos de política do Azure

Cada definição de política na política do Azure tem um efeito único. Esse efeito determina o que acontece quando a regra de política é avaliada para corresponder. Os efeitos ter um comportamento diferente se forem para um novo recurso, um recurso atualizado ou um recurso existente.

Atualmente, existem seis efeitos que são suportados numa definição de política:

- Acrescentar
- Auditoria
- AuditIfNotExists
- Negar
- DeployIfNotExists
- Desativado

## <a name="order-of-evaluation"></a>Ordem de avaliação

Pedidos para criar ou atualizar um recurso através do Gestor de recursos do Azure são avaliados primeiro pela política. Política cria uma lista de todas as atribuições de que se aplicam ao recurso e, em seguida, avalia o recurso em relação a cada definição. Política processa vários dos efeitos antes de entregar o pedido para o fornecedor de recursos apropriado. Se o fizer, impede o processamento desnecessária por um fornecedor de recursos quando um recurso não cumpre os controlos de governação projetado de política.

- **Desativado** será verificado primeiro para determinar se a regra de política deve ser avaliada.
- **Acrescentar** , em seguida, é avaliada. Acrescentar uma vez que pode alterar o pedido, uma alteração efetuada pelo acréscimo pode impedir que uma auditoria ou negar o efeito de acionar.
- **Negar** , em seguida, é avaliada. Avaliando negar antes de auditoria, registo duplo de um recurso indesejado é evitado.
- **Auditoria** , em seguida, é avaliado antes do pedido vai para o fornecedor de recursos.

Depois do fornecedor de recursos devolve um código de sucesso **AuditIfNotExists** e **DeployIfNotExists** avaliar para determinar se o registo de conformidade adicionais ou ação é necessária.

## <a name="disabled"></a>Desativado

Esse efeito é útil para situações de teste ou para quando a definição de política tem parametrizado o efeito. Esta flexibilidade torna possível desativar uma atribuição de única em vez de desativar todas as atribuições dessa política.

## <a name="append"></a>Acrescentar

Acrescentar é usado para adicionar campos adicionais para o recurso solicitado durante a criação ou atualização. Um exemplo comum é adicionar etiquetas nos recursos, como costCenter ou permitido especificar IPs para um recurso de armazenamento.

### <a name="append-evaluation"></a>Acrescentar a avaliação

Acrescentar avalia antes do pedido é processado por um fornecedor de recursos durante a criação ou atualização de um recurso. Acrescentar adiciona campos ao recurso quando o **se** é cumprida a condição de regra de política. Se o efeito de acréscimo poderia substituir um valor no pedido original com um valor diferente, em seguida, ele atua como um efeito de recusa e rejeita o pedido. Para acrescentar um novo valor a uma matriz existente, utilize o **[\*]** versão de alias.

Quando uma definição de política com o efeito de acréscimo é executada como parte de um ciclo de avaliação, não faz as alterações aos recursos já existentes. Em vez disso, ele marca a qualquer recurso que cumpra os **se** condição como não conforme.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito de acréscimo apenas tem um **detalhes** matriz, o que é necessário. Como **detalhes** é uma matriz, pode demorar um único **campo/valor** par ou múltiplos. Consulte a [estrutura de definição](definition-structure.md#fields) para obter a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de acréscimo

Exemplo 1: Único **campo/valor** par para acrescentar uma etiqueta.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exemplo 2: Dois **campo/valor** pares para acrescentar um conjunto de marcas.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Exemplo 3: Único **valor do campo** emparelhe com um não -**[\*]**
[alias](definition-structure.md#aliases) com uma matriz **valor** para definir regras IP num conta de armazenamento. Quando o não -**[\*]** alias é uma matriz, o efeito acrescenta o **valor** como a matriz de inteira. Se a matriz de já existir, ocorre um evento de negação do conflito.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Exemplo 4: Único **valor do campo** emparelhe com um **[\*]** [alias](definition-structure.md#aliases) com uma matriz **valor** para definir regras IP numa conta de armazenamento. Ao utilizar o **[\*]** acrescenta o efeito de alias, o **valor** a uma matriz de potencialmente já existente. Se a matriz não ainda existe, será criada.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="deny"></a>Negar

Negar serve para impedir que um pedido de recurso que não corresponde aos padrões definidos por meio de uma definição de política e o pedido de falha.

### <a name="deny-evaluation"></a>Avaliação de negação

Quando criar ou atualizar um recurso correspondente, negar impede que o pedido antes de serem enviados para o fornecedor de recursos. O pedido é retornado como um `403 (Forbidden)`. No portal, o proibido pode ser visualizado como um Estado de implementação que foi impedida pela atribuição de política.

Durante a avaliação dos recursos existentes, os recursos que correspondam a uma definição de política de negação são marcados como não conforme.

### <a name="deny-properties"></a>Propriedades de negação

O efeito de recusa não tem as propriedades adicionais para utilização no **, em seguida,** condição para a definição de política.

### <a name="deny-example"></a>Exemplo de negação

Exemplo: Usando o efeito de recusa.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditoria

Auditoria é utilizada para criar um evento de aviso no registo de atividades, ao avaliar a um recurso não compatível, mas ele não para o pedido.

### <a name="audit-evaluation"></a>Avaliação de auditoria

Auditoria é o efeito de última verificado pela política durante a criação ou atualização de um recurso. Política, em seguida, envia o recurso para o fornecedor de recursos. Auditoria funciona da mesma para um pedido de recurso e um ciclo de avaliação. A política de adiciona um `Microsoft.Authorization/policies/audit/action` operação ao registo de atividades e marca o recurso como não conforme.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito de auditoria não tem as propriedades adicionais para utilização no **, em seguida,** condição para a definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: Usando o efeito de auditoria.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists permite a auditoria em recursos que correspondam a **se** condição, mas não tem os componentes especificados no **detalhes** do **, em seguida,** condição.

### <a name="auditifnotexists-evaluation"></a>Avaliação de AuditIfNotExists

AuditIfNotExists é executado depois de um fornecedor de recursos tratou de um pedido de recurso de criar ou atualizar e devolveu um código de estado de êxito. A auditoria ocorre se existirem não existem recursos relacionados a ou se os recursos definidos pelo **ExistenceCondition** não avaliadas como verdadeiras. A política de adiciona um `Microsoft.Authorization/policies/audit/action` operação para a atividade iniciar da mesma forma como o efeito de auditoria. Quando acionado, o recurso que satisfeitos os **se** condição é o recurso que está marcado como não conforme.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

O **detalhes** propriedade dos efeitos AuditIfNotExists tem todos os subproperties que definem os recursos relacionados para corresponder.

- **Tipo de** [necessário]
  - Especifica o tipo de recurso relacionado para corresponder.
  - Começa ao tentar obter um recurso por baixo da **se** recursos de condição, em seguida, consultas dentro do mesmo grupo de recursos como o **se** recursos de condição.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para corresponder e faz com que a política para obter um recurso específico em vez de todos os recursos do tipo especificado.
- **ResourceGroupName** (opcional)
  - Permite a correspondência do recurso relacionado provenientes de um grupo de recursos diferente.
  - Não se aplica se **tipo** é um recurso que seria por baixo da **se** recursos de condição.
  - A predefinição é o **se** grupo de recursos do recurso de condição.
- **ExistenceScope** (opcional)
  - Valores permitidos são _subscrição_ e _ResourceGroup_.
  - Define o âmbito de como obter o recurso relacionado para corresponder a partir de.
  - Não se aplica se **tipo** é um recurso que seria por baixo da **se** recursos de condição.
  - Para _ResourceGroup_, seria o limite para o **se** grupo de recursos do recurso de condição ou o grupo de recursos especificado na **ResourceGroupName**.
  - Para _subscrição_, consulta da subscrição completa para os recursos relacionados.
  - A predefinição é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, quaisquer relacionadas com recursos de **tipo** satisfaça o efeito e não aciona a auditoria.
  - Utiliza o mesmo idioma como a regra de política para o **se** de condição, mas é avaliado relativamente a cada recurso relacionado individualmente.
  - Se todos os recursos relacionados correspondente for avaliada como true, o efeito é satisfeito e não aciona a auditoria.
  - Pode utilizar [field()] para verificar a equivalência com valores no **se** condição.
  - Por exemplo, poderia ser usado para validar que o recurso principal (no **se** condição) está na mesma localização de recursos que o recurso relacionado correspondente.

### <a name="auditifnotexists-example"></a>Exemplo de AuditIfNotExists

Exemplo: Avalia as máquinas virtuais para determinar se a extensão de Antimalware existe, em seguida, as auditorias quando em falta.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Assim como AuditIfNotExists, DeployIfNotExists executa uma implementação de modelo quando a condição é cumprida.

> [!NOTE]
> [Aninhados modelos](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) são suportadas com **deployIfNotExists**, mas [ligado modelos](../../../azure-resource-manager/resource-group-linked-templates.md) não são atualmente suportadas.

### <a name="deployifnotexists-evaluation"></a>Avaliação de DeployIfNotExists

DeployIfNotExists é executado depois de um fornecedor de recursos tratou de um pedido de recurso de criar ou atualizar e devolveu um código de estado de êxito. Uma implementação de modelo ocorre se existirem não existem recursos relacionados a ou se os recursos definidos pelo **ExistenceCondition** não avaliadas como verdadeiras.

Durante um ciclo de avaliação, definições de política com um efeito de DeployIfNotExists que correspondem aos recursos são marcadas como não conforme, mas foi efetuada nenhuma ação esse recurso.

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

O **detalhes** propriedade dos efeitos DeployIfNotExists tem todos os subproperties que definem os recursos relacionados para correspondência e a implementação do modelo para executar.

- **Tipo de** [necessário]
  - Especifica o tipo de recurso relacionado para corresponder.
  - Começa ao tentar obter um recurso por baixo da **se** recursos de condição, em seguida, consultas dentro do mesmo grupo de recursos como o **se** recursos de condição.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para corresponder e faz com que a política para obter um recurso específico em vez de todos os recursos do tipo especificado.
- **ResourceGroupName** (opcional)
  - Permite a correspondência do recurso relacionado provenientes de um grupo de recursos diferente.
  - Não se aplica se **tipo** é um recurso que seria por baixo da **se** recursos de condição.
  - A predefinição é o **se** grupo de recursos do recurso de condição.
  - Se uma implementação do modelo for executada, é implementada no grupo de recursos deste valor.
- **ExistenceScope** (opcional)
  - Valores permitidos são _subscrição_ e _ResourceGroup_.
  - Define o âmbito de como obter o recurso relacionado para corresponder a partir de.
  - Não se aplica se **tipo** é um recurso que seria por baixo da **se** recursos de condição.
  - Para _ResourceGroup_, seria o limite para o **se** grupo de recursos do recurso de condição ou o grupo de recursos especificado na **ResourceGroupName**.
  - Para _subscrição_, consulta da subscrição completa para os recursos relacionados.
  - A predefinição é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, quaisquer relacionadas com recursos de **tipo** satisfaça o efeito e não aciona a implementação.
  - Utiliza o mesmo idioma como a regra de política para o **se** de condição, mas é avaliado relativamente a cada recurso relacionado individualmente.
  - Se todos os recursos relacionados correspondente for avaliada como true, o efeito é satisfeito e não aciona a implementação.
  - Pode utilizar [field()] para verificar a equivalência com valores no **se** condição.
  - Por exemplo, poderia ser usado para validar que o recurso principal (no **se** condição) está na mesma localização de recursos que o recurso relacionado correspondente.
- **roleDefinitionIds** [necessário]
  - Esta propriedade tem de incluir uma matriz de cadeias de caracteres que corresponde ao ID de função de controlo de acesso baseado em funções acessível pela subscrição. Para obter mais informações, consulte [remediação - configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **Escopo** (opcional)
  - Valores permitidos são _subscrição_ e _ResourceGroup_.
  - Define o tipo de implementação para ser acionado. _Subscrição_ indica um [implementação ao nível da subscrição](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ indica uma implementação para um grupo de recursos.
  - Uma _localização_ propriedade deve ser especificada no _implementação_ quando utilizar implementações ao nível da subscrição.
  - A predefinição é _ResourceGroup_.
- **Implementação** [necessário]
  - Esta propriedade deve incluir a implementação do modelo completo, como ele foi transmitido para o `Microsoft.Resources/deployments` colocar API. Para obter mais informações, consulte a [API do REST de implementações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro de **implementação** propriedade são avaliadas como componentes do modelo, não a política. A exceção é o **parâmetros** propriedade que passa os valores da política para o modelo. O **valor** nesta secção num modelo de nome do parâmetro é utilizado para efetuar este valor passando (consulte _fullDbName_ no exemplo DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo de DeployIfNotExists

Exemplo: Avalia a bases de dados do SQL Server para determinar se transparentDataEncryption está ativado. Caso contrário, em seguida, uma implementação para permitir que é executada.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>Políticas de disposição em camadas

Um recurso pode ser afetado por várias atribuições. Estas atribuições podem ser no mesmo âmbito ou em âmbitos diferentes. Cada um destas atribuições também é provável que ter um efeito de diferente definido. A condição e o efeito de cada política de forma independente é avaliada. Por exemplo:

- Política de 1
  - Restringe a localização do recurso para 'westus'
  - Atribuído a subscrição A
  - Negar o efeito
- Política de 2
  - Restringe a localização de recursos para "eastus"
  - Atribuído ao grupo de recursos B na subscrição A
  - Efeito de auditoria
  
Esta configuração iria originar o seguinte resultado:

- Qualquer recurso já no grupo de recursos B no "eastus" está em conformidade com a política de 2 e não em conformidade com a política de 1
- Qualquer recurso já no grupo de recursos B não está em "eastus" é incompatível para a política de 2 e não em conformidade com a política de 1 se não estiver em 'westus'
- De qualquer novo recurso na subscrição não está no 'westus' é negado por política 1
- De qualquer novo recurso na subscrição A e B de grupo de recursos em 'westus' está criado e em não conformidade na política de 2

Se a diretiva 1 e 2 de política que tinha efeitos de negações, a situação muda para:

- Qualquer recurso já no grupo de recursos B não está em "eastus" está em conformidade para a política de 2
- Qualquer recurso já no grupo de recursos B não está no 'westus' está em conformidade para a política de 1
- De qualquer novo recurso na subscrição não está no 'westus' é negado por política 1
- De qualquer novo recurso no grupo de recursos B de uma subscrição é negado

Cada atribuição individualmente é avaliada. Como tal, existem não é uma oportunidade para um recurso de ortografia passar por meio de um intervalo de diferenças no âmbito. O resultado líquido de políticas de disposição em camadas ou sobreposição de diretivas é considerado como estando **cumulativa mais restritivo**. Por exemplo, se ambas as políticas de 1 e 2 tinham um efeito de recusa, um recurso seria bloqueado pelas políticas de conflitantes e sobrepostas. Se ainda precisar do recurso a ser criadas no âmbito de destino, reveja as exclusões em cada atribuição para validar as políticas corretas estão a afetar os âmbitos certos.

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md)
- Reveja o [estrutura de definição de política](definition-structure.md)
- Compreender como [criar políticas programaticamente](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [remediar recursos não compatíveis](../how-to/remediate-resources.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](../../management-groups/overview.md)
