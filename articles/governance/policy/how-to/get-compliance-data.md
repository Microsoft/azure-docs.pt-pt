---
title: Obter dados de conformidade de políticas
description: Determinam a conformidade e efeitos de avaliações de política do Azure. Saiba como obter os detalhes de conformidade dos seus recursos Azure.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280641"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obtenha dados de conformidade dos recursos do Azure

Um dos maiores benefícios da Política Azure é a perspicácia e controloque fornece sobre recursos num grupo de subscrição ou [gestão](../../management-groups/overview.md) de subscrições. Esse controle pode ser realizado em muitas formas diferentes, tais como impedir os recursos a ser criados no lugar errado, impor a utilização de etiqueta comum e consistente, ou apropriado de auditorias recursos existentes para configurações e definições. Em todos os casos, os dados são gerados pela Política Azure para que possa compreender o estado de conformidade do seu ambiente.

Existem várias formas de acessar as informações de conformidade geradas pela sua política e as atribuições de iniciativa:

- Usando o [portal Azure](#portal)
- Através da [scripting](#command-line) da linha de comando

Antes de examinar os métodos para gerar relatórios sobre compatibilidade, vamos ver quando as informações de conformidade são atualizadas e a frequência e eventos que disparam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver a ser reportado como **não registado,** verifique se o Fornecedor de Recursos **Microsoft.PolicyInsights** está registado e que o utilizador tem as permissões adequadas de controlo de acesso baseados em funções (RBAC), conforme descrito no [RBAC na Política Azure](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Acionadores de avaliação

Os resultados de um ciclo de avaliação concluído estão disponíveis no Fornecedor de Recursos `Microsoft.PolicyInsights` através de operações `PolicyStates` e `PolicyEvents`. Para obter mais informações sobre as operações da Política Azure REST API, consulte [azure Policy Insights](/rest/api/policy-insights/).

Avaliações de políticas atribuídas e iniciativas de acontecem como resultado de vários eventos:

- Uma política ou iniciativa recentemente é atribuída a um âmbito. Demora cerca de 30 minutos para a atribuição a ser aplicado ao âmbito definido. Uma vez aplicada, o ciclo de avaliação começa para recursos dentro daquele escopo em relação a política atribuída recentemente ou a iniciativa e consoante os efeitos utilizados pela política ou iniciativa, recursos são marcados como compatíveis ou incompatíveis. Uma política de grandes ou iniciativa avaliada em comparação com um âmbito grande de recursos pode demorar tempo. Assim, não há nenhuma expectativa predefinida de quando o ciclo de avaliação será concluída. Depois de terminar, resultados de compatibilidade atualizados estão disponíveis no portal e SDKs.

- Uma política ou iniciativa já atribuído a um âmbito é atualizada. O ciclo de avaliação e o tempo para este cenário é igual de uma nova atribuição a um âmbito.

- Um recurso é implementado para um âmbito com uma atribuição através do Resource Manager, do REST, da CLI do Azure ou do Azure PowerShell. Neste cenário, o evento de efeito (acrescentar, auditoria, negar, implementar) e informações de estado de conformidade para o recurso individual ficarem disponíveis no portal e SDKs de cerca de 15 minutos mais tarde. Este evento não causa uma edição de avaliação de outros recursos.

- Ciclo de avaliação de conformidade. Uma vez a cada 24 horas, atribuições automaticamente são reavaliadas. Uma grande política ou iniciativa de muitos recursos pode demorar tempo, portanto, não há nenhuma expectativa predefinida de quando a avaliação ciclo será concluída. Depois de terminar, resultados de compatibilidade atualizados estão disponíveis no portal e SDKs.

- O fornecedor de recursos de [Configuração de Hóspedes](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerido.

- Análise a pedido

### <a name="on-demand-evaluation-scan"></a>Análise de avaliação a pedido

Uma análise de avaliação para uma subscrição ou um grupo de recursos pode ser iniciada com uma chamada à REST API. Esta análise é um processo assíncrono. Como tal, o ponto final REST para iniciar a análise não tem de aguardar até que a verificação estiver concluída, a responder. Em vez disso, ele fornece um URI para consultar o estado da avaliação pedida.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourRG}` - Substitua o nome do seu grupo de recursos
- `{subscriptionId}` - substituir pelo ID da subscrição

A análise suporta a avaliação dos recursos numa subscrição ou num grupo de recursos. Inicie uma varredura por âmbito com um comando REST API **POST** utilizando as seguintes estruturas URI:

- Subscrição

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Grupo de recursos

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

A chamada devolve um estatuto **aceite em 202.** Incluído no cabeçalho de resposta é uma propriedade **de Localização** com o seguinte formato:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` é gerado estáticamente para o âmbito solicitado. Se um âmbito já estiver a executar uma análise a pedido, uma nova análise não iniciou. Em vez disso, o novo pedido é fornecido com a mesma `{ResourceContainerGUID}` **localização** URI para o estado. Um comando REST API **GET** para o **Local** URI devolve um **202 Aceito** enquanto a avaliação está em curso. Quando a avaliação estiver concluída, devolve um estatuto de **200 OK.** O corpo de uma verificação concluídos é uma resposta JSON com o estado:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Como funciona a conformidade

Numa atribuição, um recurso não **é compatível** se não seguir as regras políticas ou de iniciativa.
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

Neste exemplo, é preciso ter cautela riscos de segurança. Agora que criou uma atribuição de política, esta é avaliada para todas as contas de armazenamento no grupo de recursos de ContosoRG. Audita as três contas de armazenamento não conformes, alterando consequentemente os seus Estados para **não conformes.**

![Auditado contas de armazenamento em não conformidades](../media/getting-compliance-data/resource-group03.png)

Além de **conformes** e **incumpridores,** as políticas e recursos têm três outros Estados:

- **Conflituoso**: Existem duas ou mais políticas com regras contraditórias. Por exemplo, duas políticas acrescentando a mesma etiqueta com valores diferentes.
- **Não iniciado**: O ciclo de avaliação ainda não começou para a política ou recurso.
- **Não registado**: O Fornecedor de Recursos políticos do Azure não foi registado ou a conta registada não tem permissão para ler dados de conformidade.

A Política Azure utiliza os **campos de tipo** e **nome** na definição para determinar se um recurso é compatível. Quando o recurso corresponde, é considerado aplicável e tem um estatuto de **Conforme** ou **Não Conforme**. Se qualquer **tipo** ou **nome** for o único imóvel na definição, então todos os recursos são considerados aplicáveis e avaliados.

A percentagem de conformidade é determinada dividindo os recursos **conformes** por _recursos totais._
_Os recursos totais_ são definidos como a soma dos recursos **conformes,** **incompatíveis**e **conflituosos.** Os números globais de conformidade são a soma de recursos distintos que são **compatíveis** divididos pela soma de todos os recursos distintos. Na imagem abaixo, existem 20 recursos distintos que são aplicáveis e apenas um é **incompatível.** Conformidade geral do recurso é 95% (19 de 20).

![Exemplo de conformidade de política a partir da página compliance](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portal

O portal do Azure apresenta uma experiência gráfica de visualizar e compreender o estado de conformidade no seu ambiente. Na página **Política,** a opção **Overview** fornece detalhes sobre os âmbitos disponíveis sobre o cumprimento de políticas e iniciativas. Juntamente com o estado de conformidade e a contagem por atribuição, contém um gráfico que mostra a conformidade nos últimos sete dias. A página **compliance** contém grande parte desta mesma informação (exceto o gráfico), mas fornece opções adicionais de filtragem e triagem.

![Exemplo da página de conformidade com a política do Azure](../media/getting-compliance-data/compliance-page.png)

Uma vez que uma política ou iniciativa pode ser atribuída a âmbitos diferentes, a tabela inclui o âmbito para cada atribuição e o tipo de definição de que foi atribuída. O número de recursos em não conformidade e políticas de conformidade para cada atribuição também é fornecido. Clicar numa política ou iniciativa na tabela fornece uma análise mais profunda a conformidade para essa atribuição particular.

![Exemplo da página de Detalhes de Conformidade política do Azure](../media/getting-compliance-data/compliance-details.png)

A lista de recursos no separador de conformidade de **recursos** mostra o estado de avaliação dos recursos existentes para a atribuição atual. O separador não é **compatível,** mas pode ser filtrado.
Os eventos (apêndice, auditoria, negação, implantação) desencadeados pelo pedido de criação de um recurso são mostrados ao abrigo do separador **Eventos.**

> [!NOTE]
> Para uma política de motores AKS, o recurso mostrado é o grupo de recursos.

![Exemplo de eventos de conformidade com a política do Azure](../media/getting-compliance-data/compliance-events.png)

Para os recursos do modo Fornecedor de [Recursos,](../concepts/definition-structure.md#resource-provider-modes) no separador de conformidade do **Recurso,** selecionando o recurso ou clicando à direita na linha e selecionando os detalhes de conformidade do **View** abre os detalhes de conformidade do componente. Esta página também oferece separadores para ver as políticas que são atribuídas a este recurso, eventos, eventos de componentes e alterar histórico.

![Exemplo de detalhes de conformidade da componente de política azure](../media/getting-compliance-data/compliance-components.png)

De volta à página de conformidade de recursos, clique à direita na linha do evento que deseja reunir mais detalhes e selecionar **registos**de atividade do Show . Página de registo de atividade é aberto e é previamente filtrada para a pesquisa que mostra os detalhes para a atribuição e os eventos. O registo de atividades fornece o contexto adicional e informações sobre esses eventos.

![Exemplo do Registo de Atividade de Conformidade de Política Azure](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Compreender o incumprimento

<a name="change-history-preview"></a>

Quando um recursos é determinado a **não cumprir,** existem muitas razões possíveis. Para determinar a razão pela qual um recurso não está **em conformidade** ou para encontrar a alteração responsável, consulte determinar o [incumprimento](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comandos

As mesmas informações disponíveis no portal podem ser recuperadas com a Rest API (incluindo com [armClient),](https://github.com/projectkudu/ARMClient)Azure PowerShell e Azure CLI (pré-visualização).
Para mais detalhes sobre a API REST, consulte a referência [azure Policy Insights.](/rest/api/policy-insights/) As páginas de referência da REST API tem um 'Tente It' botão verde em cada operação que permite que experimente diretamente no navegador.

Utilize o ARMClient ou uma ferramenta similar para manusear a autenticação no Azure para os exemplos REST API.

### <a name="summarize-results"></a>Resultados resumidos

Com a API REST, resumo pode ser efetuado por contentor, definição ou atribuição. Aqui está um exemplo de resumição a nível de subscrição usando o resumo da Visão de Política Azure [para subscrição:](/rest/api/policy-insights/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A saída resume a subscrição. Na saída de exemplo abaixo, a conformidade resumida está em **valor.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Este pedido fornece mais detalhes, incluindo cada atribuição que formavam os números de não conformidade e as informações de definição para cada atribuição. Cada objeto político na hierarquia fornece uma **consultaResultsUri** que pode ser usada para obter detalhes adicionais a esse nível.

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

No exemplo acima, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornece uma amostra Uri para todos os recursos não conformes para uma definição de política específica. Olhando para o valor **$filter,** isCompliant é igual (eq) a falso, PolicyAssignmentId é especificado para a definição de política, e, em seguida, o próprio PolicyDefinitionId. O motivo para incluir o PolicyAssignmentId no filtro é porque o PolicyDefinitionId poderia existe na política, várias ou atribuições de iniciativa com âmbitos diferentes. Ao especificar o PolicyAssignmentId e o PolicyDefinitionId, podemos pode ser explícitos nos resultados que estamos procurando. Anteriormente, para os PolicyStates usámos **o mais recente**, que automaticamente define uma janela de **e** **para o** tempo das últimas 24 horas.

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

Quando um recurso é criado ou atualizado, é gerado um resultado de avaliação da política. Os resultados são _chamados eventos políticos._ Utilize o Uri seguinte para ver eventos recentes de política associados à subscrição.

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

Para obter mais informações sobre consulta de eventos políticos, consulte o artigo de referência da [Azure Policy Events.](/rest/api/policy-insights/policyevents)

### <a name="azure-powershell"></a>Azure PowerShell

O módulo Azure PowerShell para a Política Azure está disponível na PowerShell Gallery como [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Utilizando o PowerShellGet, pode instalar o módulo utilizando `Install-Module -Name Az.PolicyInsights` (certifique-se de que tem o mais recente [Azure PowerShell](/powershell/azure/install-az-ps) instalado):

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

Exemplo: Obter o estado de resumo para a política atribuída superior com o maior número de recursos não compatíveis.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: Obter o registo de estado para o máximo avaliou recentemente recursos (a predefinição é por timestamp em ordem decrescente).

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

Exemplo: Obter os detalhes de todos os recursos de rede virtual não compatível.

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

Exemplo: Obter eventos relacionados com os recursos de rede virtual não compatível que ocorreram após uma data específica.

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

O campo **PrincipalOid** pode ser utilizado para obter um utilizador específico com o cmdlet Azure PowerShell `Get-AzADUser`. Substitua **{principalOid}** com a resposta que obtém do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Se tiver um espaço de [trabalho de Log Analytics](../../../log-analytics/log-analytics-overview.md) com `AzureActivity` da [solução Activity Log Analytics](../../../azure-monitor/platform/activity-log-collect.md) ligada à sua subscrição, também pode ver resultados de incumprimento do ciclo de avaliação utilizando consultas simples de Kusto e a tabela `AzureActivity`. Com detalhes nos registos do Monitor Azure, os alertas podem ser configurados para ter cuidado com o incumprimento.


![Conformidade política azure usando registos do Monitor Azure](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Aprenda a [remediar recursos não conformes](remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)