---
title: Entender como os efeitos funcionam
description: As definições de Azure Policy têm vários efeitos que determinam como a conformidade é gerenciada e relatada.
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: c448ab889ad263f4f8b6c9a59048551ca761d69a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464045"
---
# <a name="understand-azure-policy-effects"></a>Entender Azure Policy efeitos

Cada definição de política no Azure Policy tem um único efeito. Esse efeito determina o que acontece quando a regra de política é avaliada para corresponder. Os efeitos se comportam de maneira diferente se forem para um novo recurso, um recurso atualizado ou um recurso existente.

Atualmente, há suporte para esses efeitos em uma definição de política:

- [Anexar](#append)
- [Auditoria](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Negar](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desativado](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (visualização)
- [EnforceRegoPolicy](#enforceregopolicy) (visualização)
- [Alterar](#modify)

## <a name="order-of-evaluation"></a>Ordem de avaliação

As solicitações para criar ou atualizar um recurso por meio de Azure Resource Manager são avaliadas por Azure Policy primeiro. Azure Policy cria uma lista de todas as atribuições que se aplicam ao recurso e, em seguida, avalia o recurso em relação a cada definição. O Azure Policy processa vários dos efeitos antes de entregar a solicitação ao provedor de recursos apropriado. Isso impede o processamento desnecessário por um provedor de recursos quando um recurso não atende aos controles de governança criados de Azure Policy.

- **Desabilitado** primeiro é verificado para determinar se a regra de política deve ser avaliada.
- **Acréscimo** e **modificação** são então avaliados. Como o pode alterar a solicitação, uma alteração feita pode impedir o disparo de um efeito de auditoria ou negação.
- Em seguida, **Deny** é avaliado. Ao avaliar Deny antes da auditoria, o log duplo de um recurso indesejado é impedido.
- A **auditoria** é avaliada antes que a solicitação vá para o provedor de recursos.

Depois que o provedor de recursos retornar um código de êxito, **AuditIfNotExists** e **DeployIfNotExists** serão avaliados para determinar se a ação ou o log de conformidade adicional é necessário.

No momento, não há nenhuma ordem de avaliação para os efeitos **EnforceOPAConstraint** ou **EnforceRegoPolicy** .

## <a name="disabled"></a>Desativado

Esse efeito é útil para situações de teste ou para quando a definição de política tiver parametrizado o efeito. Essa flexibilidade possibilita desabilitar uma única atribuição em vez de desabilitar todas as atribuições da política.

Uma alternativa ao efeito desabilitado é **imposiçãomode** , que é definida na atribuição de política.
Quando **imposiçãomode** é _desabilitada_, os recursos ainda são avaliados. Registro em log, como logs de atividade, e o efeito de política não ocorre. Para obter mais informações, consulte [atribuição de política – modo de imposição](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Acrescentar

Append é usado para adicionar campos adicionais ao recurso solicitado durante a criação ou atualização. Um exemplo comum é especificar os IPs permitidos para um recurso de armazenamento.

> [!IMPORTANT]
> Append destina-se a uso com propriedades sem marca. Embora Append possa adicionar marcas a um recurso durante uma solicitação de criação ou atualização, é recomendável usar o efeito [Modificar](#modify) para marcas em vez disso.

### <a name="append-evaluation"></a>Avaliação de acréscimo

O acréscimo é avaliado antes que a solicitação seja processada por um provedor de recursos durante a criação ou atualização de um recurso. Append adiciona campos ao recurso quando a condição **If** da regra de política é atendida. Se o efeito de acréscimo substituir um valor na solicitação original por um valor diferente, ele agirá como um efeito de negação e rejeitará a solicitação. Para acrescentar um novo valor a uma matriz existente, use a versão **[\*]** do alias.

Quando uma definição de política que usa o efeito de acréscimo é executada como parte de um ciclo de avaliação, ela não faz alterações nos recursos que já existem. Em vez disso, ele marca qualquer recurso que atenda à condição **If** como não compatível.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito de acréscimo tem apenas uma matriz de **detalhes** , que é necessária. Como os **detalhes** são uma matriz, pode levar um único par de **campo/valor** ou múltiplos. Consulte a [estrutura de definição](definition-structure.md#fields) para obter a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de acréscimo

Exemplo 1: par de **campo/valor** único usando um [alias](definition-structure.md#aliases) não **[\*]** com um **valor** de matriz para definir regras de IP em uma conta de armazenamento. Quando o alias não **[\*]** é uma matriz, o efeito acrescenta o **valor** como a matriz inteira. Se a matriz já existir, um evento Deny ocorrerá do conflito.

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

Exemplo 2: par de **campo/valor** único usando um [alias](definition-structure.md#aliases) **[\*]** com um **valor** de matriz para definir regras de IP em uma conta de armazenamento. Usando o alias **[\*]** , o efeito acrescenta o **valor** a uma matriz potencialmente pré-existente. Se a matriz ainda não existir, ela será criada.

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

## <a name="modify"></a>Modificar

Modify é usado para adicionar, atualizar ou remover marcas em um recurso durante a criação ou atualização. Um exemplo comum é A atualização de marcas em recursos como costCenter. Uma política de modificação sempre deve ter `mode` definido como _indexado_ , a menos que o recurso de destino seja um grupo de recursos. Os recursos não compatíveis existentes podem ser corrigidos com uma [tarefa de correção](../how-to/remediate-resources.md). Uma única regra de modificação pode ter qualquer quantidade de operações.

> [!IMPORTANT]
> No momento, Modify é usado apenas com marcas. Se você estiver gerenciando marcas, é recomendável usar modificar em vez de acrescentar como modificar fornece tipos de operação adicionais e a capacidade de corrigir recursos existentes. No entanto, o acréscimo é recomendado se você não conseguir criar uma identidade gerenciada.

### <a name="modify-evaluation"></a>Modificar avaliação

Modify avalia antes que a solicitação seja processada por um provedor de recursos durante a criação ou atualização de um recurso. Modifique adiciona ou atualiza marcas em um recurso quando a condição **se** da regra de política for atendida.

Quando uma definição de política que usa o efeito modificar é executada como parte de um ciclo de avaliação, ela não faz alterações nos recursos que já existem. Em vez disso, ele marca qualquer recurso que atenda à condição **If** como não compatível.

### <a name="modify-properties"></a>Modificar propriedades

A propriedade **Details** do efeito modificar tem todas as subpropriedades que definem as permissões necessárias para a correção e as **operações** usadas para adicionar, atualizar ou remover valores de marca.

- **roleDefinitionIds** [obrigatório]
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondam à ID da função de controle de acesso baseada em função acessível pela assinatura. Para obter mais informações, consulte [correção – configurar definição de política](../how-to/remediate-resources.md#configure-policy-definition).
  - A função definida deve incluir todas as operações concedidas à função [colaborador](../../../role-based-access-control/built-in-roles.md#contributor) .
- **operações** [obrigatório]
  - Uma matriz de todas as operações de marca a serem concluídas em recursos correspondentes.
  - Properties
    - **operação** [obrigatório]
      - Define a ação a ser tomada em um recurso correspondente. As opções são: _addOrReplace_, _Adicionar_, _remover_. _Adicionar_ se comporta de forma semelhante ao efeito de [acréscimo](#append) .
    - **campo** [obrigatório]
      - A marca a ser adicionada, substituída ou removida. Os nomes de marca devem aderir à mesma convenção de nomenclatura para outros [campos](./definition-structure.md#fields).
    - **valor** (opcional)
      - O valor para o qual definir a marca.
      - Essa propriedade será necessária se a **operação** for _addOrReplace_ ou _Add_.

### <a name="modify-operations"></a>Modificar operações

A matriz de propriedades de **operações** torna possível alterar várias marcas de diferentes maneiras de uma única definição de política. Cada operação é composta de propriedades de **operação**, **campo**e **valor** . A operação determina o que a tarefa de correção faz nas marcas, o campo determina qual marca é alterada e o valor define a nova configuração para essa marca. O exemplo a seguir faz as seguintes alterações de marca:

- Define a marca de `environment` como "Test", mesmo que ela já exista com um valor diferente.
- Remove a marca `TempResource`.
- Define a marca de `Dept` como o parâmetro de política _deptname_ configurado na atribuição de política.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

A propriedade **Operation** tem as seguintes opções:

|Operação |Descrição |
|-|-|
|addOrReplace |Adiciona a marca e o valor definidos ao recurso, mesmo que a marca já exista com um valor diferente. |
|Adicionar |Adiciona a marca e o valor definidos ao recurso. |
|Remover |Remove a marca definida do recurso. |

### <a name="modify-examples"></a>Modificar exemplos

Exemplo 1: adicionar a marca de `environment` e substituir as marcas de `environment` existentes por "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Exemplo 2: Remova a marca de `env` e adicione a marca de `environment` ou substitua as marcas de `environment` existentes por um valor com parâmetros:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Negar

Deny é usado para impedir que uma solicitação de recurso que não corresponda a padrões definidos por meio de uma definição de política e falhe na solicitação.

### <a name="deny-evaluation"></a>Avaliação de negação

Ao criar ou atualizar um recurso correspondente, Deny impede que a solicitação seja enviada para o provedor de recursos. A solicitação é retornada como um `403 (Forbidden)`. No portal, o proibido pode ser exibido como um status na implantação que foi impedido pela atribuição de política.

Durante a avaliação de recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como sem conformidade.

### <a name="deny-properties"></a>Propriedades de Deny

O efeito de negação não tem nenhuma propriedade adicional para uso na condição **then** da definição de política.

### <a name="deny-example"></a>Exemplo de Deny

Exemplo: usando o efeito de negação.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditoria

A auditoria é usada para criar um evento de aviso no log de atividades ao avaliar um recurso sem conformidade, mas ele não interrompe a solicitação.

### <a name="audit-evaluation"></a>Avaliação de auditoria

Audit é o último efeito verificado por Azure Policy durante a criação ou a atualização de um recurso. Azure Policy, em seguida, envia o recurso para o provedor de recursos. A auditoria funciona da mesma para uma solicitação de recurso e um ciclo de avaliação. Azure Policy adiciona uma operação de `Microsoft.Authorization/policies/audit/action` ao log de atividades e marca o recurso como sem conformidade.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito de auditoria não tem nenhuma propriedade adicional para uso na condição **then** da definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: usando o efeito de auditoria.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

O AuditIfNotExists habilita a auditoria em recursos que correspondem à condição **If** , mas não tem os componentes especificados nos **detalhes** da condição **then** .

### <a name="auditifnotexists-evaluation"></a>Avaliação do AuditIfNotExists

AuditIfNotExists é executado depois que um provedor de recursos tratou de uma solicitação de criação ou atualização de recurso e retornou um código de status de êxito. A auditoria ocorrerá se não houver recursos relacionados ou se os recursos definidos por **ExistenceCondition** não forem avaliados como true. Azure Policy adiciona uma operação de `Microsoft.Authorization/policies/audit/action` ao log de atividades da mesma maneira que o efeito de auditoria. Quando disparado, o recurso que satisfez a condição **If** é o recurso marcado como sem conformidade.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

A propriedade **Details** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondentes.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a corresponder.
  - Se **Details. Type** for um tipo de recurso sob o recurso **If** Condition, a política consultará os recursos desse **tipo** dentro do escopo do recurso avaliado. Caso contrário, as consultas de política dentro do mesmo grupo de recursos que o recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando os valores de condição para **If. Field. Type** e **. Details. Type** forem correspondentes, o **nome** será _necessário_ e deverá ser `[field('name')]`. No entanto, um efeito de [auditoria](#audit) deve ser considerado em vez disso.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se o **tipo** for um recurso que estaria abaixo do recurso de condição **If** .
  - O padrão é o grupo de recursos do recurso de condição **If** .
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Subscription_ e _resourcegroup_.
  - Define o escopo de onde buscar o recurso relacionado correspondente.
  - Não se aplica se o **tipo** for um recurso que estaria abaixo do recurso de condição **If** .
  - Para _resourcegroup_, limitaria ao grupo de recursos do recurso de condição **If** ou ao grupo de recursos especificado em **ResourceGroupName**.
  - Para _assinatura_, o consulta toda a assinatura para o recurso relacionado.
  - O padrão é _resourcegroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, qualquer recurso relacionado do **tipo** satisfizer o efeito e não disparará a auditoria.
  - Usa o mesmo idioma que a regra de política para a condição **If** , mas é avaliada em cada recurso relacionado individualmente.
  - Se qualquer recurso relacionado correspondente for avaliado como true, o efeito será atendido e não disparará a auditoria.
  - Pode usar [Field ()] para verificar a equivalência com valores na condição **If** .
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **If** ) está no mesmo local de recurso que o recurso relacionado correspondente.

### <a name="auditifnotexists-example"></a>Exemplo de AuditIfNotExists

Exemplo: avalia as máquinas virtuais para determinar se a extensão Antimalware existe e, em seguida, audita quando ausente.

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

Semelhante a AuditIfNotExists, uma definição de política DeployIfNotExists executa uma implantação de modelo quando a condição é atendida.

> [!NOTE]
> [Modelos aninhados](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) têm suporte com **deployIfNotExists**, mas atualmente não há suporte para [modelos vinculados](../../../azure-resource-manager/resource-group-linked-templates.md) .

### <a name="deployifnotexists-evaluation"></a>Avaliação do DeployIfNotExists

DeployIfNotExists é executado depois que um provedor de recursos tratou de uma solicitação de criação ou atualização de recurso e retornou um código de status de êxito. Uma implantação de modelo ocorrerá se não houver recursos relacionados ou se os recursos definidos por **ExistenceCondition** não forem avaliados como true.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondem aos recursos são marcadas como sem conformidade, mas nenhuma ação é executada nesse recurso.

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

A propriedade **Details** do efeito DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados para corresponder e a implantação de modelo a ser executada.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a corresponder.
  - Começa com a tentativa de buscar um recurso sob o recurso condição **If** e, em seguida, consultas dentro do mesmo grupo de recursos que o recurso **If** Condition.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando os valores de condição para **If. Field. Type** e **. Details. Type** forem correspondentes, o **nome** será _necessário_ e deverá ser `[field('name')]`.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se o **tipo** for um recurso que estaria abaixo do recurso de condição **If** .
  - O padrão é o grupo de recursos do recurso de condição **If** .
  - Se uma implantação de modelo for executada, ela será implantada no grupo de recursos desse valor.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Subscription_ e _resourcegroup_.
  - Define o escopo de onde buscar o recurso relacionado correspondente.
  - Não se aplica se o **tipo** for um recurso que estaria abaixo do recurso de condição **If** .
  - Para _resourcegroup_, limitaria ao grupo de recursos do recurso de condição **If** ou ao grupo de recursos especificado em **ResourceGroupName**.
  - Para _assinatura_, o consulta toda a assinatura para o recurso relacionado.
  - O padrão é _resourcegroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, qualquer recurso relacionado do **tipo** satisfizer o efeito e não disparará a implantação.
  - Usa o mesmo idioma que a regra de política para a condição **If** , mas é avaliada em cada recurso relacionado individualmente.
  - Se qualquer recurso relacionado correspondente for avaliado como true, o efeito será atendido e não disparará a implantação.
  - Pode usar [Field ()] para verificar a equivalência com valores na condição **If** .
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **If** ) está no mesmo local de recurso que o recurso relacionado correspondente.
- **roleDefinitionIds** [obrigatório]
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondam à ID da função de controle de acesso baseada em função acessível pela assinatura. Para obter mais informações, consulte [correção – configurar definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (opcional)
  - Os valores permitidos são _Subscription_ e _resourcegroup_.
  - Define o tipo de implantação a ser disparada. A _assinatura_ indica uma [implantação no nível da assinatura](../../../azure-resource-manager/deploy-to-subscription.md), _resourcegroup_ indica uma implantação em um grupo de recursos.
  - Uma propriedade _Location_ deve ser especificada na _implantação_ ao usar implantações de nível de assinatura.
  - O padrão é _resourcegroup_.
- **Implantação** [obrigatório]
  - Essa propriedade deve incluir a implantação de modelo completa, pois ela seria passada para a API `Microsoft.Resources/deployments` PUT. Para obter mais informações, consulte a [API REST de implantações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade de **implantação** são avaliadas como componentes do modelo, não a política. A exceção é a propriedade **Parameters** que passa valores da política para o modelo. O **valor** nesta seção em um nome de parâmetro de modelo é usado para executar esse valor de passagem (consulte _fullDbName_ no exemplo de DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo de DeployIfNotExists

Exemplo: avalia SQL Server bancos de dados para determinar se o transparentDataEncryption está habilitado. Caso contrário, uma implantação a ser habilitada será executada.

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
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
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

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Esse efeito é usado com um *modo* de definição de política de `Microsoft.Kubernetes.Data`. Ele é usado para passar regras de controle de admissão do gatekeeper v3 definidas com a [estrutura de restrição Opa](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) para abrir o OPA ( [agente de política](https://www.openpolicyagent.org/) ) para clusters kubernetes autogerenciados no Azure.

> [!NOTE]
> [Azure Policy para o mecanismo AKs](aks-engine.md) está em visualização pública e só dá suporte a definições de políticas internas.

### <a name="enforceopaconstraint-evaluation"></a>Avaliação do EnforceOPAConstraint

O controlador de admissão do agente de política aberto avalia qualquer nova solicitação no cluster em tempo real.
A cada 5 minutos, uma verificação completa do cluster é concluída e os resultados relatados para Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Propriedades de EnforceOPAConstraint

A propriedade **Details** do efeito EnforceOPAConstraint tem as subpropriedades que descrevem a regra de controle de admissão do gatekeeper v3.

- **constrainttemplate** [obrigatório]
  - O modelo de restrição CustomResourceDefinition (CRD) que define novas restrições. O modelo define a lógica Rego, o esquema de restrição e os parâmetros de restrição que são passados por meio de **valores** de Azure Policy.
- **restrição** [obrigatório]
  - A implementação de CRD do modelo de restrição. Usa parâmetros passados por meio de **valores** como `{{ .Values.<valuename> }}`. No exemplo a seguir, seria `{{ .Values.cpuLimit }}` e `{{ .Values.memoryLimit }}`.
- **valores** [opcional]
  - Define quaisquer parâmetros e valores a serem passados para a restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="enforceregopolicy-example"></a>Exemplo de EnforceRegoPolicy

Exemplo: regra de controle de admissão do gatekeeper V3 para definir limites de recursos de memória e CPU do contêiner no mecanismo de AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Esse efeito é usado com um *modo* de definição de política de `Microsoft.ContainerService.Data`. Ele é usado para passar as regras de controle de admissão do gatekeeper v2 definidas com [rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) para abrir o OPA ( [agente de política](https://www.openpolicyagent.org/) ) no [serviço kubernetes do Azure](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure Policy para AKs](rego-for-aks.md) está em visualização limitada e só dá suporte a definições de políticas internas

### <a name="enforceregopolicy-evaluation"></a>Avaliação do EnforceRegoPolicy

O controlador de admissão do agente de política aberto avalia qualquer nova solicitação no cluster em tempo real.
A cada 5 minutos, uma verificação completa do cluster é concluída e os resultados relatados para Azure Policy.

### <a name="enforceregopolicy-properties"></a>Propriedades de EnforceRegoPolicy

A propriedade **Details** do efeito EnforceRegoPolicy tem as subpropriedades que descrevem a regra de controle de admissão do gatekeeper v2.

- **PolicyId** [obrigatório]
  - Um nome exclusivo passado como um parâmetro para a regra de controle de admissão rego.
- **política** [obrigatório]
  - Especifica o URI da regra de controle de admissão rego.
- **políticaparameters** [opcional]
  - Define quaisquer parâmetros e valores a serem passados para a política rego.

### <a name="enforceregopolicy-example"></a>Exemplo de EnforceRegoPolicy

Exemplo: regra de controle de admissão do gatekeeper v2 para permitir apenas as imagens de contêiner especificadas em AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Políticas de camadas

Um recurso pode ser afetado por várias atribuições. Essas atribuições podem estar no mesmo escopo ou em escopos diferentes. Cada uma dessas atribuições também provavelmente terá um efeito diferente definido. A condição e o efeito de cada política são avaliados de forma independente. Por exemplo:

- Política 1
  - Restringe a localização do recurso para ' westus '
  - Atribuído à assinatura A
  - Negar efeito
- Política 2
  - Restringe a localização do recurso para ' eastus '
  - Atribuído ao grupo de recursos B na assinatura A
  - Efeito de auditoria
  
Essa configuração resultaria no seguinte resultado:

- Qualquer recurso que já esteja no grupo de recursos B em ' eastus ' é compatível com a política 2 e não compatível com a política 1
- Qualquer recurso que já esteja no grupo de recursos B não está em ' eastus ' não está em conformidade com a política 2 e não compatível com a política 1 se não estiver em ' westus '
- Qualquer novo recurso na assinatura que não esteja em ' westus ' é negado pela política 1
- Todos os novos recursos na assinatura A e no grupo de recursos B em ' westus ' são criados e não estão em conformidade na política 2

Se a política 1 e a política 2 tivessem efeito de negação, a situação muda para:

- Qualquer recurso que já esteja no grupo de recursos B não está em ' eastus ' não está em conformidade com a política 2
- Qualquer recurso que já esteja no grupo de recursos B que não esteja em ' westus ' não está em conformidade com a política 1
- Qualquer novo recurso na assinatura que não esteja em ' westus ' é negado pela política 1
- Qualquer recurso novo no grupo de recursos B da assinatura A é negado

Cada atribuição é avaliada individualmente. Assim, não há uma oportunidade para um recurso percorrer uma lacuna das diferenças no escopo. O resultado líquido de políticas de camadas ou sobreposição de política é considerado como **cumulativo mais restritivo**. Por exemplo, se a política 1 e 2 tiver um efeito de negação, um recurso será bloqueado pelas políticas de sobreposição e conflitantes. Se você ainda precisar que o recurso seja criado no escopo de destino, examine as exclusões em cada atribuição para validar se as políticas certas estão afetando os escopos corretos.

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
