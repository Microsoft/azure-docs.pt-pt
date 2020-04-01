---
title: Entenda como os efeitos funcionam
description: As definições de Política Azure têm vários efeitos que determinam como a conformidade é gerida e reportada.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 0330cb5c732921efda3627dec92e486657097d82
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422444"
---
# <a name="understand-azure-policy-effects"></a>Compreender os efeitos da política azure

Cada definição de política no Azure Policy tem um efeito único. Este efeito determina o que acontece quando a regra política é avaliada para corresponder. Os efeitos comportam-se de forma diferente se forem para um novo recurso, um recurso atualizado ou um recurso existente.

Estes efeitos são atualmente apoiados numa definição política:

- [Acrescentar](#append)
- [Auditoria](#audit)
- [AuditoriaIfNotExists](#auditifnotexists)
- [Negar](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Desativado](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (pré-visualização)
- [EnforceRegoPolicy](#enforceregopolicy) (pré-visualização)
- [Modificar](#modify)

## <a name="order-of-evaluation"></a>Ordem de avaliação

Os pedidos para criar ou atualizar um recurso através do Azure Resource Manager são avaliados pelo Azure Policy. A Política Azure cria uma lista de todas as atribuições que se aplicam ao recurso e, em seguida, avalia o recurso em relação a cada definição. A Política Azure processa vários dos efeitos antes de entregar o pedido ao Fornecedor de Recursos adequado. Ao fazê-lo, impede o processamento desnecessário por parte de um Fornecedor de Recursos quando um recurso não satisfaz os controlos de governação desenhados da Política Azure.

- **Os deficientes** são verificados primeiro para determinar se a regra da política deve ser avaliada.
- **O apêndice** e **modificar** são avaliados. Uma vez que qualquer um dos dois pode alterar o pedido, uma alteração feita pode impedir que uma auditoria ou negar o efeito de desencadear.
- **Negar** é então avaliado. Ao avaliar o negar antes da auditoria, evita-se o duplo registo de um recurso indesejado.
- **A auditoria** é então avaliada antes do pedido de ida ao Fornecedor de Recursos.

Depois de o Fornecedor de Recursos devolver um código de sucesso, **auditarIfNotexists** e **implementarIfNotExists** avaliam para determinar se é necessário registo ou ação adicional de conformidade.

Atualmente, não existe qualquer ordem de avaliação para os efeitos **EnforceOPAConstraint** ou **EnforceRegoPolicy.**

## <a name="disabled"></a>Desativado

Este efeito é útil para testar situações ou para quando a definição de política tiver parametrizado o efeito. Esta flexibilidade permite desativar uma única missão em vez de desativar todas as atribuições dessa política.

Uma alternativa ao efeito Desativado é a **execuçãoMode** que é definida na atribuição de políticas.
Quando **o modo de execução** é _desativado,_ os recursos ainda são avaliados. A exploração madeireira, como os registos de atividade, e o efeito político não ocorrem. Para mais informações, consulte [a atribuição de políticas - modo de execução](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Acrescentar

O apêndice é utilizado para adicionar campos adicionais ao recurso solicitado durante a criação ou atualização. Um exemplo comum é especificar iPs autorizados para um recurso de armazenamento.

> [!IMPORTANT]
> O apêndice destina-se a ser utilizado com propriedades não etiquetadas. Embora o Apêndice possa adicionar etiquetas a um recurso durante um pedido de criação ou atualização, recomenda-se que utilize o efeito [Modificar](#modify) para etiquetas.

### <a name="append-evaluation"></a>Avaliação do apêndice

O apêndice avalia antes de o pedido ser processado por um Fornecedor de Recursos durante a criação ou atualização de um recurso. O apêndice adiciona campos ao recurso quando a condição **da** regra da política for cumprida. Se o efeito apêndice anular um valor no pedido original com um valor diferente, então atua como um efeito de negação e rejeita o pedido. Para anexar um novo valor a uma matriz existente, use **a\*** versão do pseudónimo.

Quando uma definição de política que usa o efeito apêndice é executada como parte de um ciclo de avaliação, não faz alterações nos recursos que já existem. Em vez disso, marca qualquer recurso que cumpra a condição **se** não conforme.

### <a name="append-properties"></a>Propriedades anexadas

Um efeito apêndice tem apenas um conjunto de **detalhes,** que é necessário. Como **os detalhes** são uma matriz, pode levar um único **campo/par** de valor ou múltiplos. Consulte a estrutura de [definição](definition-structure.md#fields) para a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos anexados

Exemplo 1: Um único **par de campo/valor** utilizando um [pseudónimo](definition-structure.md#aliases) não-**\*[]** com um **valor** de matriz para definir regras ip numa conta de armazenamento. Quando o**pseudónimo\*** não é uma matriz, o efeito atribua o **valor** como toda a matriz. Se a matriz já existir, um evento de negação ocorre do conflito.

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

Exemplo 2: Um único **campo/par** de valor utilizando um **\*[ ]** [pseudónimo](definition-structure.md#aliases) com um **valor** de matriz para definir regras ip numa conta de armazenamento. Ao utilizar o **pseudónimo,\*** o efeito atribua o **valor** a uma matriz potencialmente pré-existente. Se a matriz ainda não existir, será criada.

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

Modificar é usado para adicionar, atualizar ou remover etiquetas num recurso durante a criação ou atualização. Um exemplo comum é atualizar tags em recursos como costCenter. Uma política de `mode` modificação deve sempre ter definido para _Indexado,_ a menos que o recurso-alvo seja um grupo de recursos. Os recursos não conformes existentes podem ser remediados com uma tarefa de [reparação.](../how-to/remediate-resources.md) Uma única regra de Modificação pode ter qualquer número de operações.

> [!IMPORTANT]
> Modificar é atualmente apenas para uso com etiquetas. Se estiver a gerir as etiquetas, recomenda-se utilizar o Modificar em vez do Apêndice, uma vez que o Modificar fornece tipos de operação adicionais e a capacidade de remediar os recursos existentes. No entanto, o Apêndice é recomendado se não for capaz de criar uma identidade gerida.

### <a name="modify-evaluation"></a>Modificar a avaliação

Modificar avalia ções antes de o pedido ser processado por um Fornecedor de Recursos durante a criação ou atualização de um recurso. Modificar etiquetas de adds ou atualizações num recurso quando **o** estado da regra da política for cumprido.

Quando uma definição de política que utiliza o efeito Modificar é executada como parte de um ciclo de avaliação, não faz alterações nos recursos que já existem. Em vez disso, marca qualquer recurso que cumpra a condição **se** não conforme.

### <a name="modify-properties"></a>Modificar propriedades

A propriedade **de detalhes** do efeito Modificar tem todas as subpropriedades que definem as permissões necessárias para a reparação e as **operações** utilizadas para adicionar, atualizar ou remover valores de etiqueta.

- **roleDefinitionIds** [necessário]
  - Esta propriedade deve incluir um conjunto de cordas que correspondam à função de controlo de acesso baseado em papéis de identificação acessível pela subscrição. Para mais informações, consulte [a reparação - configure a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
  - O papel definido deve incluir todas as operações concedidas ao papel [de Contribuinte.](../../../role-based-access-control/built-in-roles.md#contributor)
- **operações** [necessárias]
  - Uma série de todas as operações de etiquetas a serem concluídas com recursos correspondentes.
  - Propriedades:
    - **operação** [necessária]
      - Define que medidas tomar num recurso correspondente. As opções são: _addOrReplace,_ _Add_, _Remove ._ _Adicione_ comportamentos semelhantes ao efeito [apêndice.](#append)
    - **campo** [necessário]
      - A etiqueta para adicionar, substituir ou remover. Os nomes das etiquetas devem aderir à mesma convenção de nomeação para outros [domínios.](./definition-structure.md#fields)
    - **valor** (opcional)
      - O valor para definir a etiqueta para.
      - Esta propriedade é necessária se o **funcionamento** for _addOrReplace_ ou _Add_.

### <a name="modify-operations"></a>Modificar operações

O conjunto de propriedades de **operações** permite alterar várias tags de diferentes maneiras a partir de uma definição de política única. Cada operação é constituída por propriedades de **operação,** **campo**e **valor.** A operação determina o que a tarefa de reparação faz às etiquetas, o campo determina qual a etiqueta alterada e o valor define a nova definição para essa etiqueta. O exemplo abaixo faz as seguintes alterações de etiqueta:

- Define a `environment` etiqueta para "Teste", mesmo que já exista com um valor diferente.
- Remove a `TempResource`etiqueta .
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

A propriedade **de operação** tem as seguintes opções:

|Operação |Descrição |
|-|-|
|addOrReplace |Adiciona a etiqueta e valor definidos ao recurso, mesmo que a etiqueta já exista com um valor diferente. |
|Adicionar |Adiciona a etiqueta e o valor definidos ao recurso. |
|Remover |Remove a etiqueta definida do recurso. |

### <a name="modify-examples"></a>Modificar exemplos

Exemplo 1: `environment` Adicione a etiqueta `environment` e substitua as etiquetas existentes por "Teste":

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

Exemplo 2: `env` Retire a `environment` etiqueta e adicione `environment` a etiqueta ou substitua as etiquetas existentes por um valor parametrizado:

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

O Deny é usado para evitar um pedido de recursos que não corresponda aos padrões definidos através de uma definição de política e falha no pedido.

### <a name="deny-evaluation"></a>Negar a avaliação

Ao criar ou atualizar um recurso compatível, negue que o pedido previne antes de ser enviado para o Fornecedor de Recursos. O pedido é `403 (Forbidden)`devolvido como um . No portal, o Proibido pode ser visto como um estatuto de implantação que foi impedido pela atribuição de políticas.

Durante a avaliação dos recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como incompatíveis.

### <a name="deny-properties"></a>Negar propriedades

O efeito de negação não tem propriedades adicionais para uso na condição **então** da definição de política.

### <a name="deny-example"></a>Negar exemplo

Exemplo: Utilizando o efeito de negação.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Auditoria

A auditoria é usada para criar um evento de alerta no registo de atividade ao avaliar um recurso não conforme, mas não para o pedido.

### <a name="audit-evaluation"></a>Avaliação de auditoria

A auditoria é o último efeito verificado pela Política Azure durante a criação ou atualização de um recurso. A Política Azure envia então o recurso para o Fornecedor de Recursos. A auditoria funciona da mesma forma para um pedido de recursos e um ciclo de avaliação. A Política Azure adiciona uma `Microsoft.Authorization/policies/audit/action` operação ao registo de atividade e marca o recurso como incompatível.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito de auditoria não tem quaisquer propriedades adicionais para uso na **condição então** da definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: Utilização do efeito de auditoria.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditoriaIfNotExists

AuditoriaIfNotExists permite a auditoria de recursos que correspondam à condição **se,** mas não tem os componentes especificados nos **detalhes** da condição **então.**

### <a name="auditifnotexists-evaluation"></a>AuditoriaIfNotExists avaliação

AuditIfNotExists é executado depois de um Fornecedor de Recursos ter tratado um pedido de criação ou atualização de recursos e ter devolvido um código de estado de sucesso. A auditoria ocorre se não houver recursos relacionados ou se os recursos definidos pela **ExistenceCondition** não avaliarem verdadeiramente. A Política Azure adiciona uma `Microsoft.Authorization/policies/audit/action` operação ao registo de atividade da mesma forma que o efeito de auditoria. Quando acionado, o recurso que satisfez a condição **de se** é o recurso que é marcado como incompatível.

### <a name="auditifnotexists-properties"></a>AuditoriaIfNotExists propriedades

A propriedade **de detalhes** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados para combinar.

- **Tipo** [obrigatório]
  - Especifica o tipo de recurso relacionado a combinar.
  - Se **os detalhes.type** for um tipo de recurso por baixo do recurso de condição **se,** a política questiona recursos deste **tipo** no âmbito do recurso avaliado. Caso contrário, as consultas políticas dentro do mesmo grupo de recursos que o recurso avaliado.
- **Nome** (opcional)
  - Especifica o nome exato do recurso para combinar e faz com que a política requeira um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição valoriza para **se.field.type** **e, em seguida.details.type** match, então **o nome** torna-se _obrigatório_ e deve ser `[field('name')]`. No entanto, deve ser considerado um efeito [de auditoria.](#audit)
- **Nome do Grupo de Recursos** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se o **tipo** for um recurso que estaria por baixo do recurso **se** condicionar.
  - O padrão é **o** grupo de recursos do recurso da condição.
- **ExistênciaScope** (opcional)
  - Os valores permitidos são _Subscrição_ e _Recursos Group_.
  - Define o âmbito de onde buscar o recurso relacionado para combinar.
  - Não se aplica se o **tipo** for um recurso que estaria por baixo do recurso **se** condicionar.
  - Para o _ResourceGroup,_ limitar-se-ia ao grupo de recursos do **estado** ou ao grupo de recursos especificado no **ResourceGroupName**.
  - Para _Subscrição,_ consulta toda a subscrição do recurso relacionado.
  - Padrão é _ResourceGroup_.
- **ExistênciaCondição** (opcional)
  - Se não especificado, qualquer recurso de **tipo** relacionado satisfaz o efeito e não desencadeia a auditoria.
  - Usa a mesma linguagem que a regra da política para a condição **se,** mas é avaliado individualmente contra cada recurso relacionado.
  - Se qualquer recurso relacionado avaliar verdadeiramente, o efeito é satisfeito e não desencadeia a auditoria.
  - Pode utilizar [campo)] para verificar a equivalência com valores em condições **de caso.**
  - Por exemplo, poderia ser usado para validar que o recurso-mãe (na condição **de se)** está no mesmo local de recurso que o recurso relacionado com a correspondência.

### <a name="auditifnotexists-example"></a>AuditIfNotExiste exemplo

Exemplo: Avalia as Máquinas Virtuais para determinar se a extensão Antimalware existe edepois audita quando falta.

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

Semelhante ao AuditIfNotExists, uma definição de política DeployIfNotExists executa uma implementação do modelo quando a condição é satisfeita.

> [!NOTE]
> [Os modelos aninhados](../../../azure-resource-manager/templates/linked-templates.md#nested-template) são suportados com **implementaçãoIfNotExists,** mas os [modelos ligados](../../../azure-resource-manager/templates/linked-templates.md#linked-template) não são suportados atualmente.

### <a name="deployifnotexists-evaluation"></a>Implementar IfNotExists avaliação

O DeployIfNotExists funciona cerca de 15 minutos depois de um Fornecedor de Recursos ter tratado de um pedido de recurso de criação ou atualização e ter devolvido um código de estado de sucesso. Uma implementação do modelo ocorre se não houver recursos relacionados ou se os recursos definidos pela **ExistenceCondition** não avaliarem verdadeiramente.
A duração da implantação depende da complexidade dos recursos incluídos no modelo.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondem aos recursos são marcadas como não conformes, mas não são tomadas medidas nesse recurso. Os recursos não conformes existentes podem ser remediados com uma tarefa de [reparação.](../how-to/remediate-resources.md)

### <a name="deployifnotexists-properties"></a>Implementar Propriedades IfNotExist

A propriedade **de detalhes** do efeito DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados para combinar e a implementação do modelo para executar.

- **Tipo** [obrigatório]
  - Especifica o tipo de recurso relacionado a combinar.
  - Começa por tentar obter um recurso por baixo do recurso **se** condição, em seguida, consultas dentro do mesmo grupo de recursos que o recurso de condição **se.**
- **Nome** (opcional)
  - Especifica o nome exato do recurso para combinar e faz com que a política requeira um recurso específico em vez de todos os recursos do tipo especificado.
  - Quando a condição valoriza para **se.field.type** **e, em seguida.details.type** match, então **o nome** torna-se _obrigatório_ e deve ser `[field('name')]`.
- **Nome do Grupo de Recursos** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se o **tipo** for um recurso que estaria por baixo do recurso **se** condicionar.
  - O padrão é **o** grupo de recursos do recurso da condição.
  - Se um modelo de implantação for executado, é implantado no grupo de recursos deste valor.
- **ExistênciaScope** (opcional)
  - Os valores permitidos são _Subscrição_ e _Recursos Group_.
  - Define o âmbito de onde buscar o recurso relacionado para combinar.
  - Não se aplica se o **tipo** for um recurso que estaria por baixo do recurso **se** condicionar.
  - Para o _ResourceGroup,_ limitar-se-ia ao grupo de recursos do **estado** ou ao grupo de recursos especificado no **ResourceGroupName**.
  - Para _Subscrição,_ consulta toda a subscrição do recurso relacionado.
  - Padrão é _ResourceGroup_.
- **ExistênciaCondição** (opcional)
  - Se não especificado, qualquer recurso de **tipo** relacionado satisfaz o efeito e não desencadeia a implantação.
  - Usa a mesma linguagem que a regra da política para a condição **se,** mas é avaliado individualmente contra cada recurso relacionado.
  - Se qualquer recurso relacionado corresponder a verdade, o efeito é satisfeito e não desencadeia a implantação.
  - Pode utilizar [campo)] para verificar a equivalência com valores em condições **de caso.**
  - Por exemplo, poderia ser usado para validar que o recurso-mãe (na condição **de se)** está no mesmo local de recurso que o recurso relacionado com a correspondência.
- **roleDefinitionIds** [necessário]
  - Esta propriedade deve incluir um conjunto de cordas que correspondam à função de controlo de acesso baseado em papéis de identificação acessível pela subscrição. Para mais informações, consulte [a reparação - configure a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **ImplementaçãoScope** (opcional)
  - Os valores permitidos são _Subscrição_ e _Recursos Group_.
  - Define o tipo de implantação a acionar. _A subscrição_ indica uma [implementação ao nível de subscrição,](../../../azure-resource-manager/templates/deploy-to-subscription.md) _o ResourceGroup_ indica uma implementação para um grupo de recursos.
  - Uma propriedade de _localização_ deve ser especificada na _Implantação_ ao utilizar implementações de nível de subscrição.
  - Padrão é _ResourceGroup_.
- **Implantação** [necessária]
  - Esta propriedade deve incluir a implementação completa `Microsoft.Resources/deployments` do modelo, uma vez que seria passada para a API PUT. Para mais informações, consulte a API REST [Deployments](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade **de Implantação** são avaliadas como componentes do modelo, não a política. A exceção é a propriedade de **parâmetros** que passa valores da apólice para o modelo. O **valor** nesta secção sob um nome de parâmetro de modelo é usado para executar este valor de passagem (ver _fullDbName_ no exemplo DeployIfNotExists).

### <a name="deployifnotexists-example"></a>DeployIfNotExiste exemplo

Exemplo: Avalia as bases de dados do Servidor SQL para determinar se está ativada uma encriptação transparente do DataEncryption. Caso contrário, então é executada uma implantação para ativar.

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

Este efeito é utilizado *mode* com `Microsoft.Kubernetes.Data`um modo de definição de política de . É usado para passar as regras de controlo de admissão gatekeeper v3 definidas com [o Quadro de Restrição OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) para o Agente de [Política Aberta](https://www.openpolicyagent.org/) (OPA) para clusters Kubernetes autogeridos em Azure.

> [!NOTE]
> [A Política Azure para o Motor AKS](aks-engine.md) está na Pré-Visualização Pública e apenas suporta definições políticas incorporadas.

### <a name="enforceopaconstraint-evaluation"></a>Avaliação EnforceOPAConstraint

O controlador de admissão do Agente de Política Aberta avalia qualquer novo pedido sobre o cluster em tempo real.
A cada 5 minutos, uma varredura completa do cluster é concluída e os resultados reportados à Política Azure.

### <a name="enforceopaconstraint-properties"></a>Propriedades EnforceOPAConstraint

A propriedade **de detalhes** do efeito EnforceOPAConstraint tem as subpropriedades que descrevem a regra de controlo de admissão Gatekeeper v3.

- **modelo de restrição** [necessário]
  - O modelo de restrição CustomResourceDefinition (CRD) que define novos constrangimentos. O modelo define a lógica rego, o esquema de restrição e os parâmetros de restrição que são passados através de **valores** da Política de Azure.
- **restrição** [necessária]
  - A implementação do CRD do modelo de restrição. Utiliza parâmetros **passados** através de valores como. `{{ .Values.<valuename> }}` No exemplo abaixo, este `{{ .Values.cpuLimit }}` `{{ .Values.memoryLimit }}`seria e .
- **valores** [opcional]
  - Define quaisquer parâmetros e valores para passar para a Restrição. Cada valor deve existir no CRD do modelo de restrição.

### <a name="enforceopaconstraint-example"></a>Exemplo de EnforceOPAConstraint

Exemplo: Regra de controlo de admissão gatekeeper v3 para definir cpU de contentor e limites de recursos de memória no motor AKS.

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

## <a name="enforceregopolicy"></a>Política EnforceRego

Este efeito é utilizado *mode* com `Microsoft.ContainerService.Data`um modo de definição de política de . É usado para passar as regras de controlo de admissão Gatekeeper v2 definidas com [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) para [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) no Serviço [Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [A Política Azure para AKS](rego-for-aks.md) está em Pré-visualização Limitada e apenas suporta definições políticas incorporadas

### <a name="enforceregopolicy-evaluation"></a>Avaliação EnforceRegoPolicy

O controlador de admissão do Agente de Política Aberta avalia qualquer novo pedido sobre o cluster em tempo real.
A cada 5 minutos, uma varredura completa do cluster é concluída e os resultados reportados à Política Azure.

### <a name="enforceregopolicy-properties"></a>Propriedades EnforceRegoPolicy

A propriedade **de detalhes** do efeito EnforceRegoPolicy tem as subpropriedades que descrevem a regra de controlo de admissão Gatekeeper v2.

- **policyId** [necessário]
  - Um nome único passou como parâmetro para a regra de controlo de admissão rego.
- **política** [necessária]
  - Especifica o URI da regra de controlo de admissão rego.
- **políticaParâmetros** [opcional]
  - Define quaisquer parâmetros e valores para passar para a política de rego.

### <a name="enforceregopolicy-example"></a>Exemplo de Política EnforceRego

Exemplo: Regra de controlo de admissão gatekeeper v2 para permitir apenas as imagens especificadas do recipiente no AKS.

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

Um recurso pode ser impactado por várias atribuições. Estas atribuições podem estar no mesmo âmbito ou em diferentes âmbitos. Cada uma destas atribuições também é suscetível de ter um efeito diferente definido. A condição e o efeito para cada política são avaliados de forma independente. Por exemplo:

- Política 1
  - Restringe a localização dos recursos a 'westus'
  - Atribuído à subscrição A
  - Efeito negar
- Política 2
  - Restringe a localização dos recursos a 'eastus'
  - Atribuído ao grupo de recursos B na subscrição A
  - Efeito de auditoria
  
Esta configuração resultaria no seguinte resultado:

- Qualquer recurso já no grupo de recursos B em "eastus" está em conformidade com a política 2 e não está em conformidade com a política 1
- Qualquer recurso já no grupo de recursos B que não esteja no "leste" não está em conformidade com a política 2 e não está em conformidade com a política 1, se não em "westus"
- Qualquer novo recurso na subscrição A não em 'westus' é negado pela política 1
- Qualquer novo recurso na subscrição A e no grupo de recursos B em 'westus' é criado e não conforme na política 2

Se tanto a política 1 como a política 2 tiveram efeito de negação, a situação muda para:

- Qualquer recurso já no grupo de recursos B que não esteja no "leste" não está em conformidade com a política 2
- Qualquer recurso já no grupo de recursos B que não esteja em 'westus' não está em conformidade com a política 1
- Qualquer novo recurso na subscrição A não em 'westus' é negado pela política 1
- Qualquer novo recurso no grupo de recursos B da subscrição A é negado

Cada atribuição é avaliada individualmente. Como tal, não há uma oportunidade para um recurso escapar a uma lacuna das diferenças de âmbito. Considera-se que o resultado líquido das políticas de camadas ou da sobreposição de políticas é **considerado cumulativo mais restritivo.** A título de exemplo, se tanto a política 1 como a 2 tivessem um efeito de negação, um recurso seria bloqueado pelas políticas sobrepostas e conflituosas. Se ainda precisar do recurso para ser criado no âmbito-alvo, reveja as exclusões de cada atribuição para validar as políticas certas estão a afetar os âmbitos certos.

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)
