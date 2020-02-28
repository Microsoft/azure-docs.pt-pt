---
title: Deteção Inteligente - anomalias de falha, em Insights de Aplicação / Microsoft Docs
description: Alerta-o para alterações invulgares na taxa de pedidos falhados na sua aplicação web e fornece análise sinuosa. Não é necessária nenhuma configuração.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: e1c07fca3a4eee19e56c313a889e5b86ce2b4c42
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671754"
---
# <a name="smart-detection---failure-anomalies"></a>Deteção Inteligente - Anomalias de Falha
[Application Insights](../../azure-monitor/app/app-insights-overview.md) alerta-o automaticamente em tempo real se a sua aplicação da Web experimentar um aumento anormal na taxa de pedidos falhados. Deteta um aumento invulgar na taxa de pedidos de HTTP ou chamadas de dependência que são reportadas como falhadas. Para pedidos, os pedidos falhados geralmente têm códigos de resposta de 400 ou mais. Para ajudá-lo a triagem e diagnosticar o problema, uma análise das características das falhas e dados de aplicação relacionados é fornecida nos detalhes de alerta. Há também ligações com o portal Application Insights para mais diagnóstico. A funcionalidade não necessita de configuração nem configuração, uma vez que utiliza algoritmos de aprendizagem automática para prever a taxa normal de falha.

