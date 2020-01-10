---
title: Alternar da API de alertas de Log Analytics herdados para a nova API de alertas do Azure
description: Visão geral do savedSearch herdado com base Log Analytics API de alerta e processo para alternar as regras de alerta para a nova API ScheduledQueryRules, com detalhes que abordam as preocupações comuns dos clientes.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 5d6b8ce557cb794b3a56ecb3a938a2fe184156ab
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680754"
---
# <a name="switch-api-preference-for-log-alerts"></a>Trocar a preferência de API para os Alertas de Registos

> [!NOTE]
> Conteúdo declarado aplicável a usuários somente na nuvem pública do Azure e **não** para o Azure governamental ou para a nuvem do Azure China.  

Até há pouco tempo, geria as regras de alerta no portal Microsoft Operations Management Suite. A nova experiência de alertas foi integrada a vários serviços em Microsoft Azure, incluindo Log Analytics e solicitamos que [você estenda suas regras de alerta do portal do OMS para o Azure](alerts-extend.md). Mas, para garantir o mínimo de interrupção para os clientes, o processo não alterou a interface programática para sua [API de alerta de log Analytics](api-alerts.md) de consumo com base em SavedSearch.

Mas agora você anuncia para Log Analytics alertar os usuários uma verdadeira alternativa programática do Azure, uma [API Azure monitor-ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), que também é refletida em sua [cobrança do Azure – para alertas de log](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Para saber mais sobre como gerenciar seus alertas de log usando a API, consulte [Gerenciando alertas de log usando o modelo de recursos do Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [Gerenciando alertas de log usando o PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Benefícios de mudar para a nova API do Azure

Há várias vantagens de criar e gerenciar alertas usando a [API do scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) sobre a [API de alerta do log Analytics herdado](api-alerts.md); Listamos alguns dos principais abaixo:

- Capacidade de [fazer a pesquisa de logs de espaço de trabalho](../log-query/cross-workspace-query.md) em regras de alerta e abranger recursos externos, como espaços de trabalho log Analyticss ou até mesmo aplicativos Application insights
- Quando vários campos usados para agrupar na consulta, o uso do usuário da [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pode especificar em qual campo agregar no portal do Azure
- Os alertas de log criados usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) podem ter um período definido até 48 horas e buscar dados por um período maior do que antes
- Crie regras de alerta em uma captura como um único recurso sem a necessidade de criar três níveis de recursos, como com a [API de alerta de log Analytics herdada](api-alerts.md)
- Interface programática única para todas as variantes de alertas de log baseados em consulta no Azure – a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pode ser usada para gerenciar regras para log Analytics, bem como Application insights
- Gerenciar seus alertas de log usando [cmdlets do PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Todas as novas funcionalidades de alerta de log e desenvolvimento futuro estarão disponíveis somente por meio da nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo de troca de API de alertas de log herdado

Os usuários são livres para usar a [API de alerta log Analytics herdada](api-alerts.md) ou a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). As regras de alerta criadas por qualquer API poderão *ser gerenciadas pela mesma API somente* , bem como de portal do Azure. Por padrão, Azure Monitor continuará a usar a [API de alerta log Analytics herdada](api-alerts.md) para criar qualquer nova regra de alerta de portal do Azure para os espaços de trabalho existentes do log Analytics. Conforme [anunciado novo espaço de trabalho de log criado em ou após 1º de junho de 2019,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) o usará automaticamente a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) por padrão, incluindo em portal do Azure.

Os impactos da mudança de preferência para a API scheduledQueryRules são compilados abaixo:

- Todas as interações realizadas para gerenciar alertas de log por meio de interfaces programáticas agora devem ser feitas usando [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . Para obter mais informações, consulte [exemplo de uso por meio do modelo de recurso do Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [uso de exemplo por meio do PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Qualquer nova regra de alerta de log criada no portal do Azure, será criada usando apenas [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e permitirá que os usuários usem a [funcionalidade adicional da nova API](#benefits-of-switching-to-new-azure-api) por meio de portal do Azure também
- A severidade das regras de alerta de log mudará de: *crítico, aviso & informativo*, para *valores de severidade de 0, 1 & 2*. Junto com a opção de criar/atualizar regras de alerta com a severidade 3 e 4 também.

O processo de mover as regras de alerta da [API de alerta de log Analytics herdado](api-alerts.md) não envolve alterar a definição, a consulta ou a configuração de alerta de forma alguma. Suas regras de alerta e o monitoramento não são afetados e os alertas não serão interrompidos ou não serão interrompidos durante ou após o comutador. As únicas alterações são:

- Uma alteração na preferência de API e o acesso às suas regras por meio de uma nova API.
- Um URI de recurso de regra de alerta modificado que contém as IDs usadas na [API de alerta log Analytics herdada](api-alerts.md) em vez do nome da regra de alerta nesta `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`de estrutura. O nome de exibição da regra de alerta permanecerá inalterado.

> [!NOTE]
> Depois que um usuário optar por alternar a preferência para a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , não será possível reverter para o uso da [API de alerta de log Analytics herdada](api-alerts.md)mais antiga.

Qualquer cliente que queira alternar voluntariamente para o novo [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e bloquear o uso da [API de alerta log Analytics herdada](api-alerts.md); pode fazer isso executando uma chamada PUT na API abaixo para alternar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo da solicitação que contém o JSON abaixo.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

A API também pode ser acessada de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API de Azure Resource Manager. Conforme ilustrado abaixo, em exemplo, colocar chamada usando a ferramenta ARMclient para alternar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se a opção de todas as regras de alerta no espaço de trabalho Log Analytics para usar o New [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) for bem-sucedida, a seguinte resposta será fornecida.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Os usuários também podem verificar o status atual do seu espaço de trabalho Log Analytics e ver se ele tem ou não foi alternado para usar apenas [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) . Para verificar, os usuários podem executar uma chamada GET na API abaixo.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Para executar o acima em usando a linha de comando do PowerShell usando a ferramenta [ARMClient](https://github.com/projectkudu/ARMClient) , consulte o exemplo abaixo.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho Log Analytics especificado foi alternado para usar somente [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ; em seguida, o JSON de resposta será listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Caso contrário, se o espaço de trabalho analítico de log especificado ainda não tiver sido alternado para usar somente [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) ; em seguida, o JSON de resposta será listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [alertas de log de Azure monitor](alerts-unified-log.md).
- Saiba como criar [alertas de log nos alertas do Azure](alerts-log.md).
- Saiba mais sobre a [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
