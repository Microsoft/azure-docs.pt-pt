---
title: Obter dados de conformidade de políticas
description: As avaliações e efeitos da Política Azure determinam a conformidade. Saiba como obter os detalhes de conformidade dos seus recursos Azure.
ms.date: 05/20/2020
ms.topic: how-to
ms.openlocfilehash: 55f0b471eff15140de0a586fd5d326d9cd913b1a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747090"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obtenha dados de conformidade dos recursos do Azure

Um dos maiores benefícios da Política Azure é a perspicácia e controloque fornece sobre recursos num grupo de subscrição ou [gestão](../../management-groups/overview.md) de subscrições. Este controlo pode ser exercido de várias maneiras diferentes, tais como evitar que os recursos sejam criados no local errado, impor o uso de etiquetas comuns e consistentes, ou auditar os recursos existentes para configurações e configurações apropriadas. Em todos os casos, os dados são gerados pela Política Azure para que possa compreender o estado de conformidade do seu ambiente.

Existem várias formas de aceder às informações de conformidade geradas pela sua política e atribuição de iniciativas:

- Usando o [portal Azure](#portal)
- Através da [scripting](#command-line) da linha de comando

Antes de analisar os métodos para reportar sobre o cumprimento, vamos ver quando a informação de conformidade é atualizada e a frequência e eventos que desencadeiam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver a ser reportado como **não registado,** verifique se o Fornecedor de Recursos **Microsoft.PolicyInsights** está registado e que o utilizador tem as permissões adequadas de controlo de acesso baseados em funções (RBAC), conforme descrito no [RBAC na Política Azure](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Gatilhos de avaliação

Os resultados de um ciclo de avaliação concluído estão disponíveis no Fornecedor de `Microsoft.PolicyInsights` Recursos `PolicyStates` através e `PolicyEvents` operações. Para obter mais informações sobre as operações da Política Azure REST API, consulte [azure Policy Insights](/rest/api/policy-insights/).

As avaliações das políticas e iniciativas atribuídas acontecem em resultado de diversos eventos:

- Uma política ou iniciativa é recentemente atribuída a um âmbito. Leva cerca de 30 minutos para a atribuição ser aplicada ao âmbito definido. Uma vez aplicado, o ciclo de avaliação começa por recursos dentro desse âmbito contra a política ou iniciativa recém-atribuída e dependendo dos efeitos usados pela política ou iniciativa, os recursos são marcados como conformes ou não conformes. Uma grande política ou iniciativa avaliada contra um grande âmbito de recursos pode levar tempo. Como tal, não há expectativa pré-definida de quando o ciclo de avaliação termina. Uma vez concluído, os resultados atualizados de conformidade estão disponíveis no portal e SDKs.

- É atualizada uma política ou iniciativa já atribuída a um âmbito de aplicação. O ciclo de avaliação e o calendário para este cenário são os mesmos que para uma nova atribuição a um âmbito.

- Um recurso é implantado para um âmbito com uma atribuição via Resource Manager, REST, Azure CLI ou Azure PowerShell. Neste cenário, o evento de efeito (apêndice, auditoria, negar, implementar) e informação de estado conforme para o recurso individual fica disponível no portal e SDKs cerca de 15 minutos depois. Este evento não causa uma avaliação de outros recursos.

- Ciclo padrão de avaliação de conformidade. Uma vez a cada 24 horas, as atribuições são automaticamente reavaliadas. Uma grande política ou iniciativa de muitos recursos pode levar tempo, por isso não há expectativa pré-definida de quando o ciclo de avaliação terminar. Uma vez concluído, os resultados atualizados de conformidade estão disponíveis no portal e SDKs.

- O fornecedor de recursos de [Configuração de Hóspedes](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerido.

- Digitalização a pedido

### <a name="on-demand-evaluation-scan"></a>On-demand evaluation scan (Análise de avaliação a pedido)

Uma avaliação para uma subscrição ou um grupo de recursos pode ser iniciada com o Azure PowerShell ou uma chamada para a API REST. Esta tomografia é um processo assíncrono.

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Avaliação a pedido - Azure PowerShell

A varredura de conformidade é iniciada com o [cmdlet Start-AzPolicyComplianceScan.](/powershell/module/az.policyinsights/start-azpolicycompliancescan)

Por predefinição, `Start-AzPolicyComplianceScan` inicia uma avaliação para todos os recursos na subscrição atual. Para iniciar uma avaliação num grupo de recursos específico, utilize o parâmetro **ResourceGroupName.** O exemplo seguinte inicia uma análise de conformidade na subscrição atual do grupo de recursos _MyRG:_

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName MyRG
```

Pode pedir à PowerShell que achamada assíncrona esteja concluída antes de fornecer a saída dos resultados ou executá-la em segundo plano como [um trabalho](/powershell/module/microsoft.powershell.core/about/about_jobs). Para utilizar um trabalho powerShell para executar a verificação de conformidade em segundo plano, use o parâmetro **AsJob** e defina o valor para um objeto, como `$job` neste exemplo:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Pode verificar o estado do trabalho verificando o `$job` objeto. O trabalho é do `Microsoft.Azure.Commands.Common.AzureLongRunningJob` tipo. Utilize `Get-Member` no objeto para ver as propriedades e `$job` métodos disponíveis.

Enquanto a verificação de conformidade está em execução, verificando os resultados das saídas do `$job` objeto como estes:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Quando a varredura de conformidade estiver concluída, a propriedade **do Estado** muda para _Concluída_.

#### <a name="on-demand-evaluation-scan---rest"></a>Avaliação a pedido - REST

Como um processo assíncrono, o ponto final do REST para iniciar a varredura não espera até que a digitalização esteja completa para responder. Em vez disso, fornece um URI para consultar o estado da avaliação solicitada.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourRG}`- Substitua pelo nome do seu grupo de recursos
- `{subscriptionId}` - substituir pelo ID da subscrição

A varredura suporta a avaliação de recursos numa subscrição ou num grupo de recursos. Inicie uma varredura por âmbito com um comando REST API **POST** utilizando as seguintes estruturas URI:

- Subscrição

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Grupo de recursos

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

A chamada devolve um estatuto **aceite em 202.** Incluído no cabeçalho de resposta é uma propriedade **de Localização** com o seguinte formato:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}`é gerado estáticamente para o âmbito solicitado. Se já está a fazer uma varredura a pedido, não é iniciado um novo exame. Em vez disso, o novo pedido é fornecido o mesmo `{ResourceContainerGUID}` **local** URI para o estado. Um comando REST API **GET** para o **Local** URI devolve um **202 Aceito** enquanto a avaliação está em curso. Quando a avaliação estiver concluída, devolve um estatuto de **200 OK.** O corpo de uma varredura completa é uma resposta JSON com o estatuto:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Como funciona a conformidade

Numa atribuição, um recurso não **é compatível** se não seguir as regras políticas ou de iniciativa.
O quadro seguinte mostra como diferentes efeitos políticos funcionam com a avaliação da condição para o estado de conformidade resultante:

| Estado de recursos | Efeito | Avaliação política | Estado de conformidade |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Compatível |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Novo | Audit, AuditIfNotExist\* | Falso | Compatível |

\* Os efeitos de Append, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja TRUE.
Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

Por exemplo, assuma que tem um grupo de recursos – ContsoRG, com algumas contas de armazenamento (destacadas a vermelho) que estão expostas a redes públicas.

:::image type="content" source="../media/getting-compliance-data/resource-group01.png" alt-text="Contas de armazenamento expostas a redes públicas" border="false":::

Neste exemplo, é preciso ter cuidado com os riscos de segurança. Agora que criou uma atribuição de políticas, é avaliada para todas as contas de armazenamento no grupo de recursos ContosoRG. Audita as três contas de armazenamento não conformes, alterando consequentemente os seus Estados para **não conformes.**

:::image type="content" source="../media/getting-compliance-data/resource-group03.png" alt-text="Contas de armazenamento não conformes auditadas" border="false":::

Além de **conformes** e **incumpridores,** as políticas e recursos têm três outros Estados:

- **Conflituoso**: Existem duas ou mais políticas com regras contraditórias. Por exemplo, duas políticas que adem a mesma etiqueta com valores diferentes.
- **Não iniciado**: O ciclo de avaliação ainda não começou para a política ou recurso.
- **Não registado**: O Fornecedor de Recursos políticos do Azure não foi registado ou a conta registada não tem permissão para ler dados de conformidade.

A Política Azure utiliza os **campos de tipo** e **nome** na definição para determinar se um recurso é compatível. Quando o recurso corresponde, é considerado aplicável e tem um estatuto de **Conforme** ou **Não Conforme**. Se qualquer **tipo** ou **nome** for o único imóvel na definição, então todos os recursos são considerados aplicáveis e avaliados.

A percentagem de conformidade é determinada dividindo os recursos **conformes** por _recursos totais._
_Os recursos totais_ são definidos como a soma dos recursos **conformes,** **incompatíveis**e **conflituosos.** Os números globais de conformidade são a soma de recursos distintos que são **compatíveis** divididos pela soma de todos os recursos distintos. Na imagem abaixo, existem 20 recursos distintos que são aplicáveis e apenas um é **incompatível.** A conformidade global com os recursos é de 95% (19 em 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Exemplo de conformidade de política a partir da página compliance" border="false":::

## <a name="portal"></a>Portal

O portal Azure apresenta uma experiência gráfica de visualização e compreensão do estado de conformidade no seu ambiente. Na página **Política,** a opção **Overview** fornece detalhes sobre os âmbitos disponíveis sobre o cumprimento de políticas e iniciativas. Juntamente com o estado de conformidade e a contagem por atribuição, contém um gráfico que mostra a conformidade nos últimos sete dias. A página **compliance** contém grande parte desta mesma informação (exceto o gráfico), mas fornece opções adicionais de filtragem e triagem.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Exemplo da página de conformidade com a política do Azure" border="false":::

Uma vez que uma política ou iniciativa pode ser atribuída a diferentes âmbitos, a tabela inclui a margem para cada atribuição e o tipo de definição que foi atribuído. O número de recursos não conformes e políticas não conformes para cada atribuição também são fornecidos. Clicar numa política ou iniciativa na tabela fornece uma análise mais profunda do cumprimento dessa atribuição em particular.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Exemplo da página de Detalhes de Conformidade política do Azure" border="false":::

A lista de recursos no separador de conformidade de **recursos** mostra o estado de avaliação dos recursos existentes para a atribuição atual. O separador não é **compatível,** mas pode ser filtrado.
Os eventos (apêndice, auditoria, negação, implantação) desencadeados pelo pedido de criação de um recurso são mostrados ao abrigo do separador **Eventos.**

> [!NOTE]
> Para uma política de motores AKS, o recurso mostrado é o grupo de recursos.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Exemplo de eventos de conformidade com a política do Azure" border="false":::

Para os recursos do modo Fornecedor de [Recursos,](../concepts/definition-structure.md#resource-provider-modes) no separador de conformidade do **Recurso,** selecionando o recurso ou clicando à direita na linha e selecionando os detalhes de conformidade do **View** abre os detalhes de conformidade do componente. Esta página também oferece separadores para ver as políticas que são atribuídas a este recurso, eventos, eventos de componentes e alterar histórico.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Exemplo de detalhes de conformidade da componente de política azure" border="false":::

De volta à página de conformidade de recursos, clique à direita na linha do evento que deseja reunir mais detalhes e selecionar **registos**de atividade do Show . A página de registo de atividade abre e é pré-filtrada para a pesquisa mostrando detalhes para a atribuição e os eventos. O registo de atividade fornece contexto e informação adicional sobre esses eventos.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Exemplo do Registo de Atividade de Conformidade de Política Azure" border="false":::

### <a name="understand-non-compliance"></a>Compreender o incumprimento

Quando um recurso é determinado a **não ser compatível,** existem muitas razões possíveis. Para determinar a razão pela qual um recurso não está **em conformidade** ou para encontrar a alteração responsável, consulte determinar o [incumprimento](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comandos

As mesmas informações disponíveis no portal podem ser recuperadas com a Rest API (incluindo com [armClient),](https://github.com/projectkudu/ARMClient)Azure PowerShell e Azure CLI (pré-visualização).
Para mais detalhes sobre a API REST, consulte a referência [azure Policy Insights.](/rest/api/policy-insights/) As páginas de referência REST API têm um botão verde 'Try It' em cada operação que lhe permite experimentá-lo corretamente no navegador.

Utilize o ARMClient ou uma ferramenta similar para manusear a autenticação no Azure para os exemplos REST API.

### <a name="summarize-results"></a>Resumir resultados

Com a API REST, a resumição pode ser realizada por recipiente, definição ou atribuição. Aqui está um exemplo de resumição a nível de subscrição usando o resumo da Visão de Política Azure [para subscrição:](/rest/api/policy-insights/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

A saída resume a subscrição. Na saída de exemplo abaixo, a conformidade resumida está em **valor.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Este pedido fornece mais detalhes, incluindo cada atribuição que compõem os números não conformes e as informações de definição para cada atribuição. Cada objeto político na hierarquia fornece uma **consultaResultsUri** que pode ser usada para obter detalhes adicionais a esse nível.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Consulta de recursos

No exemplo acima, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornece uma amostra Uri para todos os recursos não conformes para uma definição de política específica. Olhando para o valor **$filter,** isCompliant é igual (eq) a falso, PolicyAssignmentId é especificado para a definição de política, e, em seguida, o próprio PolicyDefinitionId. A razão para incluir o PolicyAssignmentId no filtro é porque o PolicyDefinitionId pode existir em várias missões políticas ou de iniciativa com diferentes âmbitos. Especificando tanto o PolicyAssignmentId como o PolicyDefinitionId, podemos ser explícitos nos resultados que procuramos. Anteriormente, para os PolicyStates usámos **o mais recente**, que automaticamente define uma janela de **e** **para o** tempo das últimas 24 horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

A resposta de exemplo abaixo foi aparada a um único recurso não conforme para a brevidade. A resposta detalhada tem vários dados sobre o recurso, a política ou iniciativa, e a atribuição. Note que também pode ver que parâmetros de atribuição foram passados para a definição de política.

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

Quando um recurso é criado ou atualizado, um resultado de avaliação de políticas é gerado. Os resultados são _chamados eventos políticos._ Utilize o seguinte Uri para ver os recentes eventos políticos associados à subscrição.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
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

Exemplo: Obter o resumo do Estado para a política mais elevada atribuída com o maior número de recursos não conformes.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: Obter o registo estatal do recurso mais recentemente avaliado (o padrão é por carimbo temporal em ordem descendente).

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

Exemplo: Obter os detalhes para todos os recursos de rede virtual não conformes.

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

Exemplo: Obtenção de eventos relacionados com recursos de rede virtuais não conformes que ocorreram após uma data específica.

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

O campo **PrincipalOid** pode ser utilizado para obter um utilizador específico com o cmdlet Azure PowerShell `Get-AzADUser` . Substitua **{principalOid}** com a resposta que obtém do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Se tiver um espaço de trabalho log [Analytics](../../../log-analytics/log-analytics-overview.md) com `AzureActivity` a [solução Activity Log Analytics](../../../azure-monitor/platform/activity-log-collect.md) ligada à sua subscrição, também pode ver resultados de incumprimento do ciclo de avaliação utilizando simples consultas Kusto e a `AzureActivity` tabela. Com detalhes nos registos do Monitor Azure, os alertas podem ser configurados para ter cuidado com o incumprimento.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Conformidade política azure usando registos do Monitor Azure" border="false":::

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Aprenda a [remediar recursos não conformes](remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)