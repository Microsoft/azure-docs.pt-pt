---
title: Entenda como os efeitos funcionam
description: As definições de Política Azure têm vários efeitos que determinam como a conformidade é gerida e reportada.
ms.date: 10/05/2020
ms.topic: conceptual
ms.openlocfilehash: 19811eca33be7dff4d9bee5b8bd89dd38f185a57
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873953"
---
# <a name="understand-azure-policy-effects"></a>Compreender os efeitos da Política Azure

Cada definição de política no Azure Policy tem um efeito único. Este efeito determina o que acontece quando a regra da política é avaliada de acordo com. Os efeitos comportam-se de forma diferente se forem para um novo recurso, um recurso atualizado ou um recurso existente.

Estes efeitos são atualmente apoiados numa definição de política:

- [Append](#append)
- [Auditar](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Deny](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desativado](#disabled)
- [Modificar](#modify)

Os seguintes efeitos são _depreciados:_

- [Normação DoPaConstrado](#enforceopaconstraint)
- [Aplicação Da Política](#enforceregopolicy)

> [!IMPORTANT]
> Em vez dos efeitos **DoPlipaConstraint** ou **EnforceRegoPolicy,** utilize _auditoria_ e _negue_ com o modo fornecedor de `Microsoft.Kubernetes.Data` recursos. As definições políticas incorporadas foram atualizadas. Quando as atribuições políticas existentes destas definições de política incorporadas forem alteradas, o parâmetro de _efeito_ deve ser alterado para um valor na lista _de Valores Devues permitidos atualizados._

## <a name="order-of-evaluation"></a>Ordem de avaliação

Os pedidos para criar ou atualizar um recurso são avaliados pela Azure Policy em primeiro lugar. A Azure Policy cria uma lista de todas as atribuições que se aplicam ao recurso e, em seguida, avalia o recurso em cada definição. Para um [modo gestor de recursos,](./definition-structure.md#resource-manager-modes)a Azure Policy processa vários dos efeitos antes de entregar o pedido ao Fornecedor de Recursos apropriado. Esta ordem impede o processamento desnecessário por um Fornecedor de Recursos quando um recurso não satisfaz os controlos de governação projetados da Azure Policy. Com um [modo Fornecedor de Recursos,](./definition-structure.md#resource-provider-modes)o Fornecedor de Recursos gere a avaliação e o resultado e reporta os resultados de volta à Política Azure.

- **Os deficientes** são verificados primeiro para determinar se a regra da política deve ser avaliada.
- **Os apêndices** e **modificações** são então avaliados. Uma vez que qualquer um dos dois poderia alterar o pedido, uma alteração feita pode impedir que uma auditoria ou o efeito de negação desencadeie. Estes efeitos só estão disponíveis com um modo Gestor de Recursos.
- **O Deny** é então avaliado. Ao avaliar a negação antes da auditoria, é evitado o duplo registo de um recurso não existente.
- **A auditoria** é avaliada em último lugar.

Depois de o Fornecedor de Recursos retornar um código de sucesso num pedido de modo Gestor de Recursos, **auditifNotExists** e **DeployIfNotExists** avaliam para determinar se é necessário registo ou ação adicional de conformidade.

## <a name="append"></a>Acrescentar

O apêndice é utilizado para adicionar campos adicionais ao recurso solicitado durante a criação ou atualização. Um exemplo comum é especificar os IPs permitidos para um recurso de armazenamento.

> [!IMPORTANT]
> O apêndice destina-se a ser utilizado com propriedades não etiquetas. Embora o Apêndice possa adicionar tags a um recurso durante um pedido de criação ou atualização, é recomendado utilizar o efeito [Modificar](#modify) para tags.

### <a name="append-evaluation"></a>Avaliação do apêndice

O apêndice avalia antes de o pedido ser processado por um Fornecedor de Recursos durante a criação ou atualização de um recurso. O apêndice adiciona campos ao recurso quando **a** condição da regra política é cumprida. Se o efeito de apêndice sobrepor-se a um valor no pedido original com um valor diferente, então atua como um efeito de negação e rejeita o pedido. Para anexar um novo valor a uma matriz existente, use a **\[\*\]** versão do pseudónimo.

Quando uma definição de política usando o efeito apêndice é executada como parte de um ciclo de avaliação, não faz alterações aos recursos que já existem. Em vez disso, marca qualquer recurso que satisfaça **a** condição de não conforme.

### <a name="append-properties"></a>Propriedades do apêndice

Um efeito de apêndice tem apenas um conjunto **de detalhes,** que é necessário. Como **os detalhes** são uma matriz, pode levar um único par de **campo/valor** ou múltiplos. Consulte a [estrutura de definição](definition-structure.md#fields) para a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de apêndice

Exemplo 1: Par de **campo/valor** único usando **\[\*\]** um não-pseudónimo com um **valor** de matriz para definir [regras](definition-structure.md#aliases) IP numa conta de armazenamento. Quando o **\[\*\]** não-pseudónimo é uma matriz, o efeito anexa o **valor** como toda a matriz. Se a matriz já existe, um evento de negação ocorre do conflito.

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

Exemplo 2: Par de **campo/valor** único usando **\[\*\]** um pseudónimo com um **valor** de matriz para definir [regras](definition-structure.md#aliases) IP numa conta de armazenamento. Ao utilizar o **\[\*\]** pseudónimo, o efeito anexa o **valor** a uma matriz potencialmente pré-existente. Se a matriz ainda não existe, é criada.

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

## <a name="audit"></a>Auditoria

A auditoria é usada para criar um evento de alerta no registo de atividades ao avaliar um recurso não conforme, mas não impede o pedido.

### <a name="audit-evaluation"></a>Avaliação de auditoria

A auditoria é o último efeito verificado pela Azure Policy durante a criação ou atualização de um recurso. Para um modo gestor de recursos, a Azure Policy envia então o recurso para o Fornecedor de Recursos. A auditoria funciona da mesma forma para um pedido de recursos e um ciclo de avaliação. Para recursos novos e atualizados, a Azure Policy adiciona uma `Microsoft.Authorization/policies/audit/action` operação ao registo de atividades e marca o recurso como incompatível.

### <a name="audit-properties"></a>Propriedades de auditoria

Para um modo gestor de recursos, o efeito de auditoria não tem quaisquer propriedades adicionais para uso no **estado então** da definição de política.

Para um modo de fornecedor de `Microsoft.Kubernetes.Data` recursos, o efeito de auditoria tem as seguintes subpropriedades adicionais de **detalhes**.

- **restriçãoTemplate** (obrigatório)
  - O modelo de restrição CustomResourceDefinition (CRD) que define novos Constrangimentos. O modelo define a lógica rego, o esquema de Restrição e os parâmetros de Restrição que são passados através de **valores** da Política de Azure.
- **restrição** (necessária)
  - A implementação de CRD do modelo De Restrição. Utiliza parâmetros passados através de **valores** como `{{ .Values.<valuename> }}` . No exemplo 2 abaixo, estes valores são `{{ .Values.excludedNamespaces }}` e `{{ .Values.allowedContainerImagesRegex }}` .
- **valores** (opcional)
  - Define quaisquer parâmetros e valores para passar para a Restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo 1: Utilização do efeito de auditoria para os modos gestor de recursos.

```json
"then": {
    "effect": "audit"
}
```

Exemplo 2: Utilizar o efeito de auditoria para um modo de Fornecedor de Recursos de `Microsoft.Kubernetes.Data` . As informações adicionais em **detalhes** definem o modelo de Restrição e CRD para usar em Kubernetes para limitar as imagens permitidas do recipiente.

```json
"then": {
    "effect": "audit",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists permite a auditoria de recursos _relacionados com_ o recurso que corresponde à condição **de se,** mas não tem as propriedades especificadas nos **detalhes** da condição **então.**

### <a name="auditifnotexists-evaluation"></a>Avaliação auditIfNotExists

AuditIfNotExists funciona depois de um Fornecedor de Recursos ter tratado um pedido de criação ou atualização de recursos e ter devolvido um código de estado de sucesso. A auditoria ocorre se não houver recursos relacionados ou se os recursos definidos pela **ExistênciaCondição** não avaliarem verdadeiramente. Para recursos novos e atualizados, a Azure Policy adiciona uma `Microsoft.Authorization/policies/audit/action` operação ao registo de atividades e marca o recurso como incompatível. Quando acionado, o recurso que satisfez a condição **de se** é o recurso que é marcado como incompatível.

### <a name="auditifnotexists-properties"></a>Propriedades auditIfNotExists

Os **detalhes** da propriedade dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a combinar.

- **Tipo** (obrigatório)
  - Especifica o tipo de recurso relacionado a combinar.
  - Se **o detalhe.type** é um tipo de recurso por baixo do recurso **de** condição, as consultas de política para recursos deste **tipo** no âmbito do recurso avaliado. Caso contrário, as consultas políticas dentro do mesmo grupo de recursos que o recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para corresponder e faz com que a política pegue um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição se avalia para **se.field.type** **e, em seguida,.details.type** match, então **o nome** torna-se _necessário_ e deve ser , ou para um `[field('name')]` recurso `[field('fullName')]` infantil.
    No entanto, deve considerar-se um efeito [de auditoria.](#audit)
- **Nome do Grupo de Recursos** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **o tipo** for um recurso que estaria abaixo do recurso **de** condição.
  - O predefinido é **o** grupo de recursos do recurso da condição.
- **ExistênciaScope** (opcional)
  - Os valores permitidos são _Subscrição_ e _Grupo de Recursos._
  - Define o âmbito de onde buscar o recurso relacionado para combinar.
  - Não se aplica se **o tipo** for um recurso que estaria abaixo do recurso **de** condição.
  - Para o _Grupo de Recursos,_ limitar-se-ia ao grupo de recursos do recurso se estiver em condições ou ao grupo de recursos especificado no **ResourceGroupName**. **if**
  - Para _Subscrição,_ questione toda a subscrição do recurso relacionado.
  - Predefinido é _Grupo de Recursos._
- **ExistênciaCondição** (opcional)
  - Se não for especificado, qualquer recurso relacionado do **tipo** satisfaz o efeito e não desencadeia a auditoria.
  - Usa a mesma linguagem que a regra da política para a condição **se,** mas é avaliada individualmente contra cada recurso relacionado.
  - Se qualquer recurso relacionado se avaliar de verdade, o efeito é satisfeito e não desencadeia a auditoria.
  - Pode utilizar [campo)] para verificar a equivalência com valores na condição **de se.**
  - Por exemplo, poderia ser usado para validar que o recurso principal (na condição **em caso** de condição) está na mesma localização de recurso que o recurso correspondente.

### <a name="auditifnotexists-example"></a>AuditIfNotExists exemplo

Exemplo: Avalia máquinas virtuais para determinar se a extensão antimalware existe e, em seguida, audita quando falta.

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

## <a name="deny"></a>Negar

O Deny é usado para evitar um pedido de recursos que não corresponda aos padrões definidos através de uma definição de política e falhe no pedido.

### <a name="deny-evaluation"></a>Negar avaliação

Ao criar ou atualizar um recurso combinado num modo Gestor de Recursos, negar impede o pedido antes de ser enviado para o Fornecedor de Recursos. O pedido é devolvido como `403 (Forbidden)` um . No portal, o Proibido pode ser visto como um estatuto na implantação que foi impedido pela atribuição de políticas. Para um modo fornecedor de recursos, o fornecedor de recursos gere a avaliação do recurso.

Durante a avaliação dos recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como incompatíveis.

### <a name="deny-properties"></a>Negar propriedades

Para um modo Gestor de Recursos, o efeito negar não tem quaisquer propriedades adicionais para uso no **estado então** da definição de política.

Para um modo de fornecedor de recursos `Microsoft.Kubernetes.Data` de , o efeito de negação tem as seguintes subproperriedades adicionais de **detalhes**.

- **restriçãoTemplate** (obrigatório)
  - O modelo de restrição CustomResourceDefinition (CRD) que define novos Constrangimentos. O modelo define a lógica rego, o esquema de Restrição e os parâmetros de Restrição que são passados através de **valores** da Política de Azure.
- **restrição** (necessária)
  - A implementação de CRD do modelo De Restrição. Utiliza parâmetros passados através de **valores** como `{{ .Values.<valuename> }}` . No exemplo 2 abaixo, estes valores são `{{ .Values.excludedNamespaces }}` e `{{ .Values.allowedContainerImagesRegex }}` .
- **valores** (opcional)
  - Define quaisquer parâmetros e valores para passar para a Restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="deny-example"></a>Negar exemplo

Exemplo 1: Utilizar o efeito de negação para os modos gestor de recursos.

```json
"then": {
    "effect": "deny"
}
```

Exemplo 2: Utilizar o efeito de negação para um modo de Fornecedor de Recursos de `Microsoft.Kubernetes.Data` . As informações adicionais em **detalhes** definem o modelo de Restrição e CRD para usar em Kubernetes para limitar as imagens permitidas do recipiente.

```json
"then": {
    "effect": "deny",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/constraint.yaml",
        "values": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]",
            "excludedNamespaces": "[parameters('excludedNamespaces')]"
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Semelhante ao AuditIfNotExists, uma definição de política deployIfNotExists executa uma implementação do modelo quando a condição é satisfeita.

> [!NOTE]
> [Os modelos de aninhados](../../../azure-resource-manager/templates/linked-templates.md#nested-template) são suportados com **implementaçõesIfNotExists,** mas [os modelos ligados](../../../azure-resource-manager/templates/linked-templates.md#linked-template) não são suportados atualmente.

### <a name="deployifnotexists-evaluation"></a>Implementação Avaliação DeNotExists

O DeployIfNotExists funciona cerca de 15 minutos depois de um Fornecedor de Recursos ter tratado um pedido de criação ou atualização de recursos e ter devolvido um código de estado de sucesso. Uma implementação do modelo ocorre se não houver recursos relacionados ou se os recursos definidos pela **ExistênciaCondição** não avaliarem verdadeiramente.
A duração da implantação depende da complexidade dos recursos incluídos no modelo.

Durante um ciclo de avaliação, definições de políticas com um efeito DeployIfNotExists que correspondem aos recursos são marcados como incompatíveis, mas não são tomadas medidas nesse recurso. Os recursos existentes não conformes podem ser remediados com uma [tarefa de reparação](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Implementar propriedades DoIfNotExists

A propriedade de **detalhes** do efeito DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados para corresponder e a implementação do modelo para executar.

- **Tipo** (obrigatório)
  - Especifica o tipo de recurso relacionado a combinar.
  - Começa por tentar obter um recurso por baixo do recurso **de condição,** em seguida, questiona dentro do mesmo grupo de recursos que o recurso **da** condição.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para corresponder e faz com que a política pegue um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição se avalia para **se.field.type** **e, em seguida,.details.type** match, então **o nome** torna-se _necessário_ e deve ser , ou para um `[field('name')]` recurso `[field('fullName')]` infantil.
- **Nome do Grupo de Recursos** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **o tipo** for um recurso que estaria abaixo do recurso **de** condição.
  - O predefinido é **o** grupo de recursos do recurso da condição.
  - Se uma implementação do modelo for executada, é implantada no grupo de recursos deste valor.
- **ExistênciaScope** (opcional)
  - Os valores permitidos são _Subscrição_ e _Grupo de Recursos._
  - Define o âmbito de onde buscar o recurso relacionado para combinar.
  - Não se aplica se **o tipo** for um recurso que estaria abaixo do recurso **de** condição.
  - Para o _Grupo de Recursos,_ limitar-se-ia ao grupo de recursos do recurso se estiver em condições ou ao grupo de recursos especificado no **ResourceGroupName**. **if**
  - Para _Subscrição,_ questione toda a subscrição do recurso relacionado.
  - Predefinido é _Grupo de Recursos._
- **ExistênciaCondição** (opcional)
  - Se não for especificado, qualquer recurso relacionado do **tipo** satisfaz o efeito e não desencadeia a implantação.
  - Usa a mesma linguagem que a regra da política para a condição **se,** mas é avaliada individualmente contra cada recurso relacionado.
  - Se qualquer recurso relacionado se avaliar de verdade, o efeito é satisfeito e não desencadeia a implantação.
  - Pode utilizar [campo)] para verificar a equivalência com valores na condição **de se.**
  - Por exemplo, poderia ser usado para validar que o recurso principal (na condição **em caso** de condição) está na mesma localização de recurso que o recurso correspondente.
- **roleDefinitionIds** (obrigatório)
  - Esta propriedade deve incluir uma variedade de cordas que correspondam ao ID da função de controlo de acesso baseado em funções, acessível pela subscrição. Para obter mais informações, consulte [a remediação - configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **Implementação DoScope** (opcional)
  - Os valores permitidos são _Subscrição_ e _Grupo de Recursos._
  - Define o tipo de implantação a ser acionado. _A subscrição_ indica uma [implementação ao nível de subscrição,](../../../azure-resource-manager/templates/deploy-to-subscription.md) _o ResourceGroup_ indica uma implantação para um grupo de recursos.
  - Uma propriedade de _localização_ deve ser especificada na _Implementação_ quando utilizar implementações de nível de subscrição.
  - Predefinido é _Grupo de Recursos._
- **Implantação** (necessária)
  - Esta propriedade deve incluir a implementação completa do modelo, uma vez que seria passada para a `Microsoft.Resources/deployments` API PUT. Para obter mais informações, consulte a [API de Implementações REST](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade **De implementação** são avaliadas como componentes do modelo, não a política. A exceção é a propriedade **de parâmetros** que passa valores da política para o modelo. O **valor** nesta secção sob um nome de parâmetro de modelo é usado para executar este valor de passagem (ver _fullDbName_ no exemplo DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Implementar Exemplo de NãoExists

Exemplo: Avalia as bases de dados do SQL Server para determinar se oDataEncryption transparente está ativado. Caso contrário, uma implantação para ativar é executada.

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

## <a name="disabled"></a>Desativado

Este efeito é útil para situações de teste ou para quando a definição de política tiver parametrizado o efeito. Esta flexibilidade permite desativar uma única missão em vez de desativar todas as atribuições dessa apólice.

Uma alternativa ao efeito de deficientes é **a "EnforcementMode",** que está definida na atribuição de políticas.
Quando **a aplicação da medida de medida** é _desativada,_ os recursos ainda são avaliados. O registo, como os registos de atividade, e o efeito da política não ocorrem. Para obter mais informações, consulte [a atribuição de políticas - modo de execução](./assignment-structure.md#enforcement-mode).

## <a name="enforceopaconstraint"></a>Normação DoPaConstrado

Este efeito é utilizado com um _modo_ de definição de política de `Microsoft.Kubernetes.Data` . É usado para passar regras de controlo de admissão gatekeeper v3 definidas com [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) para [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) para clusters Kubernetes em Azure.

> [!IMPORTANT]
> As definições limitadas de política de pré-visualização com efeito **EnforceOPAConstraint** e a categoria **de Serviço Kubernetes conexa** são _depreciadas_. Em vez disso, utilize a _auditoria_ de efeitos e _negue_ com o modo Fornecedor de Recursos `Microsoft.Kubernetes.Data` .

### <a name="enforceopaconstraint-evaluation"></a>Avaliação da Regra de Aplicação da OPA

O controlador de admissão do Agente de Política Aberta avalia qualquer novo pedido no cluster em tempo real.
A cada 15 minutos, uma varredura completa do cluster é completada e os resultados reportados à Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Executar propriedades da Empresa de Sub-2PAConstrata

Os **detalhes** propriedade do efeito EnforceOPAConstraint tem as subpropriedades que descrevem a regra de controlo de admissão Gatekeeper v3.

- **restriçãoTemplate** (obrigatório)
  - O modelo de restrição CustomResourceDefinition (CRD) que define novos Constrangimentos. O modelo define a lógica rego, o esquema de Restrição e os parâmetros de Restrição que são passados através de **valores** da Política de Azure.
- **restrição** (necessária)
  - A implementação de CRD do modelo De Restrição. Utiliza parâmetros passados através de **valores** como `{{ .Values.<valuename> }}` . No exemplo abaixo, estes valores são `{{ .Values.cpuLimit }}` e `{{ .Values.memoryLimit }}` .
- **valores** (opcional)
  - Define quaisquer parâmetros e valores para passar para a Restrição. Cada valor deve existir no modelo de restrição CRD.

### <a name="enforceopaconstraint-example"></a>Executar exemplo de RegrasOPA

Exemplo: Regra de controlo de admissão de gatekeeper v3 para definir cpu de contentor e limites de recursos de memória em Kubernetes.

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

## <a name="enforceregopolicy"></a>Aplicação Da Política

Este efeito é utilizado com um _modo_ de definição de política de `Microsoft.ContainerService.Data` . É usado para passar regras de controlo de admissão Gatekeeper v2 definidas com [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) para [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) no Serviço [Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> As definições de política de pré-visualização limitadas com o efeito **EnforceRegoPolicy** e a categoria **de Serviço Kubernetes conexas** são _depreciadas_. Em vez disso, utilize a _auditoria_ de efeitos e _negue_ com o modo Fornecedor de Recursos `Microsoft.Kubernetes.Data` .

### <a name="enforceregopolicy-evaluation"></a>Avaliação de Política De Aplicação

O controlador de admissão do Agente de Política Aberta avalia qualquer novo pedido no cluster em tempo real.
A cada 15 minutos, uma varredura completa do cluster é completada e os resultados reportados à Azure Policy.

### <a name="enforceregopolicy-properties"></a>Executar propriedades daRegoPolicy

Os **detalhes** propriedade do efeito EnforceRegoPolicy tem as subpropriedades que descrevem a regra de controlo de admissão gatekeeper v2.

- **policyId** (obrigatório)
  - Um nome único passou como parâmetro para a regra de controlo de admissão rego.
- **política** (necessária)
  - Especifica o URI da regra de controlo de admissão rego.
- **apólicesParameters** (opcional)
  - Define quaisquer parâmetros e valores para passar para a política do rego.

### <a name="enforceregopolicy-example"></a>Impor Exemplo de Política

Exemplo: Regra de controlo de admissão de porteiro v2 para permitir apenas as imagens de recipientes especificadas em AKS.

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

## <a name="modify"></a>Modificar

Modificar é usado para adicionar, atualizar ou remover propriedades ou tags em um recurso durante a criação ou atualização.
Um exemplo comum é a atualização de etiquetas em recursos como o CostCenter. Os recursos existentes não conformes podem ser remediados com uma [tarefa de reparação](../how-to/remediate-resources.md). Uma única regra de modificação pode ter qualquer número de operações.

As seguintes operações são suportadas pela Modificação:

- Adicione, substitua ou remova as etiquetas de recursos. Para etiquetas, uma política de modificação deveria ter `mode` definido para _Indexado,_ a menos que o recurso-alvo seja um grupo de recursos.
- Adicione ou substitua o valor do tipo de identidade gerido `identity.type` () de máquinas virtuais e conjuntos de balanças de máquinas virtuais.
- Adicione ou substitua os valores de certos pseudónimos (pré-visualização).
  - Utilizar o comando `Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }`
    em Azure PowerShell **4.6.0** ou superior para obter uma lista de pseudónimos que podem ser usados com Modificação.

> [!IMPORTANT]
> Se estiver a gerir tags, é aconselhável utilizar modificar em vez de apêndice, pois o Modificar fornece tipos de operação adicionais e a capacidade de remediar os recursos existentes. No entanto, o Apêndice é recomendado se não for capaz de criar uma identidade gerida ou modificar ainda não suporta o pseudónimo para a propriedade de recursos.

### <a name="modify-evaluation"></a>Modificar a avaliação

Modifique avaliações antes de o pedido ser processado por um Fornecedor de Recursos durante a criação ou atualização de um recurso. As operações de Modificação são aplicadas ao conteúdo do pedido quando **a** condição da regra política é cumprida. Cada operação de modificação pode especificar uma condição que determina quando é aplicada. As operações com condições que são avaliadas em _falso_ são ignoradas.

Quando um pseudónimo é especificado, são realizados os seguintes controlos adicionais para garantir que a operação Modificação não altere o conteúdo do pedido de forma a levar o fornecedor de recursos a rejeitá-lo:

- A propriedade a que o pseudónimo mapeia está marcada como "Modificável" na versão API do pedido.
- O tipo de símbolo na operação Modificar corresponde ao tipo de símbolo esperado para a propriedade na versão API do pedido.

Se um destes controlos falhar, a avaliação da política recaem para o **conflito especificado.**

> [!IMPORTANT]
> É recomefeitado que Modificar definições que incluem pseudónimos usam o **efeito de conflito** de _auditoria_ para evitar pedidos falhados usando versões API onde a propriedade mapeada não é 'Modifiável'. Se o mesmo pseudónimo se comportar de forma diferente entre as versões API, podem ser utilizadas operações de modificação condicional para determinar a operação de modificação utilizada para cada versão API.

Quando uma definição de política usando o efeito Modificar é executada como parte de um ciclo de avaliação, não faz alterações aos recursos que já existem. Em vez disso, marca qualquer recurso que satisfaça **a** condição de não conforme.

### <a name="modify-properties"></a>Modificar propriedades

Os **detalhes** da propriedade do efeito Modificar têm todas as subpropriedades que definem as permissões necessárias para a reparação e as **operações** usadas para adicionar, atualizar ou remover valores de marcação.

- **roleDefinitionIds** (obrigatório)
  - Esta propriedade deve incluir uma variedade de cordas que correspondam ao ID da função de controlo de acesso baseado em funções, acessível pela subscrição. Para obter mais informações, consulte [a remediação - configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
  - O papel definido deve incluir todas as operações concedidas ao [papel de contribuinte.](../../../role-based-access-control/built-in-roles.md#contributor)
- **conflitoFeito** (opcional)
  - Determina qual a definição de política "ganha" no caso de mais de uma definição de política modificar a mesma propriedade ou quando a operação Modificar não funciona no pseudónimo especificado.
    - Para recursos novos ou atualizados, a definição de política com _negação_ tem precedência. Definições de política com _auditoria_ saltam todas as **operações.** Se mais do que uma definição política _tiver negado,_ o pedido é negado como um conflito. Se todas as definições políticas tiverem _auditoria,_ nenhuma das **operações** das definições de política conflituosa será processada.
    - Para os recursos existentes, se mais do que uma definição política _tiver de negar,_ o estatuto de conformidade é _Conflito._ Se uma ou menos definições de política _tiverem de negar,_ cada atribuição devolve um estatuto de _conformidade de incumprimento_.
  - Valores disponíveis: _auditoria,_ _negação,_ _deficientes._
  - O valor predefinido é _negar._
- **operações** (necessárias)
  - Um conjunto de todas as operações de etiquetas a serem concluídas em recursos correspondentes.
  - Propriedades:
    - **funcionamento** (obrigatório)
      - Define que ação tomar num recurso correspondente. As opções são: _addOrReplace_, _Add_, _Remove_. _Adicione_ comporta-se semelhante ao efeito [Apêndice.](#append)
    - **campo** (obrigatório)
      - A etiqueta para adicionar, substituir ou remover. Os nomes de identificação devem aderir à mesma convenção de nomeação para outros [domínios](./definition-structure.md#fields).
    - **valor** (opcional)
      - O valor para definir a etiqueta para.
      - Esta propriedade é necessária se **a operação** for _addOrReplace_ ou _Add_.
    - **condição** (opcional)
      - Uma corda que contém uma expressão linguística Azure Policy com [funções políticas](./definition-structure.md#policy-functions) que _avaliam_ a verdade ou _falsa_.
      - Não suporta as seguintes funções políticas: `field()` `resourceGroup()` . . . `subscription()` .

### <a name="modify-operations"></a>Modificar operações

O conjunto de propriedades **de operações** permite alterar várias tags de diferentes maneiras a partir de uma única definição de política. Cada operação é constituída por propriedades de **operação,** **campo**e **valor.** O funcionamento determina o que a tarefa de remediação faz às tags, o campo determina qual a etiqueta alterada e o valor define a nova definição para essa etiqueta. O exemplo abaixo faz as seguintes alterações na etiqueta:

- Define a `environment` etiqueta para "Testar", mesmo que já exista com um valor diferente.
- Remove a etiqueta `TempResource` .
- Define a `Dept` etiqueta para o parâmetro de política _DeptName_ configurado na atribuição de política.

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

A **propriedade de operação** tem as seguintes opções:

|Operação |Descrição |
|-|-|
|addOrReplace |Adiciona a propriedade definida ou etiqueta e valor ao recurso, mesmo que o imóvel ou etiqueta já exista com um valor diferente. |
|Adicionar |Adiciona a propriedade definida ou etiqueta e valor ao recurso. |
|Remover |Remove a propriedade ou etiqueta definida do recurso. |

### <a name="modify-examples"></a>Modificar exemplos

Exemplo 1: Adicione a `environment` etiqueta e substitua `environment` as etiquetas existentes por "Teste":

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

Exemplo 2: Retire a `env` etiqueta e adicione a etiqueta ou `environment` substitua as `environment` etiquetas existentes por um valor parametrizado:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "conflictEffect": "deny",
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

Exemplo 3: Certifique-se de que uma conta de armazenamento não permite o acesso público blob, a operação Modificar é aplicada apenas ao avaliar pedidos com versão API maior ou igual a '2019-04-01':

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/microsoft.authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab"
        ],
        "conflictEffect": "audit",
        "operations": [
            {
                "condition": "[greaterOrEquals(requestContext().apiVersion, '2019-04-01')]",
                "operation": "addOrReplace",
                "field": "Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
                "value": false
            }
        ]
    }
}
```

## <a name="layering-policy-definitions"></a>Definições de políticas de camadas

Um recurso pode ser impactado por várias atribuições. Estas atribuições podem estar no mesmo âmbito ou em âmbitos diferentes. Cada uma destas atribuições também é suscetível de ter um efeito diferente definido. A condição e o efeito de cada política são avaliados de forma independente. Por exemplo:

- Política 1
  - Restringe a localização dos recursos ao 'westus'
  - Atribuída à assinatura A
  - Negar efeito
- Política 2
  - Restringe a localização dos recursos ao 'eastus'
  - Atribuído ao grupo de recursos B na assinatura A
  - Efeito de auditoria
  
Esta configuração resultaria no seguinte resultado:

- Qualquer recurso já no grupo de recursos B em 'eastus' está em conformidade com a política 2 e não está em conformidade com a política 1
- Qualquer recurso já no grupo B de recursos que não esteja no "eastus" não está em conformidade com a política 2 e não está em conformidade com a política 1, se não em 'westus'
- Qualquer novo recurso na subscrição A não em 'westus' é negado pela política 1
- Qualquer novo recurso na subscrição A e grupo de recursos B em 'westus' é criado e não conforme na política 2

Se tanto a política 1 como a política 2 tiveram efeito de negação, a situação muda para:

- Qualquer recurso já no grupo B de recursos que não esteja em 'eastus' não está em conformidade com a política 2
- Qualquer recurso já no grupo B de recursos que não esteja em 'westus' não está em conformidade com a política 1
- Qualquer novo recurso na subscrição A não em 'westus' é negado pela política 1
- Qualquer novo recurso no grupo B de subscrição A é negado

Cada atribuição é avaliada individualmente. Como tal, não há uma oportunidade para um recurso escapar através de uma lacuna de diferenças de âmbito. Considera-se **cumulativo o**resultado líquido das definições de política de camadas. A título de exemplo, se ambas as políticas 1 e 2 tivessem um efeito de negação, um recurso seria bloqueado pelas definições políticas sobrepostas e contraditórias. Se ainda precisar do recurso para ser criado no âmbito alvo, reveja as exclusões de cada atribuição para validar as atribuições políticas certas estão a afetar os âmbitos certos.

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)