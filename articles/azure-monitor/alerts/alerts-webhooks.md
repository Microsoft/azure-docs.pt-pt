---
title: Ligue para um webhook com um alerta métrico clássico no Azure Monitor
description: Aprenda a redirecionar alertas métricos Azure para outros sistemas não-Azure.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/14/2021
ms.subservice: alerts
ms.openlocfilehash: 1ead28618582ff670aca048bb92803d61c6ca938
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723525"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Ligue para um webhook com um alerta métrico clássico no Azure Monitor

> [!WARNING]
> Este artigo descreve como usar alertas métricos clássicos mais antigos. O Azure Monitor suporta agora [novos alertas métricos em tempo real e uma nova experiência de alertas.](./alerts-overview.md) Os alertas clássicos são [retirados](./monitoring-classic-retirement.md) para utilizadores de nuvem pública, embora ainda em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Governo Azure e para o Azure China 21Vianet vão reformar-se a **29 de fevereiro de 2024**.
>

Pode utilizar webhooks para encaminhar uma notificação de alerta Azure para outros sistemas para ações pós-processamento ou personalizadas. Você pode usar um webhook em um alerta para encaminhá-lo para serviços que enviam mensagens SMS, para registar bugs, para notificar uma equipe através de serviços de chat ou mensagens, ou para várias outras ações. 

Este artigo descreve como definir um webhook em um alerta métrico Azure. Também mostra como é a carga útil do HTTP POST para um webhook. Para obter informações sobre a configuração e esquema para um alerta de registo de atividade Azure (alerta sobre eventos), consulte [um webhook num alerta de registo de atividade do Azure](../alerts/alerts-log-webhook.md).

Os alertas Azure usam HTTP POST para enviar o conteúdo de alerta no formato JSON para um webhook URI que fornece quando cria o alerta. O esquema é definido mais tarde neste artigo. O URI deve ser um ponto final HTTP ou HTTPS válido. O Azure regista uma entrada por pedido quando um alerta é ativado.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configurar webhooks através do portal Azure
Para adicionar ou atualizar o webhook URI, no [portal Azure,](https://portal.azure.com/)vá a **Alertas de Criação/Atualização**.

![Adicione um painel de regras de alerta](./media/alerts-webhooks/Alertwebhook.png)

Também pode configurar um alerta para publicar num webhook URI utilizando [cmdlets Azure PowerShell](../powershell-samples.md#create-metric-alerts), um [CLI de plataforma cruzada,](../cli-samples.md#work-with-alerts)ou [APIs de REPOUSO do Monitor Azure](/rest/api/monitor/alertrules).

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar utilizando uma autorização baseada em fichas. O webhook URI é salvo com um ID simbólico. Por exemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de carga útil
A operação POST contém a seguinte carga útil json e esquema para todos os alertas baseados em métricas:

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| Campo | Obrigatório | Conjunto fixo de valores | Notas |
|:--- |:--- |:--- |:--- |
| status |Y |Ativado, resolvido |O estado do alerta com base nas condições definidas. |
| contexto |Y | |O contexto de alerta. |
| carimbo de data/hora |Y | |O momento em que o alerta foi desencadeado. |
| ID |Y | |Todas as regras de alerta têm uma identificação única. |
| name |Y | |O nome de alerta. |
| descrição |Y | |Uma descrição do alerta. |
| estadoTip |Y |Métrica, Evento |Dois tipos de alertas são suportados: métrica e evento. Os alertas métricos baseiam-se numa condição métrica. Os alertas de eventos são baseados num evento no registo de atividades. Utilize este valor para verificar se o alerta se baseia numa métrica ou num evento. |
| condição |Y | |Os campos específicos para verificar com base no valor **de estadoType.** |
| nome métrico |Para alertas métricos | |O nome da métrica que define o que a regra monitoriza. |
| métricaSa |Para alertas métricos |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |A unidade permitida na métrica. Ver [valores permitidos.](/previous-versions/azure/reference/dn802430(v=azure.100)) |
| métricaValue |Para alertas métricos | |O valor real da métrica que causou o alerta. |
| limiar |Para alertas métricos | |O valor limiar no qual o alerta é ativado. |
| tamanho de janelas |Para alertas métricos | |O período de tempo que é usado para monitorizar a atividade de alerta com base no limiar. O valor deve estar entre 5 minutos e 1 dia. O valor deve estar no formato de duração ISO 8601. |
| timeAggregation |Para alertas métricos |Média, Último, Máximo, Mínimo, Nenhum, Total |Como os dados recolhidos devem ser combinados ao longo do tempo. O valor predefinido é médio. Ver [valores permitidos.](/previous-versions/azure/reference/dn802410(v=azure.100)) |
| operador |Para alertas métricos | |O operador que é usado para comparar os dados métricos atuais com o limiar definido. |
| subscriptionId |Y | |O ID de assinatura Azure. |
| resourceGroupName |Y | |O nome do grupo de recursos para o recurso afetado. |
| resourceName |Y | |O nome do recurso do recurso afetado. |
| resourceType |Y | |O tipo de recurso do recurso afetado. |
| resourceId |Y | |A identificação de recursos do recurso afetado. |
| Reedição de recursos |Y | |A região ou localização do recurso afetado. |
| portalLink |Y | |Uma ligação direta à página de resumo do recurso do portal. |
| propriedades |N |Opcional |Um conjunto de pares chave/valor que tem detalhes sobre o evento. Por exemplo, `Dictionary<String, String>`. O campo de propriedades é opcional. Num fluxo de trabalho personalizado baseado em UI ou lógica, os utilizadores podem introduzir pares chave/valor que podem ser passados através da carga útil. Uma forma alternativa de passar propriedades personalizadas de volta para o webhook é através do próprio webhook URI (como parâmetros de consulta). |

> [!NOTE]
> Só pode definir o campo **de propriedades** utilizando [APIs de REPOUSO do Monitor Azure](/rest/api/monitor/alertrules).
>
>

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre alertas e webhooks do Azure no vídeo [Integrate Azure alertas com PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Saiba como [executar scripts da Azure Automation (runbooks) em alertas Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Saiba como [utilizar uma aplicação lógica para enviar uma mensagem SMS via Twilio a partir de um alerta Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
* Saiba como [usar uma aplicação lógica para enviar uma mensagem Slack a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Saiba como [usar uma aplicação lógica para enviar uma mensagem para uma fila Azure a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).