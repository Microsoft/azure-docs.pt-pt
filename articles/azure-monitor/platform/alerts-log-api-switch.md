---
title: Mude de legado Log Analytics alerta API para novo Azure Alerts API
description: Visão geral do legado salvou API de alerta de log analytics baseado em Pesquisa de Registo e processo para mudar as regras de alerta para a nova API ScheduledQueryRules, com detalhes que abordam as preocupações comuns do cliente.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249441"
---
# <a name="switch-api-preference-for-log-alerts"></a>Alternar a preferência da API para alertas de registo

> [!NOTE]
> Conteúdo declarado aplicável aos utilizadores Apenas nuvem pública azure e **não** para o Governo Azure ou nuvem Azure China.  

> [!NOTE]
> Uma vez que um utilizador opte por mudar a preferência para a nova [API agendada,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) não é possível voltar a utilizar o legado antigo [Log Analytics Alert API](api-alerts.md).

Até recentemente, geriu regras de alerta no portal Microsoft Operations Management Suite. A nova experiência de alertas foi integrada com vários serviços no Microsoft Azure, incluindo o Log Analytics, e pedimos para [estender as suas regras de alerta do portal OMS ao Azure.](alerts-extend.md) Mas para garantir a interrupção mínima para os clientes, o processo não alterou a interface programática para o seu consumo - [Log Analytics Alert API](api-alerts.md) com base no SavedSearch.

Mas agora anuncia para o Log Analytics alertar os utilizadores de uma verdadeira alternativa programática Azure, [O Monitor Azure - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), que também é reflexo na sua [faturação Azure - para alertas](alerts-unified-log.md#pricing-and-billing-of-log-alerts)de log . Para saber mais sobre como gerir os seus alertas de registo utilizando a API, consulte a Gestão de [alertas de registo utilizando o Modelo](alerts-log.md#managing-log-alerts-using-azure-resource-template) de Recurso Azure e [gerindo alertas](alerts-log.md#managing-log-alerts-using-powershell)de registo utilizando o PowerShell .

## <a name="benefits-of-switching-to-new-azure-api"></a>Benefícios da mudança para a nova API Azure

Existem várias vantagens em criar e gerir alertas utilizando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) sobre a [legacy Log Analytics Alert API;](api-alerts.md) listamos alguns dos principais abaixo:

- Capacidade de [cruzar a pesquisa](../log-query/cross-workspace-query.md) de registo de log space em regras de alerta e abranger recursos externos como espaços de trabalho Log Analytics ou até aplicações application Insights
- Quando vários campos utilizados para agrupar em consulta, utilizando o utilizador [api](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) aqueryRules agendado pode especificar que campo agregar no portal Azure
- Os alertas de registo criados utilizando a [API agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Podem ter um período definido até 48 horas e recolher dados por um período mais longo do que antes
- Criar regras de alerta num só tiro como um único recurso sem a necessidade de criar três níveis de recursos como com a [api](api-alerts.md) de alerta de log analytics legado
- Interface programática única para todas as variantes de alertas de registo baseados em consultas em Azure - novos [API agendados QueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) podem ser usados para gerir regras para Log Analytics, bem como Insights de Aplicação
- Gerencie os seus alertas de registo utilizando [cmdlets Powershell](alerts-log.md#managing-log-alerts-using-powershell)
- Todas as novas funcionalidades de alerta de log e desenvolvimento futuro estarão disponíveis apenas através da nova [API agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo de mudança do legado Log Alerts API

Os utilizadores são gratuitos para utilizar a [API](api-alerts.md) de Alerta de Log Analytics ou a nova [API agendada.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) As regras de alerta criadas por qualquer API, *serão geríveis apenas pela mesma API* - bem como pelo portal Azure. Por padrão, o Azure Monitor continuará a utilizar a [Legacy Log Analytics Alert API](api-alerts.md) para criar qualquer nova regra de alerta do portal Azure para espaços de trabalho existentes no Log Analytics. Como anunciado novo espaço de trabalho log criado em ou depois de 1 de junho de [2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) - utilizará automaticamente novos [API agendados](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Por padrão, incluindo no portal Azure.

Os impactos da mudança de preferência para a API agendada são compilados abaixo:

- Todas as interações efetuadas para gerir alertas de registo através de interfaces programáticas devem agora ser feitas usando regras [programadas.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Para mais informações, consulte, [a utilização da amostra através do Modelo](alerts-log.md#managing-log-alerts-using-azure-resource-template) de Recurso Azure e a [utilização da amostra através do PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Qualquer nova regra de alerta de log criada no portal Azure, será criada usando apenas regras [programadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) E permitirá que os utilizadores utilizem a [funcionalidade adicional da nova API](#benefits-of-switching-to-new-azure-api) via portal Azure
- A gravidade das regras de alerta de registo passará de: *Crítico, Aviso e Informação,* para *valores de gravidade de 0, 1 e 2*. Juntamente com a opção de criar/atualizar regras de alerta com a severidade 3 e 4 também.

O processo de mudança de regras de alerta do [legado Log Analytics Alert API](api-alerts.md) não envolve alterar de forma alguma a sua definição de alerta, consulta ou configuração. As suas regras de alerta e monitorização não são afetadas e os alertas não param ou serão parados, durante ou após o interruptor. As únicas alterações são:

- Uma alteração na preferência da API e acesso às suas regras através de uma nova API.
- Um recurso de regra de alerta modificado URI contendo os IDs utilizados no [legado Log Analytics Alert API](api-alerts.md) em vez do nome da regra de alerta nesta estrutura `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. O nome do visor da regra de alerta permanecerá inalterado.

Qualquer cliente que deseje mudar voluntariamente para as novas [Regras agendadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e bloquear a utilização do [legado Log Analytics Alert API;](api-alerts.md) pode fazê-lo executando uma chamada PUT na API abaixo para mudar todas as regras de alerta associadas ao espaço de trabalho específico log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo de pedido que contém o JSON abaixo.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

A API também pode ser acedida a partir de uma linha de comando PowerShell utilizando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure. Como ilustrado abaixo, na chamada PUT da amostra utilizando a ferramenta ARMclient para mudar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se a mudança de todas as regras de alerta no espaço de trabalho do Log Analytics utilizar novas Regras [agendadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) será bem sucedida, será dada a seguinte resposta.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Os utilizadores também podem verificar o estado atual do seu espaço de trabalho Log Analytics e ver se foi ou não alterado para utilizar apenas [regras agendadas.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Para verificar, os utilizadores podem realizar uma chamada GET na API abaixo.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Para executar o acima na utilização da linha de comando PowerShell utilizando a ferramenta [ARMClient,](https://github.com/projectkudu/ARMClient) consulte a amostra abaixo.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho de Log Analytics especificado tiver sido mudado para utilizar apenas regras [agendadas;](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em seguida, a resposta JSON será como listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Caso contrário, se o espaço de trabalho analítico de log especificado ainda não tiver sido alterado para utilizar apenas regras [programadas;](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em seguida, a resposta JSON será como listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passos seguintes

- Conheça o [Monitor Azure - Alertas](alerts-unified-log.md)de Registo .
- Saiba como criar alertas de [registo em Alertas Azure](alerts-log.md).
- Saiba mais sobre a [experiência Azure Alerts.](../../azure-monitor/platform/alerts-overview.md)
