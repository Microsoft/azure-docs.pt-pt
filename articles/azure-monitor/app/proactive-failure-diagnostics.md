---
title: Smart Detection - anomalias de falha, em Insights de Aplicação | Microsoft Docs
description: Alerta-o para alterações invulgares na taxa de pedidos falhados na sua aplicação web e fornece análises de diagnóstico. Não é necessária nenhuma configuração.
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 0f4de3aaba4acf86df37048134089326196e87ff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587537"
---
# <a name="smart-detection---failure-anomalies"></a>Deteção Inteligente - Anomalias de falha
[O Application Insights](./app-insights-overview.md) alerta-o automaticamente em tempo real se a sua aplicação web experimentar um aumento anormal na taxa de pedidos falhados. Deteta um aumento invulgar na taxa de pedidos HTTP ou chamadas de dependência que são reportadas como falhadas. Para pedidos, os pedidos falhados geralmente têm códigos de resposta de 400 ou mais. Para o ajudar a triagem e diagnóstico do problema, é fornecida uma análise das características das falhas e dados de aplicações relacionados nos detalhes do alerta. Existem também ligações ao portal Application Insights para um diagnóstico mais aprofundado. A funcionalidade não necessita de configuração nem configuração, pois utiliza algoritmos de aprendizagem automática para prever a taxa normal de falha.

