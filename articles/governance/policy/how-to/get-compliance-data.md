---
title: Obter dados de conformidade de política
description: Determinam a conformidade e efeitos de avaliações de política do Azure. Saiba como obter os detalhes de conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: ff50619d7b3d5bc803e8ee8d9e4cbf4389a4191f
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978093"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obter dados de conformidade de recursos do Azure

Um dos maiores benefícios do Azure Policy é as informações e os controles fornece sobre os recursos numa subscrição ou [grupo de gestão](../../management-groups/overview.md) das subscrições. Esse controle pode ser realizado em muitas formas diferentes, tais como impedir os recursos a ser criados no lugar errado, impor a utilização de etiqueta comum e consistente, ou apropriado de auditorias recursos existentes para configurações e definições. Em todos os casos, os dados são gerados pelo Azure Policy para permitir que você compreenda o estado de conformidade do seu ambiente.

Existem várias formas de acessar as informações de conformidade geradas pela sua política e as atribuições de iniciativa:

- Usando o [portal do Azure](#portal)
- Por meio [linha de comandos](#command-line) scripts

Antes de examinar os métodos para gerar relatórios sobre compatibilidade, vamos ver quando as informações de conformidade são atualizadas e a frequência e eventos que disparam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver sendo relatado como **não registrado**, verifique se o provedor de recursos **Microsoft. PolicyInsights** está registrado e se o usuário tem as permissões RBAC (controle de acesso baseado em função) apropriadas, conforme descrito em [RBAC in Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Acionadores de avaliação

Os resultados de um ciclo de avaliação concluída estão disponíveis no `Microsoft.PolicyInsights` fornecedor de recursos por meio `PolicyStates` e `PolicyEvents` operações. Para obter mais informações sobre as operações da API REST do Azure Policy insights, consulte [Azure Policy insights](/rest/api/policy-insights/).

Avaliações de políticas atribuídas e iniciativas de acontecem como resultado de vários eventos:

- Uma política ou iniciativa recentemente é atribuída a um âmbito. Demora cerca de 30 minutos para a atribuição a ser aplicado ao âmbito definido. Uma vez aplicada, o ciclo de avaliação começa para recursos dentro daquele escopo em relação a política atribuída recentemente ou a iniciativa e consoante os efeitos utilizados pela política ou iniciativa, recursos são marcados como compatíveis ou incompatíveis. Uma política de grandes ou iniciativa avaliada em comparação com um âmbito grande de recursos pode demorar tempo. Assim, não há nenhuma expectativa predefinida de quando o ciclo de avaliação será concluída. Depois de terminar, resultados de compatibilidade atualizados estão disponíveis no portal e SDKs.

- Uma política ou iniciativa já atribuído a um âmbito é atualizada. O ciclo de avaliação e o tempo para este cenário é igual de uma nova atribuição a um âmbito.

- Um recurso é implementado para um âmbito com uma atribuição através do Resource Manager, do REST, da CLI do Azure ou do Azure PowerShell. Neste cenário, o evento de efeito (acrescentar, auditoria, negar, implementar) e informações de estado de conformidade para o recurso individual ficarem disponíveis no portal e SDKs de cerca de 15 minutos mais tarde. Este evento não causa uma edição de avaliação de outros recursos.

- Ciclo de avaliação de conformidade. Uma vez a cada 24 horas, atribuições automaticamente são reavaliadas. Uma grande política ou iniciativa de muitos recursos pode demorar tempo, portanto, não há nenhuma expectativa predefinida de quando a avaliação ciclo será concluída. Depois de terminar, resultados de compatibilidade atualizados estão disponíveis no portal e SDKs.

- O provedor de recursos de [configuração de convidado](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerenciado.

- Análise a pedido

### <a name="on-demand-evaluation-scan"></a>Análise de avaliação a pedido

Uma análise de avaliação para uma subscrição ou um grupo de recursos pode ser iniciada com uma chamada à REST API. Esta análise é um processo assíncrono. Como tal, o ponto final REST para iniciar a análise não tem de aguardar até que a verificação estiver concluída, a responder. Em vez disso, ele fornece um URI para consultar o estado da avaliação pedida.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourRG}` -Substituir pelo nome do seu grupo de recursos
- `{subscriptionId}` - substituir pelo ID da subscrição

A análise suporta a avaliação dos recursos numa subscrição ou num grupo de recursos. Iniciar uma verificação pelo âmbito de aplicação com uma API REST **POST** comando utilizando as estruturas URI seguintes:

- Subscrição

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupo de recursos

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A chamada retorna um **aceite 202** estado. Incluído na resposta de cabeçalho é um **localização** propriedade com o seguinte formato:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` estaticamente é gerado para o âmbito de pedido. Se um âmbito já estiver a executar uma análise a pedido, uma nova análise não iniciou. Em vez disso, o novo pedido é fornecido o mesmo `{ResourceContainerGUID}` **localização** URI para o estado. Uma API REST **Obtenha** comando para o **localização** URI retorna um **aceite 202** enquanto a avaliação está em curso. Quando a análise de avaliação foi concluída, ele retorna um **200 OK** estado. O corpo de uma verificação concluídos é uma resposta JSON com o estado:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Como funciona a conformidade

Numa atribuição, é um recurso **em não conformidade** se ele não seguir regras de política ou iniciativa.
A tabela seguinte mostra como diferente política efeitos trabalham com a avaliação de condição para o estado de conformidade resultante:

| Estado do recurso | Efeito | Avaliação da política | Estado de conformidade |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Compatível |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Novo | Audit, AuditIfNotExist\* | Falso | Compatível |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

Por exemplo, suponha que tem um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas em vermelho), que são expostas a redes públicas.

![Contas de armazenamento expostas a redes públicas](../media/getting-compliance-data/resource-group01.png)

Neste exemplo, é preciso ter cautela riscos de segurança. Agora que criou uma atribuição de política, esta é avaliada para todas as contas de armazenamento no grupo de recursos de ContosoRG. Auditorias de contas de armazenamento em não conformidades três conseqüentemente alterar seus Estados para **em não conformidade.**

![Auditado contas de armazenamento em não conformidades](../media/getting-compliance-data/resource-group03.png)

Além disso **compatível** e **em não conformidade**, políticas e de recursos tem três outros Estados:

- Em **conflito**: Existem duas ou mais políticas com regras conflitantes. Por exemplo, duas políticas acrescentando a mesma etiqueta com valores diferentes.
- **Não iniciado**: O ciclo de avaliação não foi iniciado para a política ou o recurso.
- **Não registrado**: O provedor de recursos de Azure Policy não foi registrado ou a conta conectada não tem permissão para ler os dados de conformidade.

Azure Policy usa os campos de **tipo** e **nome** na definição para determinar se um recurso é uma correspondência. Quando o recurso corresponde, é considerado aplicável e tem o estado deles **compatível** ou **em não conformidade**. Se qualquer um dos **tipo** ou **nome** é a única propriedade na definição, em seguida, todos os recursos são considerados aplicável e são avaliados.

A percentagem de conformidade é determinada pela divisão **compatível** recursos por _total de recursos_.
_Total de recursos_ é definido como a soma do **compatível**, **em não conformidade**, e **em conflito** recursos. Os números de conformidade geral são a soma dos recursos distintos que sejam **compatível** dividido pela soma de todos os recursos distintos. Na imagem abaixo, existem 20 recursos distintos que são aplicáveis e é apenas um **em não conformidade**. Conformidade geral do recurso é 95% (19 de 20).

![Exemplo de conformidade de política da página conformidade](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

O portal do Azure apresenta uma experiência gráfica de visualizar e compreender o estado de conformidade no seu ambiente. Sobre o **política** página, o **descrição geral** opção fornece detalhes para âmbitos disponíveis na conformidade das políticas e iniciativas. Juntamente com o estado de conformidade e a contagem por atribuição, contém um gráfico que mostra a conformidade nos últimos sete dias. O **conformidade** página contém grande parte essas mesmas informações (exceto o gráfico), mas fornecem opções de ordenação e filtragem adicionais.

![Exemplo de página de conformidade Azure Policy](../media/getting-compliance-data/compliance-page.png)

Uma vez que uma política ou iniciativa pode ser atribuída a âmbitos diferentes, a tabela inclui o âmbito para cada atribuição e o tipo de definição de que foi atribuída. O número de recursos em não conformidade e políticas de conformidade para cada atribuição também é fornecido. Clicar numa política ou iniciativa na tabela fornece uma análise mais profunda a conformidade para essa atribuição particular.

![Exemplo de Azure Policy página de detalhes de conformidade](../media/getting-compliance-data/compliance-details.png)

A lista de recursos sobre o **conformidade do recurso** separador mostra o estado de avaliação dos recursos existentes para a atribuição atual. A guia assume a predefinição **em não conformidade**, mas pode ser filtrada.
Eventos (acrescentar, auditoria, negar, implementar) acionada pelo pedido para criar um recurso são apresentados sob a **eventos** separador.

![Exemplo de eventos de conformidade de Azure Policy](../media/getting-compliance-data/compliance-events.png)

Com o botão direito na linha do evento que pretende recolher mais detalhes sobre e selecione **Mostrar registos de atividades**. Página de registo de atividade é aberto e é previamente filtrada para a pesquisa que mostra os detalhes para a atribuição e os eventos. O registo de atividades fornece o contexto adicional e informações sobre esses eventos.

![Exemplo de log de atividades de conformidade Azure Policy](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Entender a não conformidade

<a name="change-history-preview"></a>

Quando um recurso é determinado como **não compatível**, há muitas razões possíveis. Para determinar o motivo pelo qual um recurso **não está em conformidade** ou para localizar a alteração responsável, consulte [determinar a não conformidade](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comandos

As mesmas informações disponíveis no portal podem ser obtidas com a API REST (incluindo com [ARMClient](https://github.com/projectkudu/ARMClient)) ou o Azure PowerShell. Para obter detalhes completos sobre a API REST, consulte a referência do [Azure Policy insights](/rest/api/policy-insights/) . As páginas de referência da REST API tem um 'Tente It' botão verde em cada operação que permite que experimente diretamente no navegador.

Para utilizar os exemplos a seguir no Azure PowerShell, construa um token de autenticação com este código de exemplo. Em seguida, substitua o $restUri a cadeia de caracteres nos exemplos para recuperar um objeto JSON que pode então ser analisado.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Resultados resumidos

Com a API REST, resumo pode ser efetuado por contentor, definição ou atribuição. Aqui está um exemplo de resumo no nível da assinatura usando o resumo do Azure Policy Insight [para a assinatura](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A saída resume a subscrição. Na saída do exemplo abaixo, a compatibilidade resumida estão sob **value.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Este pedido fornece mais detalhes, incluindo cada atribuição que formavam os números de não conformidade e as informações de definição para cada atribuição. Cada objeto de política na hierarquia fornece um **queryResultsUri** que pode ser usado para obter detalhes adicionais nesse nível.

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

### <a name="query-for-resources"></a>Consulta para os recursos

No exemplo acima, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornece um exemplo de Uri para todos os recursos em não conformidades para obter uma definição de política específica. Olhando para o **$filter** valor, IsCompliant é igual (eq) como false, PolicyAssignmentId é especificado para a definição de política e, em seguida, o PolicyDefinitionId em si. O motivo para incluir o PolicyAssignmentId no filtro é porque o PolicyDefinitionId poderia existe na política, várias ou atribuições de iniciativa com âmbitos diferentes. Ao especificar o PolicyAssignmentId e o PolicyDefinitionId, podemos pode ser explícitos nos resultados que estamos procurando. Anteriormente, para PolicyStates utilizámos **mais recente**, que define automaticamente um **partir** e **para** janela de tempo das últimas 24-horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

A resposta de exemplo abaixo tem sido removida para um único recurso de não conformidade para fins de brevidade. A resposta detalhada tem várias partes de dados sobre o recurso, a política ou iniciativa e a atribuição. Tenha em atenção que também pode ver quais os parâmetros atribuição foram transmitidos para a definição de política.

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

Quando um recurso é criado ou atualizado, é gerado um resultado de avaliação da política. Os resultados são chamados _eventos política_. Utilize o Uri seguinte para ver eventos recentes de política associados à subscrição.

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
A utilização do PowerShellGet, pode instalar o módulo usando `Install-Module -Name Az.PolicyInsights` (Certifique-se de que tem a versão mais recente [Azure PowerShell](/powershell/azure/install-az-ps) instalado):

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

Exemplo: Obter o resumo de estado para a política atribuída mais alta com o maior número de recursos sem conformidade.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: Obtendo o registro de estado para o recurso avaliado mais recentemente (o padrão é por carimbo de data/hora em ordem decrescente).

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

Exemplo: Obtendo os detalhes de todos os recursos de rede virtual não compatíveis.

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

Exemplo: Obtenção de eventos relacionados a recursos de rede virtual não compatíveis que ocorreram após uma data específica.

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

O **PrincipalOid** campo pode ser utilizado para obter um utilizador específico com o cmdlet do PowerShell do Azure `Get-AzADUser`. Substitua **{principalOid}** com a resposta do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Se você tiver um [espaço de trabalho log Analytics](../../../log-analytics/log-analytics-overview.md) com `AzureActivity` da [solução análise do log de atividades](../../../azure-monitor/platform/activity-log-collect.md) vinculada à sua assinatura, também poderá exibir resultados de não conformidade do ciclo de avaliação usando consultas simples de Kusto e a tabela `AzureActivity`. Com detalhes em logs de Azure Monitor, os alertas podem ser configurados para observar a não conformidade.


![Azure Policy conformidade usando logs de Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).