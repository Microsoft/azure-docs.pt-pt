---
title: Mude para a nova AZure Alerts API
description: Visão geral do legado salvou API de alerta de log analytics baseado em Log Analytics e processo para mudar as regras de alerta para a nova API DeQueryRules Agendadas, com detalhes que abordam as preocupações comuns dos clientes.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7e1073817013d45558a9679a4f70db0c002cfaa9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324085"
---
# <a name="switch-api-preference-for-log-alerts"></a>Preferência da API para alertas de registo

> [!NOTE]
> Conteúdo declarado aplicável apenas aos utilizadores Azure nuvem pública e **não** para O Governo Azure ou nuvem Azure China.  

> [!NOTE]
> Uma vez que um utilizador opte por mudar a preferência para a nova [API agendadaqueryRules,](/rest/api/monitor/scheduledqueryrules) não é possível voltar a utilizar o [antigo legado Log Analytics Alert API](api-alerts.md).

Até recentemente, geriu regras de alerta no portal Microsoft Operations Management Suite. A nova experiência de alerta foi integrada com vários serviços no Microsoft Azure, incluindo o Log Analytics, e pedimos que [alargassem as suas regras de alerta do portal OMS para o Azure.](./alerts-unified-log.md) Mas para garantir uma interrupção mínima para os clientes, o processo não alterou a interface programática para o seu consumo - [Log Analytics Alert API](api-alerts.md) com base no SaveSearch.

