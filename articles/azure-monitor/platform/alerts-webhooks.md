---
title: Chamar um webhook com um alerta de métrica clássico no Azure Monitor
description: Saiba como redirecionar alertas de métrica do Azure para outros sistemas não Azure.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 88de4464e5b95b49e76e5d9c4f7dc0d6732076e1
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286168"
---
# <a name="call-a-webhook-with-a-classic-metric-alert-in-azure-monitor"></a>Chamar um webhook com um alerta de métrica clássico no Azure Monitor
Você pode usar WebHooks para rotear uma notificação de alerta do Azure para outros sistemas para pós-processamento ou ações personalizadas. Você pode usar um webhook em um alerta para circulá-lo para serviços que enviam mensagens SMS, para registrar bugs, para notificar uma equipe por meio de serviços de chat ou de mensagens ou para várias outras ações. 

Este artigo descreve como definir um webhook em um alerta de métrica do Azure. Ele também mostra a aparência da carga do HTTP POST para um webhook. Para obter informações sobre a instalação e o esquema de um alerta do log de atividades do Azure (alerta sobre eventos), consulte [chamar um webhook em um alerta do log de atividades do Azure](alerts-log-webhook.md).

Os alertas do Azure usam HTTP POST para enviar o conteúdo do alerta no formato JSON para um URI de webhook que você fornece ao criar o alerta. O esquema é definido posteriormente neste artigo. O URI deve ser um ponto de extremidade HTTP ou HTTPS válido. O Azure lança uma entrada por solicitação quando um alerta é ativado.

## <a name="configure-webhooks-via-the-azure-portal"></a>Configurar WebHooks por meio do portal do Azure
Para adicionar ou atualizar o URI do webhook, na [portal do Azure](https://portal.azure.com/), vá para **criar/atualizar alertas**.

![Adicionar um painel de regras de alerta](./media/alerts-webhooks/Alertwebhook.png)

Você também pode configurar um alerta para postar em um URI de webhook usando [cmdlets Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), uma [CLI de plataforma cruzada](../../azure-monitor/platform/cli-samples.md#work-with-alerts)ou [Azure monitor APIs REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).

## <a name="authenticate-the-webhook"></a>Autenticar o webhook
O webhook pode autenticar usando a autorização baseada em token. O URI do webhook é salvo com uma ID de token. Por exemplo: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>Esquema de carga
A operação POST contém a seguinte carga e esquema JSON para todos os alertas baseados em métricas:

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
| status |S |Ativado, resolvido |O status do alerta com base nas condições definidas. |
| noticioso |S | |O contexto do alerta. |
| carimbo de data/hora |S | |A hora em que o alerta foi disparado. |
| ID |S | |Cada regra de alerta tem uma ID exclusiva. |
| nome |S | |O nome do alerta. |
| descrição |S | |Uma descrição do alerta. |
| conditionType |S |Métrica, evento |Há suporte para dois tipos de alertas: métrica e evento. Os alertas de métricas são baseados em uma condição de métrica. Os alertas de eventos são baseados em um evento no log de atividades. Use esse valor para verificar se o alerta é baseado em uma métrica ou em um evento. |
| condition |S | |Os campos específicos a serem verificados com base no valor **ConditionType** . |
| metricName |Para alertas de métricas | |O nome da métrica que define o que a regra monitora. |
| metricUnit |Para alertas de métricas |Bytes, BytesPerSecond, contagem, CountPerSecond, porcentagem, segundos |A unidade permitida na métrica. Consulte [os valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx). |
| metricvalue |Para alertas de métricas | |O valor real da métrica que causou o alerta. |
| threshold |Para alertas de métricas | |O valor de limite no qual o alerta é ativado. |
| windowSize |Para alertas de métricas | |O período de tempo usado para monitorar a atividade de alerta com base no limite. O valor deve estar entre 5 minutos e 1 dia. O valor deve estar no formato de duração ISO 8601. |
| timeAggregation |Para alertas de métricas |Média, último, máximo, mínimo, nenhum, total |Como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. Consulte [os valores permitidos](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx). |
| operator |Para alertas de métricas | |O operador usado para comparar os dados de métrica atuais com o limite definido. |
| subscriptionId |S | |A ID da assinatura do Azure. |
| resourceGroupName |S | |O nome do grupo de recursos para o recurso afetado. |
| resourceName |S | |O nome do recurso afetado. |
| resourceType |S | |O tipo de recurso do recurso afetado. |
| resourceId |S | |A ID de recurso do recurso afetado. |
| resourceRegion |S | |A região ou o local do recurso afetado. |
| portalLink |S | |Um link direto para a página de Resumo de recursos do Portal. |
| propriedades |N |Opcional |Um conjunto de pares de chave/valor que tem detalhes sobre o evento. Por exemplo, `Dictionary<String, String>`. O campo de propriedades é opcional. Em um fluxo de trabalho personalizado baseado em aplicativo lógico ou de interface do usuário, os usuários podem inserir pares de chave/valor que podem ser passados por meio da carga. Uma maneira alternativa de passar Propriedades personalizadas de volta para o webhook é por meio do próprio URI do webhook (como parâmetros de consulta). |

> [!NOTE]
> Você pode definir o campo de **Propriedades** somente usando [Azure monitor APIs REST](https://msdn.microsoft.com/library/azure/dn933805.aspx).
>
>

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os alertas do Azure e WebHooks no vídeo [integrar alertas do Azure com o PagerDuty](https://go.microsoft.com/fwlink/?LinkId=627080).
* Saiba como [executar os scripts de automação do Azure (runbooks) nos alertas do Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem SMS por meio do twilio de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem de margem de atraso de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app).
* Saiba como [usar um aplicativo lógico para enviar uma mensagem a uma fila do Azure de um alerta do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app).

