---
title: Upgrade para o atual Azure Monitor Alertas API
description: Saiba como mudar para os alertas de registo AgendadosQueryRules API
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294518"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>Upgrade para o atual Log Alertas API do legado Log Analytics Alert API

> [!NOTE]
> Este artigo só é relevante para o público de Azure (**não** para o Governo de Azure ou para a nuvem da China Azure).

> [!NOTE]
> Uma vez que um utilizador opte por mudar a preferência para a atual [API de Regras Desípoadas de Marcação,](/rest/api/monitor/scheduledqueryrules) não é possível voltar ao [antigo legado Log Analytics Alert API](api-alerts.md).

No passado, os utilizadores usaram o [legado Log Analytics Alert API](api-alerts.md) para gerir as regras de alerta de registo. Os espaços de trabalho atuais utilizam [a API de Regras Desírredas Programadas.](/rest/api/monitor/scheduledqueryrules) Este artigo descreve os benefícios e o processo de mudança da API legado para a API atual.

## <a name="benefits"></a>Benefícios

- Modelo único para a criação de regras de alerta (anteriormente necessários três modelos separados).
- API única para espaços de trabalho log analytics ou recursos de Insights de Aplicação.
- [Suporte de cmdlets PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Alinhamento de severidades com todos os outros tipos de alerta.
- Capacidade de criar [alerta de log de espaço de trabalho transversal](../log-query/cross-workspace-query.md) que abrange vários recursos externos, como espaços de trabalho Log Analytics ou recursos de Insights de Aplicação.
- Os utilizadores podem especificar dimensões para dividir os alertas utilizando o parâmetro 'Agregar On'.
- Os alertas de registo têm um período prolongado de até dois dias de dados (previamente limitados a um dia).

## <a name="impact"></a>Impacto

- Todas as novas regras devem ser criadas/editadas com a API atual. Consulte a [utilização da amostra através do modelo de recursos Azure](alerts-log-create-templates.md) e [a utilização da amostra através do PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- À medida que as regras se tornam Azure Resource Manager rastreou recursos na atual API e deve ser único, o ID de recursos de regras mudará para esta estrutura: `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . Os nomes da regra de alerta permanecerão inalterados.

## <a name="process"></a>Processo

O processo de comutação não é interativo e não requer passos manuais, na maioria dos casos. As suas regras de alerta não são paradas ou paradas, durante ou após o interruptor.
Faça esta chamada para mudar todas as regras de alerta associadas ao espaço de trabalho específico do Log Analytics:

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo de pedido contendo o Abaixo JSON:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Aqui está um exemplo de utilização do [ARMClient,](https://github.com/projectkudu/ARMClient)uma ferramenta de linha de comando de código aberto, que simplifica a invocação da chamada api acima:

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se o interruptor for bem sucedido, a resposta é:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>Verifique o estado da comutação do espaço de trabalho

Também pode utilizar esta chamada API para verificar o estado do comutação:

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Também pode utilizar a ferramenta [ARMClient:](https://github.com/projectkudu/ARMClient)

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho Log Analytics foi mudado para [API de Regras Desígrados Agendados,](/rest/api/monitor/scheduledqueryrules)a resposta é:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Se o espaço de trabalho Log Analytics não foi alterado, a resposta é:

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [Monitor Azure - Alertas de Registo](alerts-unified-log.md).
- Saiba como [gerir os seus alertas de registo utilizando a API](alerts-log-create-templates.md).
- Saiba como [gerir os alertas de registo utilizando o PowerShell](alerts-log.md#managing-log-alerts-using-powershell).
- Saiba mais sobre a [experiência Azure Alerts.](./alerts-overview.md)