Esta funcionalidade funciona para qualquer aplicação web, hospedada na nuvem ou nos seus próprios servidores, que gerem pedidos de aplicação ou dados de dependência. Por exemplo, se tiver uma função de trabalhador que chame [TrackRequest()](./api-custom-events-metrics.md#trackrequest) ou [TrackDependency()](./api-custom-events-metrics.md#trackdependency).

Depois de configurar [o Application Insights para o seu projeto](./app-insights-overview.md)– e se a sua aplicação gerar uma certa quantidade mínima de dados, a Deteção Inteligente de anomalias de falha demora 24 horas a aprender o comportamento normal da sua app, antes de ser ligada e pode enviar alertas.

Aqui está um alerta de amostra:

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="Alerta de deteção inteligente de amostras mostrando análise de cluster em torno da falha." lightbox="./media/proactive-failure-diagnostics/013.png":::

Os detalhes do alerta dir-lhe-ão:

* A taxa de falha em comparação com o comportamento normal da aplicação.
* Quantos utilizadores são afetados - por isso sabe o quanto se preocupar.
* Um padrão característico associado às falhas. Neste exemplo, há um código de resposta particular, nome de pedido (operação) e versão de aplicação. Isso diz-lhe imediatamente onde começar a procurar no seu código. Outras possibilidades podem ser um navegador específico ou um sistema operativo de clientes.
* A exceção, os vestígios de registo e a falha de dependência (bases de dados ou outros componentes externos) que parecem estar associados às falhas caracterizadas.
* Liga-se diretamente a pesquisas relevantes sobre os dados em Insights de Aplicação.

## <a name="benefits-of-smart-detection"></a>Benefícios da Deteção Inteligente
Alertas [métricos comuns](../alerts/alerts-log.md) dizem que pode haver um problema. Mas a Smart Detection inicia o trabalho de diagnóstico para si, realizando muito a análise que teria de fazer por si mesma. Você tem os resultados cuidadosamente embalados, ajudando-o a chegar rapidamente à raiz do problema.

## <a name="how-it-works"></a>Como funciona
A Smart Detection monitoriza os dados recebidos da sua aplicação e, em particular, as taxas de avaria. Esta regra conta o número de pedidos para os quais o `Successful request` imóvel é falso, e o número de pedidos de dependência para os quais o `Successful call` imóvel é falso. Para pedidos, por padrão, `Successful request == (resultCode < 400)` (a menos que tenha escrito código personalizado para [filtrar](./api-filtering-sampling.md#filtering) ou gerar as suas próprias chamadas [TrackRequest).](./api-custom-events-metrics.md#trackrequest) 

O desempenho da sua aplicação tem um padrão típico de comportamento. Alguns pedidos ou chamadas de dependência serão mais propensos a falhas do que outros; e a taxa de insucesso geral pode subir à medida que a carga aumenta. Smart Detection usa machine learning para encontrar estas anomalias.

À medida que os dados chegam ao Application Insights a partir da sua aplicação web, o Smart Detection compara o comportamento atual com os padrões observados ao longo dos últimos dias. Se um aumento anormal da taxa de avaria for observado em comparação com o desempenho anterior, é desencadeada uma análise.

Quando uma análise é desencadeada, o serviço realiza uma análise de cluster sobre o pedido falhado, para tentar identificar um padrão de valores que caracterizam as falhas. 

No exemplo acima, a análise descobriu que a maioria das falhas são sobre um código de resultados específico, nome de pedido, anfitrião de URL do servidor e instância de função. 

Quando o seu serviço é instrumentado com estas chamadas, o analisador procura uma exceção e uma falha de dependência que esteja associada a pedidos no cluster que identificou, juntamente com um exemplo de quaisquer registos de vestígios associados a esses pedidos.

A análise resultante é enviada como alerta, a menos que tenha configurado para não o fazer.

Tal como os [alertas que definiu manualmente,](../alerts/alerts-log.md)pode verificar o estado do alerta de disparo, que pode ser resolvido se o problema for corrigido. Configure as regras de alerta na página Alertas do seu recurso Application Insights. Mas, ao contrário de outros alertas, não é necessário configurar ou configurar a Deteção Inteligente. Se quiser, pode desativá-lo ou alterar os seus endereços de e-mail-alvo.

### <a name="alert-logic-details"></a>Alerte detalhes da lógica

Os alertas são desencadeados pelo nosso algoritmo de aprendizagem automática proprietário para que não possamos partilhar os detalhes exatos de implementação. Dito isto, entendemos que às vezes é preciso saber mais sobre como funciona a lógica subjacente. Os principais fatores que são avaliados para determinar se um alerta deve ser desencadeado são: 

* Análise da percentagem de avaria de pedidos/dependências numa janela de tempo de rolamento de 20 minutos.
* Uma comparação da percentagem de falha dos últimos 20 minutos com a taxa nos últimos 40 minutos e nos últimos sete dias, e à procura de desvios significativos que excedam os X-vezes esse desvio padrão.
* Utilizando um limite adaptativo para a percentagem mínima de falha, que varia em com base no volume de pedidos/dependências da aplicação.
* Existe uma lógica que pode resolver automaticamente a condição do monitor de alerta disparado, se o problema deixar de ser detetado durante 8-24 horas.
  Nota: no desenho atual. uma notificação ou ação não será enviada quando um alerta de Deteção Inteligente for resolvido. Pode verificar se um alerta de Deteção Inteligente foi resolvido no portal Azure.

## <a name="configure-alerts"></a>Configurar alertas

Pode desativar a regra de alerta de Deteção Inteligente a partir do portal ou utilizar o Gestor de Recursos Azure[(ver exemplo do modelo).](./proactive-arm-config.md)

Esta regra de alerta é criada com um [Grupo de Ação](../alerts/action-groups.md) associado chamado "Application Insights Smart Detection" que contém ações de e-mail e webhook, e pode ser estendida para desencadear ações adicionais quando o alerta dispara.

> [!NOTE]
> As notificações por e-mail enviadas a partir desta regra de alerta são agora enviadas por defeito aos utilizadores associados às funções de Monitor e Monitoring Da subscrição. Mais informações sobre isto estão disponíveis [aqui.](./proactive-email-notification.md)
> As notificações enviadas a partir desta regra de alerta seguem o [esquema comum de alerta](../alerts/alerts-common-schema.md).
>

Abra a página Alertas. As regras de alerta de anomalias de avaria estão incluídas juntamente com quaisquer alertas que tenha definido manualmente, e pode ver se está atualmente em estado de alerta.

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="Na página de recursos 'Insights de Aplicação', clique em telha Alertas e, em seguida, Gerencie as regras de alerta." lightbox="./media/proactive-failure-diagnostics/021.png":::

Clique no alerta para o configurar.

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="Ecrã de configuração de regras." lightbox="./media/proactive-failure-diagnostics/032.png":::

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
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
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
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>Triagem e diagnóstico de um alerta

Um alerta indica que foi detetado um aumento anormal na taxa de pedido falhada. É provável que haja algum problema com a sua app ou com o seu ambiente.

Para mais informações, clique em 'Ver todos os detalhes em Insights de Aplicação' os links desta página levar-lhe-ão diretamente para uma [página de pesquisa](./diagnostic-search.md) filtrada para os pedidos relevantes, exceção, dependência ou vestígios. 

Também pode abrir o [portal Azure,](https://portal.azure.com)navegar para o recurso Application Insights para a sua aplicação e abrir a página Falhas.

Clicar em 'Diagnosticar falhas' irá ajudá-lo a obter mais detalhes e resolver o problema.

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="Pesquisa de diagnóstico." lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

A partir da percentagem de pedidos e número de utilizadores afetados, pode decidir a urgência da questão. No exemplo acima, a taxa de insucesso de 78,5% compara com uma taxa normal de 2,2%, indicando que algo de mau se está a passar. Por outro lado, apenas 46 utentes foram afetados. Se fosse a sua aplicação, seria capaz de avaliar a gravidade da situação.

Em muitos casos, você será capaz de diagnosticar o problema rapidamente a partir do nome do pedido, exceção, falha de dependência e rastreios fornecidos.

Neste exemplo, houve uma exceção da Base de Dados SQL devido ao limite de pedidos atingido.

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="Falhou nos detalhes do pedido." lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>Rever alertas recentes

Clique em **Alertas** na página de recursos do Application Insights para chegar aos alertas de disparo mais recentes:

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="Alertas resumo." lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>Qual é a diferença...
A Deteção Inteligente de anomalias de falha complementa outras características similares, mas distintas, de Application Insights.

* [os alertas métricos](../alerts/alerts-log.md) são definidos por si e podem monitorizar uma vasta gama de métricas, tais como ocupação de CPU, taxas de pedido, tempos de carga de página, e assim por diante. Pode usá-los para avisá-lo, por exemplo, se precisar de adicionar mais recursos. Em contraste, a Deteção Inteligente de anomalias de falha abrange uma pequena gama de métricas críticas (atualmente apenas taxa de pedido falhada), concebidas para notificá-lo de forma quase em tempo real, uma vez que a taxa de pedido falhada da sua aplicação web aumenta em comparação com o comportamento normal da aplicação web. Ao contrário dos alertas métricos, a Deteção Inteligente define automaticamente e atualiza os limiares em alterações de resposta no comportamento. A Smart Detection também inicia o trabalho de diagnóstico para si, poupando-lhe tempo na resolução de problemas.

* [A Deteção Inteligente de anomalias de desempenho](proactive-performance-diagnostics.md) também utiliza inteligência da máquina para descobrir padrões incomuns nas suas métricas, e não é necessária nenhuma configuração por si. Mas, ao contrário da Deteção Inteligente de anomalias de falha, o objetivo de Deteção Inteligente de anomalias de desempenho é encontrar segmentos do seu coletor de uso que possam ser mal servidos - por exemplo, por páginas específicas sobre um tipo específico de navegador. A análise é feita diariamente, e se algum resultado for encontrado, é provável que seja muito menos urgente do que um alerta. Em contrapartida, a análise de anomalias de falha é realizada continuamente nos dados da aplicação recebida, e será notificado dentro de minutos se as taxas de falha do servidor forem superiores às esperadas.

## <a name="if-you-receive-a-smart-detection-alert"></a>Se receber um alerta de Deteção Inteligente
*Por que recebi este alerta?*

* Detetamos um aumento anormal na taxa de pedidos falhados em comparação com a linha de base normal do período anterior. Após a análise das falhas e dos dados de aplicações associados, pensamos que há um problema que deve analisar.

*A notificação significa que definitivamente tenho um problema?*

* Tentamos alertar para a perturbação ou degradação da app, mas só você pode entender completamente a semântica e o impacto na app ou utilizadores.

*Então, estás a ver os dados da minha candidatura?*

* N.º O serviço é totalmente automático. Só recebe as notificações. Os seus dados são [privados.](./data-retention-privacy.md)

*Tenho de subscrever este alerta?*

* N.º Cada aplicação que envia dados de pedido tem a regra de alerta de Deteção Inteligente.

*Posso cancelar a subscrição ou enviar as notificações aos meus colegas?*

* Sim, nas regras de alerta, clique na regra de Deteção Inteligente para a configurar. Pode desativar o alerta ou alterar os destinatários para o alerta.

*Perdi o e-mail. Onde posso encontrar as notificações no portal?*

* Nos registos de Atividade. Em Azure, abra o recurso Application Insights para a sua aplicação e, em seguida, selecione registos de Atividade.

*Alguns dos alertas são sobre questões conhecidas e não quero recebê-los.*

* Pode utilizar a função de supressão [de regras de ação de alerta.](../alerts/alerts-action-rules.md)

## <a name="next-steps"></a>Passos seguintes
Estas ferramentas de diagnóstico ajudam-no a inspecionar os dados da sua aplicação:

* [Explorador métrico](../essentials/metrics-charts.md)
* [Explorador de pesquisa](./diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](../logs/log-analytics-tutorial.md)

As deteções inteligentes são automáticas. Mas talvez queira fazer mais alguns alertas?

* [Alertas métricos configurados manualmente](../alerts/alerts-log.md)
* [Testes Web de disponibilidade](./monitor-web-app-availability.md)