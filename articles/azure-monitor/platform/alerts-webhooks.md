---
title: Ligue para um webhook com um alerta métrico clássico no Monitor Azure
description: Aprenda a redirecionar os alertas métricos azure para outros sistemas não-Azure.
author: harelbr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: fd4bf2d404a7152da04e72d323f463c18167f5bf
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705518"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Ligue para um webhook com um alerta métrico clássico no Monitor Azure
Pode utilizar webhooks para encaminhar uma notificação de alerta Azure para outros sistemas para pós-processamento ou ações personalizadas. Pode utilizar um webhook em alerta para encaminhá-lo para serviços que enviam mensagens SMS, para registar bugs, para notificar uma equipa através de serviços de chat ou mensagens, ou para várias outras ações. 

Este artigo descreve como colocar um webhook num alerta métrico Azure. Também mostra como é a carga útil para o HTTP POST para um webhook. Para obter informações sobre a configuração e o esquema para um alerta de registo de atividade do Azure (alerta sobre os eventos), consulte [um webhook num alerta](alerts-log-webhook.md)de registo de atividade do Azure .

Os alertas Azure usam http POST para enviar os conteúdos de alerta em formato JSON para um webhook URI que fornece quando cria o alerta. O esquema é definido mais tarde neste artigo. O URI deve ser um ponto final http ou HTTPS válido. O Azure regista uma entrada por pedido quando um alerta é ativado.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configure webhooks através do portal Azure
Para adicionar ou atualizar o webhook URI, no [portal Azure,](https://portal.azure.com/)vá a **Create/Update Alerts**.

![Adicione um painel de regra de alerta](./media/alerts-webhooks/Alertwebhook.png)

Também pode configurar um alerta para publicar num webhook URI utilizando [cmdlets Azure PowerShell,](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts)um [CLI de plataforma cruzada](../../azure-monitor/platform/cli-samples.md#work-with-alerts)ou [APIs REST DO Monitor Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar utilizando uma autorização baseada em token. O webhook URI é guardado com uma identificação simbólica. Por exemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de carga
A operação POST contém a seguinte carga útil e esquema JSON para todos os alertas de base métrica:

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
| status |S |Ativado, Resolvido |O estado do alerta com base nas condições que definiu. |
| noticioso |S | |O contexto de alerta. |
| carimbo de data/hora |S | |O momento em que o alerta foi desencadeado. |
| ID |S | |Cada regra de alerta tem uma identificação única. |
| nome |S | |O nome do alerta. |
| descrição |S | |Uma descrição do alerta. |
| conditionType |S |Métrica, Evento |São apoiados dois tipos de alertas: métrica e evento. Os alertas métricos baseiam-se numa condição métrica. Os alertas de eventos baseiam-se num evento no registo de atividade. Use este valor para verificar se o alerta é baseado numa métrica ou num evento. |
| condition |S | |Os campos específicos para verificar com base no valor do tipo de **condição.** |
| MetricName |Para alertas métricos | |O nome da métrica que define o que a regra monitoriza. |
| metricUnit |Para alertas métricos |Bytes, BytesPerSecond, Count, CountPerSecond, Por cento, Segundo |A unidade permitida na métrica. Ver [valores permitidos.](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx) |
| métricaValor |Para alertas métricos | |O valor real da métrica que causou o alerta. |
| threshold |Para alertas métricos | |O valor-limiar a que o alerta é ativado. |
| janelaTamanho |Para alertas métricos | |O período de tempo que é usado para monitorizar a atividade de alerta com base no limiar. O valor deve ser entre 5 minutos e 1 dia. O valor deve estar no formato de duração ISO 8601. |
| timeAggregation |Para alertas métricos |Média, último, máximo, mínimo, nenhum, total |Como os dados recolhidos devem ser combinados ao longo do tempo. O valor padrão é Médio. Ver [valores permitidos.](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx) |
| operator |Para alertas métricos | |O operador que é usado para comparar os dados métricos atuais com o limiar definido. |
| subscriptionId |S | |O ID de assinatura Azure. |
| resourceGroupName |S | |O nome do grupo de recursos para o recurso afetado. |
| resourceName |S | |O nome do recurso afetado. |
| resourceType |S | |O tipo de recurso do recurso afetado. |
| resourceId |S | |A identificação do recurso afetado. |
| recursosRegião |S | |A região ou localização do recurso afetado. |
| portalLink |S | |Um link direto para a página de resumo do recurso do portal. |
| propriedades |N |Opcional |Um conjunto de pares chave/valor que tem detalhes sobre o evento. Por exemplo, `Dictionary<String, String>`. O campo de propriedades é opcional. Num fluxo de trabalho personalizado baseado em UI ou lógica, os utilizadores podem introduzir pares chave/valor que podem ser passados através da carga útil. Uma forma alternativa de passar propriedades personalizadas de volta para o webhook é através do próprio webhook URI (como parâmetros de consulta). |

> [!NOTE]
> Só é possível definir o campo de **propriedades** utilizando [APIs REST DO Monitor Azure](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre alertas Azure e webhooks no vídeo [Integrate Azure alertas com PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Saiba como [executar scripts de Automação Azure (livros de execução) em alertas Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Aprenda a [usar uma aplicação lógica para enviar uma mensagem SMS via Twilio a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Aprenda a [usar uma aplicação lógica para enviar uma mensagem Slack a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Aprenda a [usar uma aplicação lógica para enviar uma mensagem para uma fila Azure a partir de um alerta Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

