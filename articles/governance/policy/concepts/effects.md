---
title: Entender como os efeitos funcionam
description: As definições de Azure Policy têm vários efeitos que determinam como a conformidade é gerenciada e relatada.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 2b588cfb7c13a63e3fa5d3a65d9ccb24a2e854fd
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972803"
---
# <a name="understand-azure-policy-effects"></a>Compreenda os efeitos de política do Azure

Cada definição de política na política do Azure tem um efeito único. Esse efeito determina o que acontece quando a regra de política é avaliada para corresponder. Os efeitos ter um comportamento diferente se forem para um novo recurso, um recurso atualizado ou um recurso existente.

Atualmente, há suporte para esses efeitos em uma definição de política:

- [Anexar](#append)
- [Auditoria](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Negar](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desativado](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (visualização)
- [EnforceRegoPolicy](#enforceregopolicy) (visualização)
- [Modificar](#modify)

## <a name="order-of-evaluation"></a>Ordem de avaliação

Os pedidos para criar ou atualizar um recurso através do Azure Resource Manager são avaliados pelo Azure Policy. Azure Policy cria uma lista de todas as atribuições que se aplicam ao recurso e, em seguida, avalia o recurso em relação a cada definição. O Azure Policy processa vários dos efeitos antes de entregar a solicitação ao provedor de recursos apropriado. Isso impede o processamento desnecessário por um provedor de recursos quando um recurso não atende aos controles de governança criados de Azure Policy.

- **Desativado** será verificado primeiro para determinar se a regra de política deve ser avaliada.
- **Acréscimo** e **modificação** são então avaliados. Como o pode alterar a solicitação, uma alteração feita pode impedir o disparo de um efeito de auditoria ou negação.
- **Negar** , em seguida, é avaliada. Avaliando negar antes de auditoria, registo duplo de um recurso indesejado é evitado.
- **Auditoria** , em seguida, é avaliado antes do pedido vai para o fornecedor de recursos.

Depois do fornecedor de recursos devolve um código de sucesso **AuditIfNotExists** e **DeployIfNotExists** avaliar para determinar se o registo de conformidade adicionais ou ação é necessária.

No momento, não há nenhuma ordem de avaliação para os efeitos **EnforceOPAConstraint** ou **EnforceRegoPolicy** .

## <a name="disabled"></a>Desativado

Esse efeito é útil para situações de teste ou para quando a definição de política tem parametrizado o efeito. Esta flexibilidade torna possível desativar uma atribuição de única em vez de desativar todas as atribuições dessa política.

Uma alternativa ao efeito desabilitado é **imposiçãomode** , que é definida na atribuição de política.
Quando **imposiçãomode** é _desabilitada_, os recursos ainda são avaliados. Registro em log, como logs de atividade, e o efeito de política não ocorre. Para obter mais informações, consulte [atribuição de política – modo de imposição](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Acrescentar

Acrescentar é usado para adicionar campos adicionais para o recurso solicitado durante a criação ou atualização. Um exemplo comum é especificar os IPs permitidos para um recurso de armazenamento.

> [!IMPORTANT]
> Append destina-se a uso com propriedades sem marca. Embora Append possa adicionar marcas a um recurso durante uma solicitação de criação ou atualização, é recomendável usar o efeito [Modificar](#modify) para marcas em vez disso.

### <a name="append-evaluation"></a>Acrescentar a avaliação

Acrescentar avalia antes do pedido é processado por um fornecedor de recursos durante a criação ou atualização de um recurso. Acrescentar adiciona campos ao recurso quando o **se** é cumprida a condição de regra de política. Se o efeito de acréscimo poderia substituir um valor no pedido original com um valor diferente, em seguida, ele atua como um efeito de recusa e rejeita o pedido. Para acrescentar um novo valor a uma matriz existente, use a versão **[\*]** do alias.

Quando uma definição de política com o efeito de acréscimo é executada como parte de um ciclo de avaliação, não faz as alterações aos recursos já existentes. Em vez disso, ele marca a qualquer recurso que cumpra os **se** condição como não conforme.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito de acréscimo apenas tem um **detalhes** matriz, o que é necessário. Como **detalhes** é uma matriz, pode demorar um único **campo/valor** par ou múltiplos. Consulte a [estrutura de definição](definition-structure.md#fields) para obter a lista de campos aceitáveis.

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

Quando uma definição de política que usa o efeito modificar é executada como parte de um ciclo de avaliação, ela não faz alterações nos recursos que já existem. Em vez disso, ele marca a qualquer recurso que cumpra os **se** condição como não conforme.

### <a name="modify-properties"></a>Modificar propriedades

A propriedade **Details** do efeito modificar tem todas as subpropriedades que definem as permissões necessárias para a correção e as **operações** usadas para adicionar, atualizar ou remover valores de marca.

- **roleDefinitionIds** [necessário]
  - Esta propriedade tem de incluir uma matriz de cadeias de caracteres que corresponde ao ID de função de controlo de acesso baseado em funções acessível pela subscrição. Para obter mais informações, consulte [remediação - configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
  - A função definida deve incluir todas as operações concedidas à função [colaborador](../../../role-based-access-control/built-in-roles.md#contributor) .
- **operações** [obrigatório]
  - Uma matriz de todas as operações de marca a serem concluídas em recursos correspondentes.
  - Propriedades:
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

Negar serve para impedir que um pedido de recurso que não corresponde aos padrões definidos por meio de uma definição de política e o pedido de falha.

### <a name="deny-evaluation"></a>Avaliação de negação

Quando criar ou atualizar um recurso correspondente, negar impede que o pedido antes de serem enviados para o fornecedor de recursos. O pedido é retornado como um `403 (Forbidden)`. No portal, o proibido pode ser visualizado como um Estado de implementação que foi impedida pela atribuição de política.

Durante a avaliação dos recursos existentes, os recursos que correspondam a uma definição de política de negação são marcados como não conforme.

### <a name="deny-properties"></a>Propriedades de negação

O efeito de recusa não tem as propriedades adicionais para utilização no **, em seguida,** condição para a definição de política.

### <a name="deny-example"></a>Exemplo de negação

Exemplo: A utilizar o efeito de recusa.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditoria

Auditoria é utilizada para criar um evento de aviso no registo de atividades, ao avaliar a um recurso não compatível, mas ele não para o pedido.

### <a name="audit-evaluation"></a>Avaliação de auditoria

Audit é o último efeito verificado por Azure Policy durante a criação ou a atualização de um recurso. Azure Policy, em seguida, envia o recurso para o provedor de recursos. Auditoria funciona da mesma para um pedido de recurso e um ciclo de avaliação. Azure Policy adiciona uma operação de `Microsoft.Authorization/policies/audit/action` ao log de atividades e marca o recurso como sem conformidade.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito de auditoria não tem as propriedades adicionais para utilização no **, em seguida,** condição para a definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: A utilizar o efeito de auditoria.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists permite a auditoria em recursos que correspondam a **se** condição, mas não tem os componentes especificados no **detalhes** do **, em seguida,** condição.

### <a name="auditifnotexists-evaluation"></a>Avaliação de AuditIfNotExists

AuditIfNotExists é executado depois de um fornecedor de recursos tratou de um pedido de recurso de criar ou atualizar e devolveu um código de estado de êxito. A auditoria ocorre se existirem não existem recursos relacionados a ou se os recursos definidos pelo **ExistenceCondition** não avaliadas como verdadeiras. Azure Policy adiciona uma operação de `Microsoft.Authorization/policies/audit/action` ao log de atividades da mesma maneira que o efeito de auditoria. Quando acionado, o recurso que satisfeitos os **se** condição é o recurso que está marcado como não conforme.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

O **detalhes** propriedade dos efeitos AuditIfNotExists tem todos os subproperties que definem os recursos relacionados para corresponder.

- **Tipo de** [necessário]
  - Especifica o tipo de recurso relacionado para corresponder.
  - Se **Details. Type** for um tipo de recurso sob o recurso **If** Condition, a política consultará os recursos desse **tipo** dentro do escopo do recurso avaliado. Caso contrário, as consultas de política dentro do mesmo grupo de recursos que o recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para corresponder e faz com que a política para obter um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando os valores de condição para **If. Field. Type** e **. Details. Type** forem correspondentes, o **nome** será _necessário_ e deverá ser `[field('name')]`. No entanto, um efeito de [auditoria](#audit) deve ser considerado em vez disso.
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

Semelhante a AuditIfNotExists, uma definição de política DeployIfNotExists executa uma implantação de modelo quando a condição é atendida.

> [!NOTE]
> [Aninhados modelos](../../../azure-resource-manager/templates/linked-templates.md#nested-template) são suportadas com **deployIfNotExists**, mas [ligado modelos](../../../azure-resource-manager/templates/linked-templates.md#linked-template) não são atualmente suportadas.

### <a name="deployifnotexists-evaluation"></a>Avaliação de DeployIfNotExists

DeployIfNotExists é executado depois de um fornecedor de recursos tratou de um pedido de recurso de criar ou atualizar e devolveu um código de estado de êxito. Uma implementação de modelo ocorre se existirem não existem recursos relacionados a ou se os recursos definidos pelo **ExistenceCondition** não avaliadas como verdadeiras.

Durante um ciclo de avaliação, definições de política com um efeito de DeployIfNotExists que correspondem aos recursos são marcadas como não conforme, mas foi efetuada nenhuma ação esse recurso.

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

A propriedade **Details** do efeito DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados para corresponder e a implantação de modelo a ser executada.

- **Tipo de** [necessário]
  - Especifica o tipo de recurso relacionado para corresponder.
  - Começa ao tentar obter um recurso por baixo da **se** recursos de condição, em seguida, consultas dentro do mesmo grupo de recursos como o **se** recursos de condição.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para corresponder e faz com que a política para obter um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando os valores de condição para **If. Field. Type** e **. Details. Type** forem correspondentes, o **nome** será _necessário_ e deverá ser `[field('name')]`.
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
- **DeploymentScope** (opcional)
  - Valores permitidos são _subscrição_ e _ResourceGroup_.
  - Define o tipo de implantação a ser disparada. A _assinatura_ indica uma [implantação no nível da assinatura](../../../azure-resource-manager/templates/deploy-to-subscription.md), _resourcegroup_ indica uma implantação em um grupo de recursos.
  - Uma propriedade _Location_ deve ser especificada na _implantação_ ao usar implantações de nível de assinatura.
  - A predefinição é _ResourceGroup_.
- **Implementação** [necessário]
  - Esta propriedade deve incluir a implementação do modelo completo, como ele foi transmitido para o `Microsoft.Resources/deployments` colocar API. Para obter mais informações, consulte a [API do REST de implementações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro de **implementação** propriedade são avaliadas como componentes do modelo, não a política. A exceção é o **parâmetros** propriedade que passa os valores da política para o modelo. O **valor** nesta secção num modelo de nome do parâmetro é utilizado para efetuar este valor passando (consulte _fullDbName_ no exemplo DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo de DeployIfNotExists

Exemplo: Avalia as bases de dados do SQL Server para determinar se transparentDataEncryption está ativado. Caso contrário, uma implantação a ser habilitada será executada.

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

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Entenda como [criar políticas programaticamente](../how-to/programmatically-create.md).
- Saiba como [obter dados de conformidade](../how-to/get-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
