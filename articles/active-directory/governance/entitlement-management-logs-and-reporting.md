---
title: Relatório de & de arquivo com a Azure Monitor - Gestão de direitos AD Azure
description: Saiba como arquivar registos e criar relatórios com o Azure Monitor na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c5ab92fcc1d70d12e37ae351e768514b4e7522f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501707"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Registos de arquivo e reportagens sobre a gestão de direitos da AD Azure no Azure Monitor

A Azure AD armazena eventos de auditoria por até 30 dias no registo de auditoria. No entanto, pode manter os dados de auditoria por mais tempo do que o período de retenção predefinido, delineado em [Quanto tempo a Azure AD armazena dados de reporte?](../reports-monitoring/reference-reports-data-retention.md) Em seguida, pode utilizar livros de trabalho e consultas personalizadas e relatórios sobre estes dados.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Configure AD AD para usar monitor Azure
Antes de utilizar os livros de trabalho do Azure Monitor, tem de configurar a Azure AD para enviar uma cópia dos seus registos de auditoria ao Azure Monitor.

Os registos de auditoria da Azure AD requer que tenha o Azure Monitor numa subscrição do Azure. Pode ler mais sobre os pré-requisitos e os custos estimados da utilização do Azure Monitor em [registos de atividade AZure AD no Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Papel pré-requisito**: Administração Global

1. Inscreva-se no portal Azure como um utilizador que é um Administrador Global. Certifique-se de que tem acesso ao grupo de recursos que contém o espaço de trabalho do Azure Monitor.
 
1. Selecione **O Diretório Ativo Azure** e, em seguida, clique em **Definições de Diagnóstico** em Monitorização no menu de navegação à esquerda. Verifique se já há uma definição para enviar os registos de auditoria para o espaço de trabalho.

1. Se já não houver uma definição, clique na **definição de diagnóstico de adicionar**. Utilize as instruções do artigo [Integre os registos Azure AD com os registos do Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) para enviar o registo de auditoria AZure AD para o espaço de trabalho do Azure Monitor.

    ![Painel de definições de diagnóstico](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Depois de o registo ser enviado para o Azure Monitor, selecione **log analytics espaços de trabalho** e selecione o espaço de trabalho que contém os registos de auditoria AZure AD.

1. Selecione **Utilização e custos estimados** e clique em **Retenção de Dados.** Altere o slider para o número de dias que pretende manter os dados para satisfazer os seus requisitos de auditoria.

    ![Painel de espaços de trabalho Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Mais tarde, para ver a gama de datas realizadas no seu espaço de trabalho, pode utilizar o manual *de datas de registo arquivado:*  
    
    1. Selecione **Azure Ative Directory** e, em **seguida,** clique em Livros de Trabalho . 
    
    1. Expandir a secção **Azure Ative Directory Troubleshooting** e clicar no **Intervalo de Data de Registo Arquivado**. 


## <a name="view-events-for-an-access-package"></a>Ver eventos para um pacote de acesso  

Para visualizar eventos para um pacote de acesso, você deve ter acesso ao espaço de trabalho do monitor Azure subjacente (ver [Gerir o acesso a dados de registo e espaços de trabalho em Azure Monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) para obter informações) e numa das seguintes funções: 

- Administrador global  
- Administrador de segurança  
- Leitor de segurança  
- Leitor de relatórios  
- Administrador de aplicação  

Utilize o seguinte procedimento para visualizar eventos: 

1. No portal Azure, selecione **Azure Ative Directory** e, em seguida, clique em **Livros de Trabalho**. Se tiver apenas uma subscrição, passe para o passo 3. 

1. Se tiver várias subscrições, selecione a subscrição que contém o espaço de trabalho.  

1. Selecione o livro denominado *Access Package Activity*. 

1. Nesse livro, selecione um intervalo de tempo (altere para **Todos** se não tiver certeza), e selecione um pacote de acesso Id da lista de todos os pacotes de acesso que tiveram atividade durante esse intervalo de tempo. Serão apresentados os eventos relacionados com o pacote de acesso que ocorreu durante o intervalo de tempo selecionado.  

    ![Ver eventos de pacote de acesso](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Cada linha inclui o tempo, o id do pacote de acesso, o nome da operação, o Id do objeto, UPN, e o nome de exibição do utilizador que iniciou a operação.  Detalhes adicionais estão incluídos no JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Crie consultas personalizadas do Azure Monitor utilizando o portal Azure
Você pode criar suas próprias consultas em eventos de auditoria Azure AD, incluindo eventos de gestão de direitos.  

1. No Diretório Ativo Azure do portal Azure, clique em **Registos** sob a secção de Monitorização no menu de navegação à esquerda para criar uma nova página de consulta.

1. O seu espaço de trabalho deve ser mostrado na parte superior esquerda da página de consulta. Se tiver vários espaços de trabalho do Azure Monitor e o espaço de trabalho que está a utilizar para armazenar eventos de auditoria AZure AD não for mostrado, clique em **Select Scope**. Em seguida, selecione a subscrição correta e o espaço de trabalho.

1. Em seguida, na área de texto de consulta, elimine a cadeia "procurar *" e substitua-a pela seguinte consulta:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Em seguida, clique em **Executar**. 

    ![Clique em Executar para iniciar consulta](./media/entitlement-management-logs-and-reporting/run-query.png)

A tabela mostrará os eventos de registo de auditoria para gestão de direitos a partir da última hora por defeito. Pode alterar a definição "Intervalo de tempo" para visualizar eventos mais antigos. No entanto, a alteração desta definição só mostrará eventos que ocorreram após a configuração do Azure AD para enviar eventos para o Azure Monitor.

Se quiser conhecer os mais antigos e mais recentes eventos de auditoria realizados no Azure Monitor, utilize a seguinte consulta:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Para obter mais informações sobre as colunas armazenadas para eventos de auditoria no Azure Monitor, consulte [o esquema de registos de auditoria Azure AD no Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Crie consultas personalizadas do Azure Monitor utilizando a Azure PowerShell

Pode aceder a registos através do PowerShell depois de configurar o Azure AD para enviar registos para o Azure Monitor. Em seguida, envie consultas de scripts ou da linha de comando PowerShell, sem precisar de ser um Administrador Global no inquilino. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Certifique-se de que o utilizador ou o principal de serviço tem a correta atribuição de funções

Certifique-se de que você, o utilizador ou o principal de serviço que autenticará a AZure AD, estão na função Azure apropriada no espaço de trabalho Log Analytics. As opções de função são o Log Analytics Reader ou o Log Analytics Contributor. Se já está numa dessas funções, então salte para [o Retrieve Log Analytics ID com uma subscrição do Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Para definir a atribuição de funções e criar uma consulta, faça os seguintes passos:

1. No portal Azure, localize o [espaço de trabalho Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Selecione **Controlo de Acesso (IAM)**.

1. Em seguida, clique **em Adicionar** para adicionar uma atribuição de função.

    ![Adicionar uma atribuição de função](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalar módulo Azure PowerShell

Assim que tiver a atribuição de funções adequada, lance o PowerShell e [instale o módulo Azure PowerShell](/powershell/azure/install-az-ps) (se ainda não tiver), digitando:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Agora está pronto para autenticar a Azure AD e recuperar a identificação do espaço de trabalho Log Analytics que está a consultar.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Recuperar Registo Analytics ID com uma subscrição do Azure
Se tiver apenas uma subscrição Azure e um único espaço de trabalho log analytics, escreva o seguinte para autenticar a AZure AD, ligue-se a essa subscrição e recupere esse espaço de trabalho:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Recuperar Registo Analytics ID com várias subscrições Azure

 [O Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) opera numa subscrição de cada vez. Por isso, se tiver várias subscrições do Azure, deverá certificar-se de que se conecta ao que tem o espaço de trabalho do Log Analytics com os registos AD do Azure. 
 
 Os cmdlets seguintes exibem uma lista de subscrições e encontram o ID da subscrição que tem o espaço de trabalho Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Pode reautorá-la e associar a sua sessão PowerShell a essa subscrição utilizando um comando como `Connect-AzAccount –Subscription $subs[0].id` . Para saber mais sobre como autenticar a Azure da PowerShell, incluindo não-interactivamente, consulte [Iniciar súm em Azure PowerShell](/powershell/azure/authenticate-azureps).

Se tiver vários espaços de trabalho do Log Analytics nessa subscrição, então o cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) devolve a lista de espaços de trabalho. Então pode encontrar o que tem os registos AD Azure. O `CustomerId` campo devolvido por este cmdlet é o mesmo que o valor do "Workspace Id" exibido no portal Azure na visão geral do espaço de trabalho log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Envie a consulta para o espaço de trabalho Log Analytics
Finalmente, uma vez identificado um espaço de trabalho, pode utilizar [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para enviar uma consulta kusto para esse espaço de trabalho. Estas consultas estão [escritas na língua de consulta de Kusto.](/azure/kusto/query/)
 
Por exemplo, pode recuperar o intervalo de datas dos registos de eventos de auditoria a partir do espaço de trabalho Log Analytics, com cmdlets PowerShell para enviar uma consulta como:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Também pode recuperar eventos de gestão de direitos usando uma consulta como:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Passos seguintes:
- [Criar relatórios interativos com livros do Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md)