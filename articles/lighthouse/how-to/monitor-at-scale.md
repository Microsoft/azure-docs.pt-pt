---
title: Monitorar recursos delegados à escala
description: Aprenda a utilizar eficazmente os Registos do Monitor Azure de forma escalável através dos inquilinos do cliente que está a gerir.
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 98fd984492276dbdfbc2f8001bca19560764a2a7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742590"
---
# <a name="monitor-delegated-resources-at-scale"></a>Monitorar recursos delegados à escala

Como prestador de serviços, pode ter a bordo vários inquilinos de clientes para o [Farol de Azure.](../overview.md) O Azure Lighthouse permite que os prestadores de serviços realizem operações em escala em vários inquilinos ao mesmo tempo, tornando as tarefas de gestão mais eficientes.

Este tópico mostra-lhe como usar [os Registos do Monitor Azure](../../azure-monitor/logs/data-platform-logs.md) de uma forma escalável através dos inquilinos do cliente que você está gerindo. Embora nos refiramos a prestadores de serviços e clientes neste tópico, esta orientação também se aplica às [empresas que utilizam o Farol Azure para gerir vários inquilinos.](../concepts/enterprise.md)

> [!NOTE]
> Certifique-se de que os utilizadores dos seus inquilinos de gestão receberam as [funções necessárias para gerir espaços de trabalho do Log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) nas subscrições de clientes delegados.

## <a name="create-log-analytics-workspaces"></a>Criar espaços de trabalho log analytics

Para recolher dados, terá de criar espaços de trabalho do Log Analytics. Estes espaços de trabalho Log Analytics são ambientes únicos para dados recolhidos pelo Azure Monitor. Cada espaço de trabalho tem o seu próprio repositório de dados e configuração, e as fontes de dados e soluções são configuradas para armazenar os seus dados num determinado espaço de trabalho.

Recomendamos a criação destes espaços de trabalho diretamente nos inquilinos do cliente. Desta forma, os seus dados permanecem nos seus inquilinos em vez de serem exportados para os seus. Isto também permite uma monitorização centralizada de quaisquer recursos ou serviços suportados pelo Log Analytics, proporcionando-lhe mais flexibilidade sobre os tipos de dados que monitoriza.

> [!TIP]
> Qualquer conta de automação utilizada para aceder a dados de um espaço de trabalho log analytics deve ser criada no mesmo inquilino que o espaço de trabalho.

Pode criar um espaço de trabalho Log Analytics utilizando o [portal Azure,](../../azure-monitor/logs/quick-create-workspace.md)utilizando [o Azure CLI,](../../azure-monitor/logs/quick-create-workspace-cli.md)ou utilizando [o Azure PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md).

> [!IMPORTANT]
> Mesmo que todos os espaços de trabalho sejam criados no inquilino do cliente, o fornecedor de recursos Microsoft.Insights também deve ser registado numa subscrição no inquilino gerente.

## <a name="deploy-policies-that-log-data"></a>Implementar políticas que registam dados

Uma vez criado os seus espaços de trabalho Log Analytics, pode implementar [a Política Azure](../../governance/policy/index.yml) através das suas hierarquias de clientes para que os dados de diagnóstico são enviados para o espaço de trabalho apropriado em cada inquilino. As políticas exatas que implementa podem variar consoante os tipos de recursos que pretende monitorizar.

Para saber mais sobre a criação de políticas, consulte [Tutorial: Criar e gerir políticas para impor o cumprimento.](../../governance/policy/tutorials/create-and-manage.md) Esta [ferramenta comunitária](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) fornece um script para ajudá-lo a criar políticas para monitorizar os tipos específicos de recursos que você escolhe.

Quando tiver determinado quais as políticas a implementar, pode [implantá-las nas suas subscrições delegadas à escala](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>Analisar os dados recolhidos

Depois de implementar as suas políticas, os dados serão registados nos espaços de trabalho do Log Analytics que criou em cada cliente inquilino. Para obter informações em todos os clientes geridos, pode utilizar ferramentas como [os Livros de Trabalho do Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md) para recolher e analisar informações de várias fontes de dados.

## <a name="view-alerts-across-customers"></a>Ver alertas entre os clientes

Pode ver [alertas](../../azure-monitor/alerts/alerts-overview.md) para as subscrições delegadas nos inquilinos do cliente que gere.

A partir do seu inquilino gerente, pode [criar, visualizar e gerir alertas de registo de atividades](../../azure-monitor/alerts/alerts-activity-log.md) no portal Azure ou através de APIs e ferramentas de gestão.

Para atualizar os alertas automaticamente em vários clientes, utilize uma consulta [de Gráfico de Recursos Azure](../../governance/resource-graph/overview.md) para filtrar alertas. Pode fixar a consulta no seu painel de instrumentos e selecionar todos os clientes e subscrições apropriados. Por exemplo, a consulta abaixo apresentará alertas de severidade 0 e 1, refrescantes a cada 60 minutos.

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>Passos seguintes

- Experimente os [registos de atividade por livro de domínio](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) no GitHub.
- Explore este [livro de amostras construído pelo MVP,](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)que rastreia o relatório de conformidade de remendos [consultando registos de Gestão de Atualização](../../automation/update-management/query-logs.md) em vários espaços de trabalho do Log Analytics. 
- Conheça outras [experiências de gestão de inquilinos cruzados.](../concepts/cross-tenant-management-experience.md)