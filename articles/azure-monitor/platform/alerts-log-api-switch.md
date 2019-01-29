---
title: API de alertas de comutador de legado do Log Analytics para a nova API de alertas do Azure
description: Descrição geral de extinção de savedSearch herdado com base em API de alerta do Log Analytics e o processo para mudar as regras de alerta para a nova API de ScheduledQueryRules, com detalhes de endereçamento preocupações mais comuns do cliente.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 8e123705443c44373734a81936459a56bbb259cc
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55106042"
---
# <a name="switch-api-preference-for-log-alerts"></a>Mude a preferência de API para alertas de registo

> [!NOTE]
> Conteúdo indicado **não** aplicáveis aos utilizadores do Azure GovCloud e apenas aos utilizadores de cloud pública do Azure.  

Até recentemente, conseguiu regras de alerta no portal do Microsoft Operations Management Suite. A nova experiência de alertas foi integrada com vários serviços no Microsoft Azure, incluindo o Log Analytics e que solicitamos [expandir as regras do alerta a partir do portal do OMS para o Azure](alerts-extend.md). Mas para garantir a mínima interrupção para os clientes, o processo não alterou a interface programática para seu consumo - [API de alerta do Log Analytics](api-alerts.md) com base em SavedSearch.

Mas, agora anunciar para o Log Analytics alertando usuários sobre uma verdadeira alternativa programática do Azure, [do Azure Monitor - API de ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), que também é reflexiva no seu [faturação do Azure – para alertas de registo](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Para saber mais sobre como gerir os alertas de registo com a API, veja [gerir alertas de registo utilizando o modelo do Azure Resource](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [gerir alertas de registo com o PowerShell, CLI ou API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Benefícios de mudar para a nova API do Azure

Existem diversas vantagens de criar e gerir alertas utilizando [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) sobre [herdados API alerta para o Log Analytics](api-alerts.md); já listamos alguns dos principais abaixo:

- Capacidade de [cruzada pesquisa de registos de área de trabalho](../log-query/cross-workspace-query.md) em regras de alerta e span de cópia de segurança a recursos externos, como áreas de trabalho do Log Analytics ou até mesmo as aplicações de Application Insights
- Quando vários campos utilizada para o grupo de consulta, usando [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) utilizador pode especificar que campo agregado-no portal do Azure
- Criar regras de alerta de uma só vez como um único recurso sem a necessidade de criar três níveis de recursos, como com [herdados API de alerta do Log Analytics](api-alerts.md)
- Interface programática única para todas as variantes de alertas de registo com base na consulta do Azure – novos [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pode ser utilizado para gerir as regras para o Log Analytics, bem como o Application Insights
- Todos os novos registar funcionalidade alerta e futuro desenvolvimento estará disponível apenas através do novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo de mudança do API de alertas de registo legado

O processo de mover as regras de alerta do [herdados API de alerta do Log Analytics](api-alerts.md) não envolve alterar suas configurações de qualquer forma, consulta ou a definição do alerta. Os utilizadores são gratuitos para a utilização de uma [herdados API alerta para o Log Analytics](api-alerts.md) ou no novo [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Criado por qualquer API, será de regras de alerta *ser gerenciáveis pela mesma API* – bem como do portal do Azure. Por predefinição, o Azure Monitor irá continuar a utilizar [herdados API de alerta do Log Analytics](api-alerts.md) para criar nova regra de alerta a partir do portal do Azure.

Os impactos do comutador de preferência para scheduledQueryRules API são compilados abaixo:

- Todas as interações feitas para gerir alertas de registo por meio de interfaces de programação deve agora ser feito utilizando [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em vez disso. Para obter mais informações, consulte, [uso de exemplo através do modelo do Azure Resource](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [uso de exemplo através da CLI do Azure e o PowerShell](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Qualquer nova regra alerta de registo criada no portal do Azure, será criada utilizando [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) apenas e permitir que os utilizadores utilizem o [funcionalidades adicionais da nova API](#Benefits-of-switching-to-new-Azure-API) através do portal do Azure também

Todos os clientes que queira mudar voluntariamente para o novo [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e bloquear a utilização a partir do [herdados API de alerta do Log Analytics](api-alerts.md); pode fazê-lo com a realização de uma chamada PUT no abaixo API para mudar o alerta de todos os regras associadas com a área de trabalho do Log Analytics específica.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo de pedido que contém o abaixo JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Se o comutador de todas as regras de alerta na área de trabalho do Log Analytics para utilizar novos [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) é efetuada com êxito, a seguinte resposta será fornecida.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Os utilizadores também podem verificar o estado atual da sua área de trabalho do Log Analytics e ver se tem ou não tiver sido mudada para usar [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) apenas. Para verificar, os utilizadores podem efetuar uma chamada GET no abaixo API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se a área de trabalho do Log Analytics especificada foi mudada para usar [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) apenas; em seguida, a resposta JSON será listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Mais, se a área de trabalho de análise de registo especificada ainda não tiver sido mudada para usar [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) apenas; em seguida, a resposta JSON será listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Monitor do Azure - alertas de registo](alerts-unified-log.md).
- Saiba como criar [alertas de registo nos alertas do Azure](alerts-log.md).
- Saiba mais sobre o [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