Mas agora anuncia para o Log Analytics alertando os utilizadores uma verdadeira alternativa programática [Azure, Azure Monitor - AgendadoQueryRules API](/rest/api/monitor/scheduledqueryrules), que também é reflexo na sua [faturação Azure - para alertas de registo](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Para saber mais sobre como gerir os seus alertas de registo utilizando a API, consulte [os alertas de registo de gestão utilizando o Modelo de Recursos Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [Gestão de alertas de registo utilizando o PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Benefícios da mudança para a nova AZURE API

Existem várias vantagens de criar e gerir alertas utilizando [a API de Regras Descontídricas Programadas](/rest/api/monitor/scheduledqueryrules) sobre [o legado Log Analytics Alert API;](api-alerts.md) enumeramos alguns dos principais abaixo:

- Capacidade de cruzar a pesquisa de [registos de espaço de trabalho](../log-query/cross-workspace-query.md) em regras de alerta e abranger recursos externos como espaços de trabalho do Log Analytics ou até mesmo aplicações Application Insights
- Quando vários campos utilizados para a consulta do Grupo, utilizando o utilizador [API de Regras API programados,](/rest/api/monitor/scheduledqueryrules) pode especificar qual o campo a agregar no portal Azure
- Os alertas de registo criados utilizando [a API de Regras Descontídicas Agendadas](/rest/api/monitor/scheduledqueryrules) podem ter um período definido até 48 horas e recolher dados por um período mais longo do que antes
- Crie regras de alerta num único recurso sem a necessidade de criar três níveis de recursos como com o [legado Log Analytics Alert API](api-alerts.md)
- Interface programática única para todas as variantes de alertas de registo baseados em consultas em Azure - novos [API de Regras DeQueryRules agendados](/rest/api/monitor/scheduledqueryrules) podem ser usados para gerir regras para Log Analytics, bem como Insights de Aplicações
- Gerencie os seus alertas de registo utilizando [cmdlets Powershell](alerts-log.md#managing-log-alerts-using-powershell)
- Toda a nova funcionalidade de alerta de registo e desenvolvimento futuro só estará disponível através da nova [API agendada para os Acordos](/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo de comutação do legado Alertas de Registo API

Os utilizadores são gratuitos para utilizar a [API de alerta de log analytics legado](api-alerts.md) ou a nova [API agendada para oQueryRules.](/rest/api/monitor/scheduledqueryrules) As regras de alerta criadas pela API, *serão geríveis apenas pela mesma API* - bem como pelo portal Azure. Por padrão, o Azure Monitor continuará a utilizar a [API de alerta de log analytics para](api-alerts.md) criar qualquer nova regra de alerta do portal Azure para espaços de trabalho existentes do Log Analytics. Conforme [anunciado novo espaço de trabalho Log criado em ou depois de 1 de junho de 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) - utilizará automaticamente o novo [API de Regras Descontágios Agendados](/rest/api/monitor/scheduledqueryrules) por padrão, incluindo no portal Azure.

Os impactos do interruptor de preferência para a API de Regras Desconseradas programadas são compilados abaixo:

- Todas as interações efetuadas para gerir os alertas de registo através de interfaces programáticas devem agora ser feitas usando [osQueryRules programados.](/rest/api/monitor/scheduledqueryrules) Para mais informações, consulte, [a utilização da amostra através do modelo de recursos Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [a utilização da amostra através do PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Qualquer nova regra de alerta de registo criada no portal Azure, será criada usando [apenas as Configurações DeSerculas programadas](/rest/api/monitor/scheduledqueryrules) e permitirá que os utilizadores utilizem a [funcionalidade adicional de novo API](#benefits-of-switching-to-new-azure-api) através do portal Azure.
- As regras de alerta de registo passarão de: *Crítico, Aviso & Informativo,* para *Valores de Severidade de 0, 1 & 2*. Juntamente com a opção de criar/atualizar regras de alerta com severidade 3 e 4 também.

O processo de mudança de regras de alerta do [legacy Log Analytics Alert API](api-alerts.md) não envolve alterar a definição de alerta, consulta ou configuração de qualquer forma. As suas regras de alerta e monitorização não são afetadas e os alertas não pararão nem ficarão parados, durante ou após o interruptor. As únicas alterações são:

- Uma alteração na preferência da API e acesso às suas regras através de uma nova API.
- Um recurso de regra de alerta modificado URI contendo os IDs utilizados no [legado Log Analytics Alert API](api-alerts.md) em vez do nome da regra de alerta nesta estrutura `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . O nome do visor da regra de alerta permanecerá inalterado.

Qualquer cliente que pretenda mudar voluntariamente para os novos [Acordos Agendados](/rest/api/monitor/scheduledqueryrules) e bloquear a utilização a partir do [legado Log Analytics Alert API;](api-alerts.md) pode fazê-lo executando uma chamada PUT na API abaixo para mudar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo de pedido contendo o JSON abaixo.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

A API também pode ser acedida a partir de uma linha de comando PowerShell utilizando [o ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de código aberto que simplifica a invocação da API do Gestor de Recursos Azure. Como ilustrado abaixo, na chamada PUT da amostra utilizando a ferramenta ARMclient para mudar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se a mudança de todas as regras de alerta no espaço de trabalho Do Log Analytics para utilizar novas [regras agendadas](/rest/api/monitor/scheduledqueryrules) de Acordo, será fornecida a seguinte resposta.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Os utilizadores também podem verificar o estado atual do seu espaço de trabalho Log Analytics e ver se ele foi ou não mudado para usar [apenas as Regras DeMarcas agendadas.](/rest/api/monitor/scheduledqueryrules) Para verificar, os utilizadores podem realizar uma chamada GET na API abaixo.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Para executar o acima na utilização da linha de comando PowerShell utilizando a ferramenta [ARMClient,](https://github.com/projectkudu/ARMClient) consulte a amostra abaixo.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho especificado do Log Analytics tiver sido mudado apenas para utilizar [as regras de Consta programadas;](/rest/api/monitor/scheduledqueryrules) então a resposta JSON será listada abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Caso contrário, se o espaço de trabalho especificado de Log Analytic ainda não tiver sido alterado para utilizar [apenas osQueryRules programados;](/rest/api/monitor/scheduledqueryrules) então a resposta JSON será listada abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Monitor Azure - Alertas de Registo](alerts-unified-log.md).
- Saiba como criar [alertas de registo em Alertas Azure](alerts-log.md).
- Saiba mais sobre a [experiência Azure Alerts.](./alerts-overview.md)