Esta funcionalidade funciona para qualquer aplicação web, alojada na nuvem ou nos seus próprios servidores, que geram pedidos de aplicação ou dados de dependência. Por exemplo, se tiver uma função de trabalhador que chame [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ou [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Depois de configurar os Insights de [Aplicação para o seu projeto](../../azure-monitor/app/app-insights-overview.md), e se a sua aplicação gerar uma certa quantidade mínima de dados, a Deteção Inteligente de anomalias de falhas leva 24 horas para aprender o comportamento normal da sua aplicação, antes de ser ligada e poder enviar alertas.

Aqui está um alerta de amostra:

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

Os detalhes do alerta dirão:

* A taxa de avaria em comparação com o comportamento normal da aplicação.
* Quantos utilizadores são afetados - por isso sabe o quanto se deve preocupar.
* Um padrão característico associado às falhas. Neste exemplo, há um código de resposta particular, nome de pedido (operação) e versão de aplicação. Isso diz-te imediatamente onde começar a procurar o teu código. Outras possibilidades podem ser um navegador específico ou sistema operativo de cliente.
* A exceção, os rastreios de registo e a falha de dependência (bases de dados ou outros componentes externos) que parecem estar associados às falhas caracterizadas.
* Links diretamente para pesquisas relevantes sobre os dados em Insights de Aplicação.

## <a name="benefits-of-smart-detection"></a>Benefícios da Deteção Inteligente
Os [alertas métricos comuns](../../azure-monitor/app/alerts.md) dizem-lhe que pode haver um problema. Mas a Deteção Inteligente inicia o trabalho de diagnóstico para si, realizando muito a análise que teria de fazer a si mesma. Obtém-se os resultados cuidadosamente embalados, ajudando-o a chegar rapidamente à raiz do problema.

## <a name="how-it-works"></a>Como funciona
A Smart Detection monitoriza os dados recebidos da sua aplicação e, em particular, as taxas de falha. Esta regra conta o número de pedidos para os quais a propriedade `Successful request` é falsa, e o número de pedidos de dependência para os quais a propriedade `Successful call` é falsa. Para pedidos, por padrão, `Successful request == (resultCode < 400)` (a menos que tenha escrito código personalizado para [filtrar](../../azure-monitor/app/api-filtering-sampling.md#filtering) ou gerar as suas próprias chamadas [TrackRequest).](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 

O desempenho da sua aplicação tem um padrão típico de comportamento. Alguns pedidos ou chamadas de dependência serão mais propensos ao fracasso do que outros; e a taxa de insucesso global pode subir à medida que a carga aumenta. A Smart Detection usa machine learning para encontrar estas anomalias.

À medida que os dados chegam aos Insights de Aplicação da sua aplicação web, o Smart Detection compara o comportamento atual com os padrões vistos ao longo dos últimos dias. Se um aumento anormal da taxa de insucesso for observado em comparação com o desempenho anterior, é desencadeada uma análise.

Quando uma análise é desencadeada, o serviço realiza uma análise de cluster sobre o pedido falhado, para tentar identificar um padrão de valores que caracterizam as falhas. 

No exemplo acima, a análise descobriu que a maioria das falhas são sobre um código de resultado específico, nome de pedido, anfitrião de URL do Servidor e exemplo de funções. 

Quando o seu serviço é instrumentado com estas chamadas, o analisador procura uma exceção e uma falha de dependência que esteja associada a pedidos no cluster que identificou, juntamente com um exemplo de quaisquer registos de vestígios associados a esses pedidos.

A análise resultante é enviada para si como alerta, a menos que tenha configurado não o fazer.

Tal como os [alertas que define manualmente,](../../azure-monitor/app/alerts.md)pode inspecionar o estado do alerta de disparo, que pode ser resolvido se o problema for corrigido. Configure as regras de alerta na página de Alertas do seu recurso Application Insights. Mas, ao contrário de outros alertas, não é necessário configurar ou configurar a Deteção Inteligente. Se quiser, pode desativá-lo ou alterar os seus endereços de e-mail-alvo.

### <a name="alert-logic-details"></a>Detalhes da lógica de alerta

Os alertas são desencadeados pelo nosso algoritmo de aprendizagem automática proprietário, por isso não podemos partilhar os detalhes exatos da implementação. Dito isto, entendemos que às vezes é preciso saber mais sobre como funciona a lógica subjacente. Os principais fatores avaliados para determinar se um alerta deve ser desencadeado são: 

* Análise da percentagem de insucesso de pedidos/dependências numa janela de tempo de rolamento de 20 minutos.
* Uma comparação da percentagem de insucesso dos últimos 20 minutos com a taxa nos últimos 40 minutos e nos últimos sete dias, e à procura de desvios significativos que excedam o desvio padrão.
* Utilizando um limite adaptativo para a percentagem mínima de insucesso, que varia em função do volume de pedidos/dependências da aplicação.
* Existe uma lógica que pode resolver automaticamente a condição do monitor de alerta disparado, se o problema já não for detetado durante 8-24 horas.

## <a name="configure-alerts"></a>Configurar alertas

Pode desativar a regra de alerta de deteção inteligente a partir do portal ou utilizando o Gestor de Recursos Azure[(ver exemplo](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)de modelo).

Esta regra de alerta é criada com um Grupo de [Ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) associado chamado "Application Insights Smart Detection" que contém ações de e-mail e webhook, e pode ser estendida para desencadear ações adicionais quando o alerta dispara.

> [!NOTE]
> As notificações por e-mail enviadas desta regra de alerta são agora enviadas por padrão aos utilizadores associados às funções de Monitoring Reader e Monitoring Contributor da subscrição. Mais informações sobre este tema [aqui.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)
> As notificações enviadas desta regra de alerta seguem o [esquema de alerta comum](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
>

Abra a página alertas. As regras de alerta de anomalias de falha estão incluídas juntamente com quaisquer alertas que tenha definido manualmente, e pode ver se está atualmente em estado de alerta.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

Clique no alerta para configurá-lo.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

Note que pode desativar ou eliminar uma regra de alerta de Anomalias de Falha, mas não pode criar outra no mesmo recurso Application Insights.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>Exemplo de Anomalias de Falha alertam carga útil do webhook

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Triagem e diagnosticar um alerta

Um alerta indica que foi detetado um aumento anormal na taxa de pedido falhada. É provável que haja algum problema com a sua app ou o seu ambiente.

Para investigar mais, clique em 'Ver todos os detalhes em Informações de Aplicação' os links nesta página irão levá-lo diretamente para uma página de [pesquisa](../../azure-monitor/app/diagnostic-search.md) filtrada para os pedidos, exceção, dependência ou vestígios relevantes. 

Também pode abrir o [portal Azure,](https://portal.azure.com)navegar para o recurso Application Insights para a sua aplicação e abrir a página Falhas.

Clicar em 'Diagnosticar falhas' irá ajudá-lo a obter mais detalhes e resolver o problema.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

A partir da percentagem de pedidos e número de utilizadores afetados, pode decidir a urgência do problema. No exemplo acima, a taxa de insucesso de 78,5% compara com uma taxa normal de 2,2%, indica ndo que algo de mau está a acontecer. Por outro lado, apenas 46 utilizadores foram afetados. Se fosse a sua aplicação, seria capaz de avaliar a gravidade disso.

Em muitos casos, poderá diagnosticar o problema rapidamente a partir do nome do pedido, exceção, falha de dependência e dados de rastreio fornecidos.

Neste exemplo, houve uma exceção da base de dados SQL devido ao limite de pedidos ser atingido.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>Reveja alertas recentes

Clique em **Alertas** na página de recursos da Application Insights para chegar aos mais recentes alertas disparados:

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>Qual é a diferença...
Deteção Inteligente de anomalias de falha complementa outras características similares mas distintas de Insights de Aplicação.

* [Os Alertas Métricos](../../azure-monitor/app/alerts.md) são definidos por si e podem monitorizar uma vasta gama de métricas, tais como ocupação de CPU, taxas de pedido, tempos de carregamento de página, e assim por diante. Pode usá-los para avisá-lo, por exemplo, se precisar de adicionar mais recursos. Em contraste, a Deteção Inteligente de anomalias de falhas abrange uma pequena gama de métricas críticas (atualmente apenas a taxa de pedido falhada), projetada para notificá-lo de forma quase em tempo real uma vez que a taxa de pedido falhada da sua aplicação web aumenta em comparação com o comportamento normal da aplicação web. Ao contrário dos alertas métricos, a Deteção Inteligente define e atualiza automaticamente os limiares nas alterações de resposta no comportamento. A Smart Detection também inicia o trabalho de diagnóstico para si, poupando-lhe tempo na resolução de problemas.

* [A Deteção Inteligente de anomalias de desempenho](proactive-performance-diagnostics.md) também utiliza inteligência automática para descobrir padrões incomuns nas suas métricas, e não é necessária nenhuma configuração por si. Mas, ao contrário da Deteção Inteligente de anomalias de falhas, o objetivo da Deteção Inteligente de anomalias de desempenho é encontrar segmentos do seu conjunto de utilização que possam ser mal servidos - por exemplo, por páginas específicas num tipo específico de navegador. A análise é feita diariamente, e se algum resultado for encontrado, é provável que seja muito menos urgente do que um alerta. Em contraste, a análise de anomalias de falha é realizada continuamente nos dados da aplicação, e você será notificado dentro de minutos se as taxas de falha do servidor forem maiores do que o esperado.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se receber um alerta de Deteção Inteligente
*Por que recebi este alerta?*

* Detetámos um aumento anormal na taxa de pedidos falhados em comparação com a linha de base normal do período anterior. Após a análise das falhas e dados de aplicação associados, pensamos que há um problema que deve analisar.

*A notificação significa que tenho um problema?*

* Tentamos alertar sobre perturbação ou degradação da aplicação, mas só você pode entender completamente a semântica e o impacto na app ou utilizadores.

*Então, estás a ver os meus dados de candidatura?*

* Não. O serviço é totalmente automático. Só recebeas as notificações. Os seus dados são [privados.](../../azure-monitor/app/data-retention-privacy.md)

*Tenho de subscrever este alerta?*

* Não. Todas as aplicações que enviam dados de pedido têm a regra de alerta de Deteção Inteligente.

*Posso cancelar a inscrição ou receber as notificações enviadas aos meus colegas?*

* Sim, em regras de alerta, clique na regra de Deteção Inteligente para configurá-la. Pode desativar o alerta ou alterar os destinatários para o alerta.

*Perdi o e-mail. Onde posso encontrar as notificações no portal?*

* Nos registos de Atividade. No Azure, abra o recurso Application Insights para a sua aplicação e, em seguida, selecione registos de Atividade.

*Alguns dos alertas são sobre questões conhecidas e não quero recebê-las.*

* Pode utilizar a funcionalidade de supressão de regras de ação de [alerta.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules)

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico ajudam-no a inspecionar os dados da sua aplicação:

* [Explorador métrico](../../azure-monitor/app/metrics-explorer.md)
* [Explorador de pesquisa](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../../azure-monitor/log-query/get-started-portal.md)

As deteções inteligentes são automáticas. Mas talvez queira criar mais alguns alertas?

* [Alertas métricos configurados manualmente](../../azure-monitor/app/alerts.md)
* [Disponibilidade de testes web](../../azure-monitor/app/monitor-web-app-availability.md)
