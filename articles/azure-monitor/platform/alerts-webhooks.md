---
title: Ter um alerta de métrica clássico notificar um sistema não pertencente ao Azure através de um webhook
description: Saiba como redirecionar os alertas de métricas do Azure para sistemas de outros, não pertencente ao Azure.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345795"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>Ter um alerta de métrica clássico notificar um sistema não pertencente ao Azure através de um webhook
Pode utilizar webhooks para encaminhar uma notificação de alerta do Azure para outros sistemas para ações de pós-processamento ou personalizados. Pode utilizar um webhook num alerta para o encaminhar para serviços que enviam mensagens SMS, para registrar bugs, para notificar a equipa através de bate-papo ou serviços de mensagens ou para várias outras ações. 

Este artigo descreve como definir um webhook num alerta de métrica do Azure. Ele também mostra o payload para o HTTP POST para um webhook como fica. Para obter informações sobre o programa de configuração e o esquema de uma atividade do Azure alerta de registo (alerta em eventos), consulte [chamar um webhook num alerta de registo de atividades do Azure](alerts-log-webhook.md).

Alertas do Azure utilizam HTTP POST para enviar o conteúdo de alerta no formato JSON para um URI que fornece ao criar o alerta de webhook. O esquema está definido neste artigo. O URI tem de ser um ponto final HTTP ou HTTPS válido. Azure publica uma entrada por pedido, quando um alerta é ativado.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configurar webhooks através do portal do Azure
Para adicionar ou atualizar o webhook URI, o [portal do Azure](https://portal.azure.com/), aceda a **Create/Update Alerts**.

![Adicionar um painel de regra de alerta](./media/alerts-webhooks/Alertwebhook.png)

Também pode configurar um alerta para postar um webhook do URI usando [cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), uma [CLI de várias plataformas](../../azure-monitor/platform/cli-samples.md#work-with-alerts), ou [APIs de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar utilizando a autorização baseada em tokens. O webhook URI é guardado com um ID de token. Por exemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema do payload
A operação de POSTAGEM contém o payload JSON seguinte e o esquema para todos os alertas com base na métrica:

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
| status |S |Ativado, resolvido |O estado do alerta com base nas condições que definir. |
| context |S | |O contexto do alerta. |
| timestamp |S | |A hora em que o alerta foi acionado. |
| id |S | |Cada regra de alerta tem um ID exclusivo. |
| name |S | |O nome do alerta. |
| description |S | |Uma descrição do alerta. |
| conditionType |S |Métrica, eventos |Dois tipos de alertas são suportados: métricas e eventos. Alertas de métricas são baseadas em condições de métricas. Alertas de eventos baseiam-se num evento no registo de atividades. Utilize este valor para verificar se o alerta é com base numa métrica ou num evento. |
| condition |S | |Os campos específicos para verificar se baseia a **conditionType** valor. |
| metricName |Para alertas de métricas | |O nome da métrica que define o que a regra monitoriza. |
| metricUnit |Para alertas de métricas |Bytes, BytesPerSecond, contagem, CountPerSecond, por cento, segundos |A unidade permitida na métrica. Ver [valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricValue |Para alertas de métricas | |O valor real da métrica que causou o alerta. |
| threshold |Para alertas de métricas | |O valor de limiar em que o alerta está ativado. |
| windowSize |Para alertas de métricas | |O período de tempo que é utilizado para monitorizar a atividade de alerta com base no limiar. O valor tem de ser entre 5 minutos e 1 dia. O valor tem de estar no formato de duração ISO 8601. |
| timeAggregation |Para alertas de métricas |Média, último, máximo, mínimo, None, Total |Como os dados que são recolhidos devem ser combinados ao longo do tempo. O valor predefinido é média. Ver [valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Para alertas de métricas | |O operador que é utilizado para comparar os dados de métricos atuais para o limiar definido. |
| subscriptionId |S | |O ID de subscrição do Azure. |
| resourceGroupName |S | |O nome do grupo de recursos para o recurso afetado. |
| resourceName |S | |O nome de recurso do recurso afetado. |
| resourceType |S | |O tipo de recurso do recurso afetado. |
| resourceId |S | |O ID de recurso do recurso afetado. |
| resourceRegion |S | |A região ou a localização do recurso afetado. |
| portalLink |S | |Uma ligação direta para a página de resumo de recursos de portal. |
| properties |N |Opcional |Um conjunto de pares chave/valor que contém os detalhes sobre o evento. Por exemplo, `Dictionary<String, String>`. O campo de propriedades é opcional. Numa interface do Usuário personalizada ou um fluxo de trabalho de base na aplicação lógica, os utilizadores podem introduzir pares de chave/valor que podem ser passados via o payload. Outra forma de passar as propriedades personalizadas para o webhook é através do webhook URI em si (como parâmetros de consulta). |

> [!NOTE]
> Pode definir o **propriedades** campo utilizando apenas [APIs de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre alertas do Azure e webhooks no vídeo [alertas de integrar o Azure com PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Saiba como [executar scripts de automatização do Azure (runbooks) nos alertas do Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Saiba como [utilizar uma aplicação lógica para enviar uma mensagem SMS através do Twilio a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Saiba como [utilizar uma aplicação lógica para enviar uma mensagem Slack a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Saiba como [utilizar uma aplicação lógica para enviar uma mensagem numa fila do Azure a partir de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

