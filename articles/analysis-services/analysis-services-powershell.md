---
title: Gerir o Azure Analysis Services com o PowerShell | Documentos da Microsoft
description: Gestão de Analysis Services do Azure com o PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1f9c30f1c914f6c8d42967e014d967ba0d5b85cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893848"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerir o Azure Analysis Services com o PowerShell

Este artigo descreve os cmdlets do PowerShell utilizados para realizar tarefas de gerenciamento de banco de dados e de servidor Azure Analysis Services. 

Tarefas de gestão de servidor, como criar ou eliminar um servidor, suspender ou retomar as operações de servidor ou alterar o nível de serviço (escalão) utilizam cmdlets do Azure Resource Manager (recurso) e os cmdlets do Analysis Services (servidor). Outras tarefas para gerir bases de dados, como adicionar ou remover membros de funções, processamento ou criação de partições utilizam os cmdlets incluídos no mesmo módulo SqlServer como SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas do PowerShell necessitam de que ter privilégios de administrador no servidor do Analysis Services que está a gerir. As tarefas agendadas do PowerShell são operações autónomas. A conta ou a executar o agendador principal de serviço, tem de ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidor utilizando cmdlets do PowerShell do Azure, sua conta ou a conta que executa o scheduler também tem de pertencer à função de proprietário para o recurso no [controlo de acesso de controlo (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-management-operations"></a>Operações de gestão de recursos 

Módulo - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Cmdlet|Descrição| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|Obtém os detalhes de uma instância de servidor.|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|Cria uma instância de servidor.|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|Cria uma nova configuração de firewall do Analysis Services.|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|Cria uma nova regra de firewall do Analysis Services.|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|Remove uma instância de servidor.|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|Retoma uma instância de servidor.|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|Suspende uma instância de servidor.| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|Modifica uma instância de servidor.|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|Testa a existência de uma instância de servidor.| 

## <a name="server-management-operations"></a>Operações de gestão de servidor

Módulo - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|Descrição| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|Adiciona uma conta autenticada a utilizar para pedidos de cmdlet do servidor Azure Analysis Services.| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|Exporta um registo de uma instância do servidor do Analysis Services no atualmente conectado no ambiente conforme especificado no comando de Add-AzAnalysisServicesAccount|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Reinicia uma instância do servidor do Analysis Services no ambiente de atualmente com sessão iniciado; especificado no comando de Add-AzAnalysisServicesAccount.|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|Sincroniza um banco de dados especificado na instância especificada do servidor do Analysis Services para todas as instâncias de aumentar horizontalmente a consulta atualmente conectado no ambiente conforme especificado no comando de Add-AzAnalysisServicesAccount no|  

## <a name="database-operations"></a>Operações de base de dados

Operações de base de dados do Analysis Services do Azure, utilize o mesmo [módulo SqlServer](https://www.powershellgallery.com/packages/SqlServer) como SQL Server Analysis Services. No entanto, nem todos os cmdlets são suportados para o Azure Analysis Services. Para obter mais informações, consulte [PowerShell do SQL Server](https://docs.microsoft.com/sql/powershell/sql-server-powershell).

O módulo SqlServer fornece cmdlets de gestão de base de dados específicos a tarefas, bem como o cmdlet Invoke-Scmd para fins gerais que aceita uma consulta de linguagem de scripts de modelo em tabela (TMSL) ou um script. Os seguintes cmdlets no módulo SqlServer são suportados para o Azure Analysis Services.

  
|Cmdlet|Descrição|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicione um membro a uma função de base de dados.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Cópia de segurança uma base de dados do Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remova um membro de uma função de base de dados.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Execute um script TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Processe uma base de dados.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Processe uma partição.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Processo de uma tabela.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Uma partição de intercalação.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Restaure uma base de dados do Analysis Services.| 
  

## <a name="related-information"></a>Informações relacionadas

* [Transferir o módulo de PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixe o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo SqlServer na galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabela modelo de programação para 1200 de nível de compatibilidade e superior](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
