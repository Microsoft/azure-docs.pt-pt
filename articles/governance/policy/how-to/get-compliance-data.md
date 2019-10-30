---
title: Obter dados de conformidade de política
description: Azure Policy avaliações e efeitos determinam a conformidade. Saiba como obter os detalhes de conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 47258f27f44b6a21c5da72e4631591e695024400
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053270"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obter dados de conformidade de recursos do Azure

Um dos maiores benefícios do Azure Policy é o Insight e os controles que ele fornece sobre recursos em uma assinatura ou [grupo de gerenciamento](../../management-groups/overview.md) de assinaturas. Esse controle pode ser exercido de várias maneiras diferentes, como a prevenção de recursos que estão sendo criados no local errado, a imposição de uso de marca comum e consistente ou a auditoria de recursos existentes para configurações e definições apropriadas. Em todos os casos, os dados são gerados pelo Azure Policy para permitir que você compreenda o estado de conformidade do seu ambiente.

Há várias maneiras de acessar as informações de conformidade geradas pelas suas atribuições de política e iniciativa:

- Usando o [portal do Azure](#portal)
- Por meio de scripts de [linha de comando](#command-line)

Antes de examinar os métodos de relatório sobre conformidade, vamos examinar quando as informações de conformidade são atualizadas e a frequência e os eventos que disparam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver sendo relatado como **não registrado**, verifique se o provedor de recursos **Microsoft. PolicyInsights** está registrado e se o usuário tem as permissões RBAC (controle de acesso baseado em função) apropriadas, conforme descrito em [RBAC in Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Gatilhos de avaliação

Os resultados de um ciclo de avaliação concluído estão disponíveis no provedor de recursos de `Microsoft.PolicyInsights` por meio de operações de `PolicyStates` e `PolicyEvents`. Para obter mais informações sobre as operações da API REST do Azure Policy insights, consulte [Azure Policy insights](/rest/api/policy-insights/).

As avaliações de políticas e iniciativas atribuídas acontecem como resultado de vários eventos:

- Uma política ou iniciativa é atribuída recentemente a um escopo. Leva cerca de 30 minutos para que a atribuição seja aplicada ao escopo definido. Depois que ele é aplicado, o ciclo de avaliação começa para os recursos dentro desse escopo em relação à política ou iniciativa atribuída recentemente e, dependendo dos efeitos usados pela política ou iniciativa, os recursos são marcados como em conformidade ou sem conformidade. Uma grande política ou iniciativa avaliada em relação a um grande escopo de recursos pode levar tempo. Assim, não há uma expectativa predefinida de quando o ciclo de avaliação será concluído. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no portal e nos SDKs.

- Uma política ou iniciativa já atribuída a um escopo é atualizada. O ciclo de avaliação e o tempo para esse cenário são os mesmos para uma nova atribuição a um escopo.

- Um recurso é implantado em um escopo com uma atribuição por meio do Resource Manager, REST, CLI do Azure ou Azure PowerShell. Nesse cenário, o evento de efeito (acréscimo, auditoria, negação, implantação) e informações de status em conformidade para o recurso individual se tornam disponíveis no portal e nos SDKs em cerca de 15 minutos depois. Esse evento não causa uma avaliação de outros recursos.

- Ciclo de avaliação de conformidade padrão. Uma vez a cada 24 horas, as atribuições são reavaliadas automaticamente. Uma grande política ou iniciativa de muitos recursos pode levar tempo, portanto, não há nenhuma expectativa predefinida de quando o ciclo de avaliação será concluído. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no portal e nos SDKs.

- O provedor de recursos de [configuração de convidado](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerenciado.

- Varredura sob demanda

### <a name="on-demand-evaluation-scan"></a>Verificação de avaliação sob demanda

Uma verificação de avaliação para uma assinatura ou um grupo de recursos pode ser iniciada com uma chamada para a API REST. Essa verificação é um processo assíncrono. Assim, o ponto de extremidade REST para iniciar a verificação não aguarda até que a verificação seja concluída para responder. Em vez disso, ele fornece um URI para consultar o status da avaliação solicitada.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourRG}`-substituir pelo nome do seu grupo de recursos
- `{subscriptionId}` - substituir pelo ID da subscrição

A verificação dá suporte à avaliação de recursos em uma assinatura ou em um grupo de recursos. Inicie uma verificação por escopo com um comando **post** da API REST usando as seguintes estruturas de URI:

- Subscrição

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupo de recursos

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A chamada retorna um status de **202 aceito** . Incluído no cabeçalho de resposta há uma propriedade **Location** com o seguinte formato:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` é gerada estaticamente para o escopo solicitado. Se um escopo já estiver executando uma varredura sob demanda, uma nova verificação não será iniciada. Em vez disso, a nova solicitação é fornecida com o mesmo URI de **local** de `{ResourceContainerGUID}` para o status. Um comando **Get** da API REST para o URI do **local** retorna um **202 aceito** enquanto a avaliação é contínua. Quando a verificação de avaliação for concluída, ela retornará um status de **200 OK** . O corpo de uma verificação concluída é uma resposta JSON com o status:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Como funciona a conformidade

Em uma atribuição, um recurso não é **compatível** se não seguir as regras de política ou de iniciativa.
A tabela a seguir mostra como os diferentes efeitos de política funcionam com a avaliação de condição para o estado de conformidade resultante:

| Estado do recurso | Efeito | Avaliação de política | Estado de conformidade |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Em conformidade |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Novo | Audit, AuditIfNotExist\* | Falso | Em conformidade |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

Por exemplo, suponha que você tenha um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas em vermelho) que são expostas para redes públicas.

![Contas de armazenamento expostas a redes públicas](../media/getting-compliance-data/resource-group01.png)

Neste exemplo, você precisa estar atento a riscos de segurança. Agora que você criou uma atribuição de política, ela é avaliada para todas as contas de armazenamento no grupo de recursos ContosoRG. Ele audita as três contas de armazenamento não compatíveis, consequentemente alterando seus Estados para **sem conformidade.**

![Auditoria de contas de armazenamento sem conformidade](../media/getting-compliance-data/resource-group03.png)

Além de estar em **conformidade** e **sem conformidade**, as políticas e os recursos têm três outros Estados:

- **Conflito**: existem duas ou mais políticas com regras conflitantes. Por exemplo, duas políticas acrescentando a mesma marca com valores diferentes.
- **Não iniciado**: o ciclo de avaliação não foi iniciado para a política ou o recurso.
- **Não registrado**: o provedor de recursos Azure Policy não foi registrado ou a conta conectada não tem permissão para ler os dados de conformidade.

Azure Policy usa os campos de **tipo** e **nome** na definição para determinar se um recurso é uma correspondência. Quando o recurso corresponde, ele é considerado aplicável e tem um status de em **conformidade** ou **sem conformidade**. Se o **tipo** ou o **nome** for a única propriedade na definição, todos os recursos serão considerados aplicáveis e serão avaliados.

A porcentagem de conformidade é determinada pela divisão de recursos em **conformidade** pelo _total de recursos_.
O _total de recursos_ é definido como a soma dos recursos **compatíveis**, **não compatíveis**e **conflitantes** . Os números de conformidade geral são a soma dos recursos distintos que são **compatíveis** divididos pela soma de todos os recursos distintos. Na imagem abaixo, há 20 recursos distintos que são aplicáveis e apenas um deles **não está em conformidade**. A conformidade geral dos recursos é de 95% (19 de 20).

![Exemplo de conformidade de política da página conformidade](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

A portal do Azure demonstra uma experiência gráfica de visualização e compreensão do estado de conformidade em seu ambiente. Na página **política** , a opção **visão geral** fornece detalhes de escopos disponíveis sobre a conformidade de políticas e iniciativas. Junto com o estado de conformidade e a contagem por atribuição, ele contém um gráfico mostrando a conformidade nos últimos sete dias. A página **conformidade** contém muitas dessas mesmas informações (exceto o gráfico), mas fornecem opções adicionais de filtragem e classificação.

![Exemplo de página de conformidade Azure Policy](../media/getting-compliance-data/compliance-page.png)

Como uma política ou iniciativa pode ser atribuída a escopos diferentes, a tabela inclui o escopo de cada atribuição e o tipo de definição que foi atribuído. O número de recursos não compatíveis e as políticas não compatíveis para cada atribuição também são fornecidos. Clicar em uma política ou iniciativa na tabela fornece uma análise mais profunda da conformidade para essa atribuição específica.

![Exemplo de Azure Policy página de detalhes de conformidade](../media/getting-compliance-data/compliance-details.png)

A lista de recursos na guia **conformidade de recursos** mostra o status de avaliação dos recursos existentes para a atribuição atual. A guia usa como padrão **não conformidade**, mas pode ser filtrada.
Os eventos (acréscimo, auditoria, negação, implantação) disparados pela solicitação para criar um recurso são mostrados na guia **eventos** .

![Exemplo de eventos de conformidade de Azure Policy](../media/getting-compliance-data/compliance-events.png)

Clique com o botão direito do mouse na linha do evento para o qual você gostaria de reunir mais detalhes e selecione **Mostrar logs de atividade**. A página log de atividades é aberta e filtrada previamente para a pesquisa mostrando os detalhes da atribuição e os eventos. O log de atividades fornece contexto adicional e informações sobre esses eventos.

![Exemplo de log de atividades de conformidade Azure Policy](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Entender a não conformidade

<a name="change-history-preview"></a>

Quando um recurso é determinado como **não compatível**, há muitas razões possíveis. Para determinar o motivo pelo qual um recurso **não está em conformidade** ou para localizar a alteração responsável, consulte [determinar a não conformidade](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comandos

As mesmas informações disponíveis no portal podem ser recuperadas com a API REST (incluindo with [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell e CLI do Azure (Preview).
Para obter detalhes completos sobre a API REST, consulte a referência do [Azure Policy insights](/rest/api/policy-insights/) . As páginas de referência da API REST têm um botão "Experimente" verde em cada operação que permite que você experimente diretamente no navegador.

Use o ARMClient ou uma ferramenta semelhante para lidar com a autenticação do Azure para os exemplos da API REST.

### <a name="summarize-results"></a>Resumir resultados

Com a API REST, o resumo pode ser executado por contêiner, definição ou atribuição. Aqui está um exemplo de resumo no nível da assinatura usando o resumo do Azure Policy Insight [para a assinatura](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A saída resume a assinatura. Na saída de exemplo abaixo, a conformidade resumida está em **Value. Results. nonCompliantResources** e **Value. Results. nonCompliantPolicies**. Essa solicitação fornece mais detalhes, incluindo cada atribuição que compõe os números não compatíveis e as informações de definição de cada atribuição. Cada objeto de política na hierarquia fornece um **queryResultsUri** que pode ser usado para obter detalhes adicionais nesse nível.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Consulta de recursos

No exemplo acima, **Value. policyAssignments. policyDefinitions. Results. queryResultsUri** fornece um exemplo de URI para todos os recursos sem conformidade para uma definição de política específica. Observando o valor de **$Filter** , IsCompliant é igual (EQ) a false, PolicyAssignmentId é especificado para a definição de política e, em seguida, o próprio PolicyDefinitionId. O motivo para incluir o PolicyAssignmentId no filtro é porque o PolicyDefinitionId pode existir em várias atribuições de política ou iniciativa com escopos diferentes. Ao especificar PolicyAssignmentId e PolicyDefinitionId, podemos ser explícitos nos resultados que procuramos. Anteriormente, para PolicyStates, usamos o **mais recente**, que define automaticamente uma janela **de** e **para** hora das últimas 24 horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

A resposta de exemplo abaixo foi cortada para um único recurso não compatível para fins de brevidade. A resposta detalhada tem várias partes de dados sobre o recurso, a política, a iniciativa e a atribuição. Observe que você também pode ver quais parâmetros de atribuição foram passados para a definição de política.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Ver eventos

Quando um recurso é criado ou atualizado, um resultado da avaliação de política é gerado. Os resultados são chamados de _eventos de política_. Use o URI a seguir para exibir eventos de política recentes associados à assinatura.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Os resultados assemelham-se ao seguinte exemplo:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Para obter mais informações sobre como consultar eventos de política, consulte o artigo de referência de [eventos de Azure Policy](/rest/api/policy-insights/policyevents) .

### <a name="azure-powershell"></a>Azure PowerShell

O módulo Azure PowerShell para Azure Policy está disponível no Galeria do PowerShell como [AZ. PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Usando o PowerShellGet, você pode instalar o módulo usando `Install-Module -Name Az.PolicyInsights` (verifique se você tem as [Azure PowerShell](/powershell/azure/install-az-ps) mais recentes instaladas):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

O módulo tem os seguintes cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exemplo: obtendo o resumo de estado para a política atribuída mais alta com o número mais alto de recursos sem conformidade.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: obtendo o registro de estado para o recurso avaliado mais recentemente (o padrão é por carimbo de data/hora em ordem decrescente).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obtendo os detalhes de todos os recursos de rede virtual não compatíveis.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obtendo eventos relacionados a recursos de rede virtual sem conformidade que ocorreram após uma data específica.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

O campo **PrincipalOid** pode ser usado para obter um usuário específico com o cmdlet Azure PowerShell `Get-AzADUser`. Substitua **{principalOid}** pela resposta obtida do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Se você tiver um [espaço de trabalho log Analytics](../../../log-analytics/log-analytics-overview.md) com `AzureActivity` da [solução análise do log de atividades](../../../azure-monitor/platform/activity-log-collect.md) vinculada à sua assinatura, também poderá exibir os resultados de não conformidade do ciclo de avaliação usando consultas simples de Kusto e a tabela `AzureActivity`. Com detalhes em logs de Azure Monitor, os alertas podem ser configurados para observar a não conformidade.


![Azure Policy conformidade usando logs de Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).