---
title: Gerir o Azure Analysis Services com o PowerShell | Documentos da Microsoft
description: Gestão de Analysis Services do Azure com o PowerShell.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a958c33e173c881a3ad09a49fe9f71ddb0c9df56
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508939"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerir o Azure Analysis Services com o PowerShell

Este artigo descreve os cmdlets do PowerShell utilizados para realizar tarefas de gerenciamento de banco de dados e de servidor Azure Analysis Services. 

Tarefas de gestão de recursos de servidor, como criar ou eliminar um servidor, suspender ou retomar as operações de servidor ou alterar o nível de serviço (escalão) utilizam cmdlets do Azure Analysis Services. Outras tarefas para gerir bases de dados, como adicionar ou remover membros de funções, processamento ou criação de partições utilizam os cmdlets incluídos no mesmo módulo SqlServer como SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas do PowerShell necessitam de que ter privilégios de administrador no servidor do Analysis Services que está a gerir. As tarefas agendadas do PowerShell são operações autónomas. A conta ou a executar o agendador principal de serviço, tem de ter privilégios de administrador no servidor do Analysis Services. 

Para operações de servidor utilizando cmdlets do PowerShell do Azure, sua conta ou a conta que executa o scheduler também tem de pertencer à função de proprietário para o recurso no [controlo de acesso de controlo (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operações de servidor e de recursos 

Instalar o módulo - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentação - [Az.AnalysisServices referência](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operações de base de dados

Operações de banco de dados do Azure Analysis Services utilizam o módulo SqlServer como SQL Server Analysis Services. No entanto, nem todos os cmdlets são suportados para o Azure Analysis Services. 

O módulo SqlServer fornece cmdlets de gestão de base de dados específicos a tarefas, bem como o cmdlet Invoke-Scmd para fins gerais que aceita uma consulta de linguagem de scripts de modelo em tabela (TMSL) ou um script. Os seguintes cmdlets no módulo SqlServer são suportados para o Azure Analysis Services.

Instalar o módulo - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentação - [SqlServer referência](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets suportados

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

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Transferir o módulo de PowerShell do SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixe o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo SqlServer na galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabela modelo de programação para 1200 de nível de compatibilidade e superior](/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
