---
title: Gerenciar Azure Analysis Services com o PowerShell | Microsoft Docs
description: Gerenciamento de Azure Analysis Services com o PowerShell.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 329ea51895c45c8cdf50a323328fac3c580ffc16
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146217"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerenciar Azure Analysis Services com o PowerShell

Este artigo descreve os cmdlets do PowerShell usados para executar Azure Analysis Services tarefas de gerenciamento de servidor e banco de dados. 

Tarefas de gerenciamento de recursos de servidor como criar ou excluir um servidor, suspender ou retomar operações de servidor ou alterar o nível de serviço (camada) usam cmdlets Azure Analysis Services. Outras tarefas para gerenciar bancos de dados, como adicionar ou remover membros de função, processamento ou particionamento, usam cmdlets incluídos no mesmo módulo do SqlServer que SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas do PowerShell exige que você tenha privilégios de administrador no servidor Analysis Services que está gerenciando. As tarefas agendadas do PowerShell são operações autônomas. A conta ou entidade de serviço que executa o Agendador deve ter privilégios de administrador no servidor de Analysis Services. 

Para operações de servidor usando cmdlets Azure PowerShell, sua conta ou o Agendador que executa a conta também deve pertencer à função de proprietário do recurso no [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operações de servidor e de recurso 

Instalar módulo- [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentação – [referência AZ. AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operações de banco de dados

Azure Analysis Services operações de banco de dados usam o mesmo módulo do SqlServer que SQL Server Analysis Services. No entanto, nem todos os cmdlets têm suporte para Azure Analysis Services. 

O módulo SqlServer fornece cmdlets de gerenciamento de banco de dados específicos de tarefas, bem como o cmdlet Invoke-ASCmd de uso geral que aceita uma consulta ou script de TMSL (linguagem de script de modelo de tabela). Os cmdlets a seguir no módulo SqlServer têm suporte para Azure Analysis Services.

Instalar módulo- [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentação- [referência do SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets com suporte

|Cmdlet|Descrição|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicione um membro a uma função de banco de dados.| 
|[Backup-asdatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Fazer backup de um banco de dados Analysis Services.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remover um membro de uma função de banco de dados.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Execute um script TMSL.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Processar um banco de dados.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Processar uma partição.| 
|[Invoke-Processtable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Processar uma tabela.|  
|[Partição de mesclagem](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Mescle uma partição.|  
|[Restore-asdatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Restaurar um banco de dados Analysis Services.| 
  

## <a name="related-information"></a>Informações relacionadas

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Baixar SQL Server PowerShell módulo](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar o SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo do SqlServer no Galeria do PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programação de modelo de tabela para nível de compatibilidade 1200 e superior](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
