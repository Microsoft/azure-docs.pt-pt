---
title: Archive & report com o Azure Monitor - Gestão de direitos da Azure AD
description: Saiba como arquivar registos e criar relatórios com o Azure Monitor na gestão de direitos do Diretório Ativo Azure.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/27/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f89af42e32783de479c4302b19c0a7ddc1289bb8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202185"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Registos de arquivo e reportagens sobre a gestão de direitos da Azure AD no Monitor Azure

A Azure AD armazena eventos de auditoria durante um n.o 30 dias no registo de auditoria. No entanto, pode manter os dados de auditoria por mais tempo do que o período de retenção por defeito, delineados em Quanto tempo é que a [loja Azure AD reporta dados?](../reports-monitoring/reference-reports-data-retention.md) Em seguida, pode utilizar livros de trabalho e consultas personalizadas e relatórios sobre estes dados.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configure Azure AD para usar o Monitor Azure
Antes de utilizar os livros do Monitor Azure, tem de configurar o Azure AD para enviar uma cópia dos seus registos de auditoria ao Monitor Azure.

Os registos de auditoria da AD Do Arquivo Azure exigem que tenha o Monitor Azure numa subscrição Azure. Pode ler mais sobre os pré-requisitos e custos estimados da utilização do Monitor Azure em registos de atividade da [Azure AD no Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Papel pré-requisito**: Global Admin

1. Inscreva-se no portal Azure como um utilizador que é um Administrador Global. Certifique-se de que tem acesso ao grupo de recursos que contém o espaço de trabalho do Monitor Azure.
 
1. Selecione **Diretório Ativo Azure** e, em seguida, clique em **definições** de diagnóstico sob monitorização no menu de navegação esquerdo. Verifique se já existe uma definição para enviar os registos de auditoria para aquele espaço de trabalho.

1. Se já não houver uma definição, clique em **Adicionar definição de diagnóstico**. Utilize as instruções do artigo [Integrar registos adctos Azure com registos do Monitor Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) para enviar o registo de auditoria da AD Azure para o espaço de trabalho do Monitor Azure.

    ![Painel de definições de diagnóstico](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Depois de o registo ser enviado para o Monitor Azure, selecione espaços de **trabalho do Log Analytics**e selecione o espaço de trabalho que contém os registos de auditoria da AD Azure.

1. Selecione **utilização e custos estimados** e clique em **Retenção de Dados**. Mude o slider para o número de dias que pretende manter os dados para satisfazer os seus requisitos de auditoria.

    ![Painel de espaços de trabalho de Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Criar consultas personalizadas do Monitor Azure utilizando o portal Azure
Você pode criar suas próprias consultas sobre eventos de auditoria da AD Azure, incluindo eventos de gestão de direitos.  

1. No Diretório Ativo Azure do portal Azure, clique em **Logs** sob a secção de Monitorização no menu de navegação à esquerda para criar uma nova página de consulta.

1. O seu espaço de trabalho deve ser mostrado na parte superior esquerda da página de consulta. Se tiver vários espaços de trabalho do Monitor Azure e o espaço de trabalho que está a usar para armazenar eventos de auditoria da Azure AD não é mostrado, clique em **Select Scope**. Em seguida, selecione a subscrição correta e o espaço de trabalho.

1. Em seguida, na área de texto de consulta, elimine a corda "search *" e substitua-a pela seguinte consulta:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Em seguida, clique em **Executar**. 

    ![Clique em Correr para iniciar consulta](./media/entitlement-management-logs-and-reporting/run-query.png)

A tabela mostrará os eventos de registo de auditoria para a gestão de direitos a partir da última hora por padrão. Pode alterar a definição de "intervalo de tempo" para ver eventos mais antigos. No entanto, a alteração desta definição só mostrará eventos ocorridos após a configuração da Azure AD para enviar eventos para o Azure Monitor.

Se quiser conhecer os mais antigos e recentes eventos de auditoria realizados no Azure Monitor, utilize a seguinte consulta:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Para obter mais informações sobre as colunas armazenadas para eventos de auditoria no Monitor Azure, consulte [Interprete os registos de auditoria da AD Azure no Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Criar consultas personalizadas do Monitor Azure usando o Azure PowerShell

Pode aceder a registos através do PowerShell depois de configurar o Azure AD para enviar registos para o Monitor Azure. Em seguida, envie consultas de scripts ou da linha de comando PowerShell, sem precisar de ser um Administrador Global no inquilino. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Certifique-se de que o utilizador ou diretor de serviço tem a tarefa correta

Certifique-se de que o utilizador ou diretor de serviço que irá autenticar a Azure AD, está no papel de Azure adequado no espaço de trabalho log Analytics. As opções de funções são o Log Analytics Reader ou o Log Analytics Contributor. Se já se encontra numa dessas funções, salte para o Id de Análise de [Registo sinuoso com uma subscrição Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Para definir a atribuição de funções e criar uma consulta, faça os seguintes passos:
1. No portal Azure, localize o espaço de [trabalho log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecione **Controlo de Acesso (IAM)** .

1. Em seguida, clique em **Adicionar** uma atribuição de funções.

    ![Adicionar uma atribuição de funções](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instale módulo PowerShell Azure

Assim que tiver a atribuição de funções apropriada, inicie o PowerShell e [instale o módulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (se ainda não o fez), digitando:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Agora está pronto para autenticar o Azure AD e recuperar a identificação do espaço de trabalho do Log Analytics que está a consultar.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recuperar id de log analytics com uma subscrição Azure
Se tiver apenas uma subscrição Azure, e um único espaço de trabalho log Analytics, escreva o seguinte para autenticar a AD Azure, ligue-se a essa subscrição e recupere esse espaço de trabalho:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recuperar id de Log Analytics com várias subscrições do Azure

 [O Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) opera numa subscrição de cada vez. Assim, se tiver várias subscrições do Azure, terá de se certificar de que se conecta ao que tem o espaço de trabalho log Analytics com os registos da AD Azure. 
 
 Os seguintes cmdlets apresentam uma lista de subscrições, e encontram a identificação da subscrição que tem o espaço de trabalho Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Pode reautenticar e associar a sua sessão PowerShell a essa subscrição utilizando um comando como `Connect-AzAccount –Subscription $subs[0].id`. Para saber mais sobre como autenticar o Azure a partir da PowerShell, incluindo não interactivamente, consulte [O Sign in com o Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Se tiver vários espaços de trabalho log analytics nessa subscrição, então o cmdlet [Get-AzOperationalInsightsWorkspace devolve](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) a lista de espaços de trabalho. Depois pode encontrar aquele que tem os registos da AD Azure. O campo `CustomerId` devolvido por este cmdlet é o mesmo que o valor do "Workspace id" exibido no portal Azure na visão geral do espaço de trabalho Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Envie a consulta para o espaço de trabalho log Analytics
Finalmente, uma vez identificado um espaço de trabalho, pode utilizar a [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) para enviar uma consulta de Kusto para esse espaço de trabalho. Estas consultas estão escritas em [linguagem de consulta kusto.](https://docs.microsoft.com/azure/kusto/query/)
 
Por exemplo, pode recuperar a gama de datas dos registos do evento de auditoria do espaço de trabalho log Analytics, com cmdlets PowerShell para enviar uma consulta como:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Também pode recuperar eventos de gestão de direitos usando uma consulta como:

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Passos seguintes:
- [Criar relatórios interativos com livros do Monitor Azure](../../azure-monitor/app/usage-workbooks.md) 

