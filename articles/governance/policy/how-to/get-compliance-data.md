---
title: Obtenha dados de conformidade com a política
description: Avaliações e efeitos da Política Azure determinam a conformidade. Saiba como obter os detalhes de conformidade dos seus recursos Azure.
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2b4db7daf75f153cadb03e5dd028084e311bb874
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91596045"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obtenha dados de conformidade dos recursos da Azure

Um dos maiores benefícios da Azure Policy é a perceção e controlos que fornece sobre recursos em um [grupo](../../management-groups/overview.md) de subscrições ou subscrições de subscrição. Este controlo pode ser exercido de muitas maneiras diferentes, tais como impedir que os recursos sejam criados no local errado, impor a utilização comum e consistente da etiqueta, ou auditar os recursos existentes para configurações e configurações apropriadas. Em todos os casos, os dados são gerados pela Azure Policy para que possa compreender o estado de conformidade do seu ambiente.

Existem várias formas de aceder às informações de conformidade geradas pelas suas atribuições de política e iniciativa:

- Usando o [portal Azure](#portal)
- Através da scripting [da linha](#command-line) de comando

Antes de analisar os métodos para relatar a conformidade, vamos olhar quando a informação de conformidade é atualizada e a frequência e eventos que desencadeiam um ciclo de avaliação.

> [!WARNING]
> Se o estado de conformidade estiver a ser reportado como **Não registado,** verifique se o Fornecedor de Recursos **Microsoft.PolicyInsights** está registado e que o utilizador tem as permissões adequadas de controlo de acesso baseado em funções Azure (Azure RBAC), tal como descrito nas [permissões Azure RBAC na Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Gatilhos de avaliação

Os resultados de um ciclo de avaliação concluído estão disponíveis no `Microsoft.PolicyInsights` Fornecedor de Recursos através e `PolicyStates` `PolicyEvents` operações. Para obter mais informações sobre as operações da Azure Policy Insights REST API, consulte [Azure Policy Insights](/rest/api/policy-insights/).

As avaliações das políticas e iniciativas atribuídas acontecem em resultado de diversos eventos:

- Uma política ou iniciativa é recentemente atribuída a um âmbito de aplicação. Demora cerca de 30 minutos para que a atribuição seja aplicada ao âmbito definido. Uma vez aplicado, o ciclo de avaliação começa para recursos dentro desse âmbito contra a política ou iniciativa recém-atribuída e dependendo dos efeitos usados pela política ou iniciativa, os recursos são marcados como conformes, não conformes ou isentos. Uma grande política ou iniciativa avaliada contra um vasto âmbito de recursos pode levar tempo. Como tal, não há expectativa pré-definida de quando o ciclo de avaliação termina. Uma vez concluído, os resultados de conformidade atualizados estão disponíveis no portal e nos SDKs.

- Uma política ou iniciativa já atribuída a um âmbito é atualizada. O ciclo de avaliação e o timing para este cenário é o mesmo que para uma nova atribuição a um âmbito.

- Um recurso é implementado ou atualizado dentro de um âmbito com uma atribuição através de Azure Resource Manager, REST API ou um SDK suportado. Neste cenário, o evento de efeito (apêndice, auditoria, negação, implementação) e informação de estado conforme para o recurso individual fica disponível no portal e SDKs cerca de 15 minutos depois. Este evento não causa uma avaliação de outros recursos.

- É criada, atualizada ou eliminada [uma isenção de política.](../concepts/exemption-structure.md) Neste cenário, a atribuição correspondente é avaliada para o âmbito de isenção definido.

- Ciclo de avaliação padrão de conformidade. Uma vez a cada 24 horas, as atribuições são automaticamente reavaliadas. Uma grande política ou iniciativa de muitos recursos pode levar tempo, por isso não há expectativa pré-definida de quando o ciclo de avaliação termina. Uma vez concluído, os resultados de conformidade atualizados estão disponíveis no portal e nos SDKs.

- O fornecedor de recursos [de Configuração de Hóspedes](../concepts/guest-configuration.md) é atualizado com detalhes de conformidade por um recurso gerido.

- Digitalização a pedido

### <a name="on-demand-evaluation-scan"></a>On-demand evaluation scan (Análise de avaliação a pedido)

Uma verificação de avaliação para uma subscrição ou um grupo de recursos pode ser iniciada com Azure CLI, Azure PowerShell, uma chamada para a API REST, ou usando o [Azure Policy Compliance Scan GitHub Action](https://github.com/marketplace/actions/azure-policy-compliance-scan).
Esta tomografia é um processo assíncronos.

#### <a name="on-demand-evaluation-scan---github-action"></a>Exame de avaliação a pedido - GitHub Action

Utilize a [ação Azure Policy Compliance Scan](https://github.com/marketplace/actions/azure-policy-compliance-scan) para desencadear uma avaliação a pedido do seu fluxo de [trabalho GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) em um ou vários recursos, grupos de recursos ou subscrições, e feche o fluxo de trabalho com base no estado de conformidade dos recursos. Também pode configurar o fluxo de trabalho para funcionar numa hora programada para obter o mais recente estado de conformidade num momento conveniente. Opcionalmente, esta ação do GitHub pode gerar um relatório sobre o estado de conformidade dos recursos digitalizados para posterior análise ou para arquivamento.

O exemplo a seguir executa uma verificação de conformidade para uma subscrição. 

```yaml
on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

Para obter mais informações e amostras de fluxo de trabalho, consulte o [GitHub Action for Azure Policy Compliance Scan .](https://github.com/Azure/policy-compliance-scan)

#### <a name="on-demand-evaluation-scan---azure-cli"></a>Exame de avaliação a pedido - Azure CLI

A verificação de conformidade é iniciada com o comando [de verificação do estado de disparo do estado az.](/cli/azure/policy/state#az-policy-state-trigger-scan)

Por padrão, `az policy state trigger-scan` inicia uma avaliação para todos os recursos na subscrição atual. Para iniciar uma avaliação num grupo de recursos específico, utilize o parâmetro **do grupo de recursos.** O exemplo a seguir inicia uma verificação de conformidade na subscrição atual do grupo de recursos _MyRG:_

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

Pode optar por não esperar que o processo assíncronos esteja concluído antes de continuar com o parâmetro **de não-espera.**

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>Exame de avaliação a pedido - Azure PowerShell

A verificação de conformidade é iniciada com o [cmdlet Start-AzPolicyComplianceScan.](/powershell/module/az.policyinsights/start-azpolicycompliancescan)

Por padrão, `Start-AzPolicyComplianceScan` inicia uma avaliação para todos os recursos na subscrição atual. Para iniciar uma avaliação num grupo de recursos específico, utilize o parâmetro **ResourceGroupName.** O exemplo a seguir inicia uma verificação de conformidade na subscrição atual do grupo de recursos _MyRG:_

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

Pode pedir à PowerShell que aguarde que a chamada assíncronos esteja concluída antes de fornecer a saída dos resultados ou que seja executada em segundo plano como [trabalho](/powershell/module/microsoft.powershell.core/about/about_jobs). Para utilizar uma função PowerShell para executar a verificação de conformidade em segundo plano, utilize o parâmetro **AsJob** e descreva o valor num objeto, como `$job` neste exemplo:

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

Pode verificar o estado do trabalho verificando o `$job` objeto. O trabalho é do `Microsoft.Azure.Commands.Common.AzureLongRunningJob` tipo. Utilize `Get-Member` no objeto para ver propriedades e `$job` métodos disponíveis.

Enquanto a verificação de conformidade está em execução, verificando os `$job` resultados das saídas do objeto, tais como estes:

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

Quando a verificação de conformidade estiver concluída, a propriedade **do Estado** muda para _Concluída._

#### <a name="on-demand-evaluation-scan---rest"></a>Exame de avaliação a pedido - REST

Como um processo assíncronos, o ponto final DO REST para iniciar a varredura não espera até que a varredura esteja completa para responder. Em vez disso, fornece um URI para consultar o estado da avaliação solicitada.

Em cada URI da API REST, existem variáveis que são utilizadas que precisa de substituir pelos seus próprios valores:

- `{YourRG}` - Substitua pelo nome do seu grupo de recursos
- `{subscriptionId}` - substituir pelo ID da subscrição

A digitalização suporta a avaliação de recursos numa subscrição ou num grupo de recursos. Inicie uma verificação por âmbito com um comando REST API **POST** utilizando as seguintes estruturas URI:

- Subscrição

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Grupo de recursos

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

A chamada devolve um estatuto **202 Aceite.** Incluído no cabeçalho de resposta é uma propriedade **localização** com o seguinte formato:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}` é gerada estáticamente para o âmbito solicitado. Se um âmbito já está a ser feito a pedido, ainda não foi iniciado um novo exame. Em vez disso, o novo pedido é fornecido no mesmo `{ResourceContainerGUID}` **local** uri para o estatuto. Um comando REST API **GET** para o **Mente** URI devolve um **202 Aceite** enquanto a avaliação está em curso. Quando a avaliação estiver concluída, retorna um estado **de 200 OK.** O corpo de uma varredura completa é uma resposta JSON com o estado:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Como funciona a conformidade

Numa atribuição, um recurso é **incompatível se** não seguir regras de política ou iniciativa e não estiver _isento._ O quadro a seguir mostra como os diferentes efeitos políticos funcionam com a avaliação da condição para o estado de conformidade resultante:

| Estado dos recursos | Efeito | Avaliação de políticas | Estado de conformidade |
| --- | --- | --- | --- |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Existe | Negar, Auditar, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Compatível |
| Novo | Audit, AuditIfNotExist\* | Verdadeiro | Incompatível |
| Novo | Audit, AuditIfNotExist\* | Falso | Compatível |

\* Os efeitos Modificar, Apêndice, DeployIfNotExist e AuditIfNotExist requerem que a declaração IF seja VERDADEIRA. Os efeitos também necessitam que a condição de existência seja FALSE para estarem em não conformidade. Quando for TRUE, a condição IF aciona a avaliação da condição de existência dos recursos relacionados.

Por exemplo, assuma que tem um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas a vermelho) que estão expostas a redes públicas.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::
   Diagrama mostrando imagens de cinco contas de armazenamento no grupo de recursos Contoso R G.  As contas de armazenamento um e três são azuis, enquanto as contas de armazenamento 2, 4 e 5 são vermelhas.
:::image-end:::

Neste exemplo, é preciso ter cuidado com os riscos de segurança. Agora que criou uma atribuição de políticas, é avaliada para todas as contas de armazenamento incluídas e não isentas no grupo de recursos ContosoRG. Audita as três contas de armazenamento não conformes, alterando assim os seus Estados para **não conformes.**

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::
   Diagrama mostrando imagens de cinco contas de armazenamento no grupo de recursos Contoso R G. As contas de armazenamento um e três têm agora marcas verdes por baixo delas, enquanto as contas de armazenamento dois, quatro e cinco têm agora sinais de aviso vermelhos por baixo deles.
:::image-end:::

Além **de conformes** e **não conformes,** as políticas e recursos têm outros quatro Estados:

- **Isento**: O recurso está no âmbito de uma atribuição, mas tem uma [isenção definida.](../concepts/exemption-structure.md)
- **Conflituoso:** Existem duas ou mais definições políticas com regras contraditórias. Por exemplo, duas definições anexam a mesma etiqueta com valores diferentes.
- **Não iniciado:** O ciclo de avaliação ainda não começou para a política ou recurso.
- **Não registado**: O Fornecedor de Recursos de Política Azure não foi registado ou a conta iniciada não tem permissão para ler dados de conformidade.

A Política Azure utiliza os campos **de tipo** e **nome** na definição para determinar se um recurso é compatível. Quando o recurso corresponde, é considerado aplicável e tem um estatuto de **Conforme,** **Não Conforme,** Ou **Isento**. Se qualquer **um dos tipos** ou **nomes** for o único imóvel na definição, então todos os recursos incluídos e não isentos são considerados aplicáveis e são avaliados.

A percentagem de conformidade é determinada dividindo recursos **conformes** e **isentos** por _recursos totais._ _Os recursos totais_ são definidos como a soma dos recursos **conformes,** **não conformes,** **isentos**e **contraditórios.** Os números globais de conformidade são a soma de recursos distintos que são **conformes** ou **isentos** divididos pela soma de todos os recursos distintos. Na imagem abaixo, existem 20 recursos distintos que são aplicáveis e apenas um é **incompatível**.
A conformidade global com os recursos é de 95% (19 em 20).

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

> [!NOTE]
> Conformidade Regulamentar na Política Azure é uma funcionalidade de pré-visualização. As propriedades de conformidade da SDK e as páginas no portal são diferentes para iniciativas habilitadas. Para mais informações, consulte [Conformidade Regulamentar](../concepts/regulatory-compliance.md)

## <a name="portal"></a>Portal

O portal Azure apresenta uma experiência gráfica de visualização e compreensão do estado de conformidade no seu ambiente. Na página **Política,** a opção **'Vista Geral'** fornece detalhes sobre os âmbitos disponíveis sobre o cumprimento de políticas e iniciativas. Juntamente com o estado de conformidade e a contagem por atribuição, contém um gráfico que mostra o cumprimento ao longo dos últimos sete dias. A página **Compliance** contém grande parte desta mesma informação (exceto o gráfico), mas fornece opções adicionais de filtragem e triagem.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

Uma vez que uma política ou iniciativa pode ser atribuída a diferentes âmbitos, a tabela inclui o âmbito para cada atribuição e o tipo de definição que foi atribuído. O número de recursos não conformes e políticas não conformes para cada atribuição também são fornecidos. A seleção de uma política ou iniciativa na tabela proporciona um olhar mais aprofundado sobre o cumprimento dessa atribuição em particular.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

A lista de recursos no separador **de conformidade de recursos** mostra o estado de avaliação dos recursos existentes para a atribuição atual. O separador não **está em conformidade,** mas pode ser filtrado.
Os eventos (apêndice, auditoria, negação, implementação) desencadeados pelo pedido de criação de um recurso são apresentados no separador **Eventos.**

> [!NOTE]
> Para uma política do motor AKS, o recurso mostrado é o grupo de recursos.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

Para os recursos [do modo Fornecedor de Recursos,](../concepts/definition-structure.md#resource-provider-modes) no separador conformidade de **Recursos,** selecionar o recurso ou clicar à direita na linha e selecionar **Os detalhes de conformidade do Ver** abre os detalhes de conformidade do componente. Esta página também oferece separadores para ver as políticas que são atribuídas a este recurso, eventos, eventos componentes e alterar o histórico.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

De volta à página de conformidade de recursos, clique com o botão direito na linha do evento que pretende recolher mais detalhes e selecione **Iniciar registos de atividades**. A página de registo de atividade abre e é pré-filtrada para a pesquisa mostrando detalhes para a atribuição e os eventos. O registo de atividades fornece contexto e informação adicional sobre esses eventos.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

### <a name="understand-non-compliance"></a>Compreender o incumprimento

Quando um recurso é determinado **como incompatível, existem**muitas razões possíveis. Para determinar a razão pela qual um recurso **não está em conformidade** ou para encontrar a alteração responsável, consulte determinar o [incumprimento](./determine-non-compliance.md).

## <a name="command-line"></a>Linha de comandos

As mesmas informações disponíveis no portal podem ser recuperadas com a API REST (incluindo com [ARMClient),](https://github.com/projectkudu/ARMClient)Azure PowerShell e Azure CLI. Para obter mais informações sobre a API REST, consulte a referência [Azure Policy Insights.](/rest/api/policy-insights/) As páginas de referência da API REST têm um botão verde 'Try It' em cada operação que lhe permite experimentá-lo diretamente no navegador.

Utilize o ARMClient ou uma ferramenta semelhante para manusear a autenticação em Azure para os exemplos de API REST.

### <a name="summarize-results"></a>Resumir resultados

Com a API REST, a resumo pode ser realizada por recipiente, definição ou atribuição. Aqui está um exemplo de resumo ao nível da subscrição utilizando o Resumo da Azure Policy Insight [para subscrição:](/rest/api/policy-insights/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

A saída resume a subscrição. Na produção de exemplo abaixo, a conformidade resumida está em **valor.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Este pedido fornece mais detalhes, incluindo cada atribuição que compõem os números não conformes e as informações de definição para cada atribuição. Cada objeto de política na hierarquia fornece uma **consultaResultsUri** que pode ser usada para obter detalhes adicionais a esse nível.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Consulta de recursos

No exemplo acima, **value.policyAssignments.policyDefinitions.results.consultaResultsUri** fornece uma amostra Uri para todos os recursos não conformes para uma definição de política específica. Olhando para o valor **$filter,** o ComplianceState é igual (eq) a 'NonCompliant', a PolicyAssignmentId é especificada para a definição de política e, em seguida, a própria PolicyDefinitionId. A razão para incluir a PolicyAssignmentId no filtro é porque o PolicyDefinitionId poderia existir em várias atribuições políticas ou de iniciativa com diferentes âmbitos. Especificando tanto o PolicyAssignmentId como o PolicyDefinitionId, podemos ser explícitos nos resultados que procuramos. Anteriormente, para o PolicyStates, utilizamos **o mais recente**, que automaticamente define uma janela **de** e **para o** tempo das últimas 24 horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
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
        "ComplianceState": "NonCompliant",
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

Quando um recurso é criado ou atualizado, um resultado de avaliação de política é gerado. Os resultados são _chamados de eventos políticos._ Use o seguinte Uri para ver eventos políticos recentes associados à subscrição.

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

Para obter mais informações sobre eventos políticos de consulta, consulte o artigo de referência da [Azure Policy Events.](/rest/api/policy-insights/policyevents)

### <a name="azure-cli"></a>CLI do Azure

O grupo de comando [Azure CLI](/cli/azure/what-is-azure-cli) para a Política Azure cobre a maioria das operações disponíveis em REST ou Azure PowerShell. Para obter a lista completa dos comandos disponíveis, consulte [Azure CLI - Azure Policy Overview](/cli/azure/policy).

Exemplo: Obter o resumo do Estado para a política mais atribuída com o maior número de recursos não conformes.

```azurecli-interactive
az policy state summarize --top 1
```

A parte superior da resposta parece este exemplo:

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

Exemplo: Obter o registo estatal do recurso mais recentemente avaliado (o padrão é por tempos em ordem descendente).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

Exemplo: Obter os detalhes de todos os recursos de rede virtuais não conformes.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

Exemplo: Obtenção de eventos relacionados com recursos de rede virtuais não conformes que ocorreram após uma data específica.

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

O módulo Azure PowerShell para a Política Azure está disponível na PowerShell Gallery como [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Utilizando o PowerShellGet, pode instalar o módulo utilizando `Install-Module -Name Az.PolicyInsights` (certifique-se de que tem o mais recente [Azure PowerShell](/powershell/azure/install-az-ps) instalado):

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

Exemplo: Obter o resumo do Estado para a política mais atribuída com o maior número de recursos não conformes.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: Obter o registo estatal do recurso mais recentemente avaliado (o padrão é por tempos em ordem descendente).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

Exemplo: Obter os detalhes de todos os recursos de rede virtuais não conformes.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

Exemplo: Obtenção de eventos relacionados com recursos de rede virtuais não conformes que ocorreram após uma data específica, convertendo-se para um objeto CSV e exportando para um ficheiro.

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

A saída do `$policyEvents` objeto parece o seguinte:

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
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

O **campo PrincipalOid** pode ser utilizado para obter um utilizador específico com o cmdlet Azure PowerShell `Get-AzADUser` . Substitua **{principalOid}** pela resposta que obtém do exemplo anterior.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Registos do Azure Monitor

Se tiver um espaço de [trabalho Log Analytics](../../../azure-monitor/log-query/log-query-overview.md) com a `AzureActivity` [solução Activity Log Analytics](../../../azure-monitor/platform/activity-log.md) ligado à sua subscrição, também pode ver resultados de incumprimento da avaliação de recursos novos e atualizados utilizando consultas simples de Kusto e a `AzureActivity` tabela. Com detalhes nos registos do Azure Monitor, os alertas podem ser configurados para observar o incumprimento.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Diagrama de contas de armazenamento expostas a redes públicas no grupo de recursos Contoso R G." border="false":::

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
