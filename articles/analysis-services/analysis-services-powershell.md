---
title: Gerir serviços de análise Azure com | PowerShell Microsoft Docs
description: Descreve os serviços de análise Azure PowerShell cmdlets para tarefas administrativas comuns, tais como a criação de servidores, a suspensão de operações ou a alteração do nível de serviço.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018769"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerir o Azure Analysis Services com o PowerShell

Este artigo descreve os cmdlets powerShell utilizados para executar o servidor dos Serviços de Análise Azure e as tarefas de gestão de bases de dados. 

Tarefas de gestão de recursos do servidor como criar ou eliminar um servidor, suspender ou retomar as operações do servidor ou alterar o nível de serviço (tier) usam cmdlets de Serviços de Análise Azure. Outras tarefas para gerir bases de dados como adicionar ou remover membros de funções, processamento ou partilhas usam cmdlets incluídos no mesmo módulo SqlServer que os SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas powerShell requerem que tenha privilégios de Administração no servidor de Serviços de Análise que está a gerir. As tarefas programadas da PowerShell são operações não acompanhadas. A conta ou o principal do serviço que executam o programador devem ter privilégios de administração no servidor de Serviços de Análise. 

Para operações de servidor utilizando cmdlets Azure PowerShell, a sua conta ou o programador de execução da conta também devem pertencer à função Proprietário para o recurso no controlo de acesso baseado em [funções Azure (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operações de recursos e servidores 

Módulo de instalação - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentação - [Referência Az.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operações da base de dados

As operações de base de dados dos Serviços de Análise Azure utilizam o mesmo módulo SqlServer que os SqL Server Analysis Services. No entanto, nem todos os cmdlets são suportados para os Serviços de Análise Azure. 

O módulo SqlServer fornece cmdlets de gestão de bases de dados específicos de tarefas, bem como o cmdlet de Invoke-ASCmd de propósito geral que aceita uma consulta ou script de script de modelo tabular (TMSL). Os cmdlets seguintes no módulo SqlServer são suportados para serviços de análise Azure.

Módulo de instalação - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentação - [Referência SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets apoiados

|Cmdlet|Descrição|
|------------|-----------------| 
|[Membro do Add-Role](/powershell/module/sqlserver/Add-RoleMember)|Adicione um membro a uma função de base de dados.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Faça cópia de segurança de uma base de dados dos Serviços de Análise.|  
|[Membro de remoção de rolemember](/powershell/module/sqlserver/remove-rolemember)|Remova um membro de uma função de base de dados.|   
|[Invocar-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Execute um script TMSL.|
|[Invocar-ProcessoASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Processe uma base de dados.|  
|[Invocar processopartição](/powershell/module/sqlserver/invoke-processpartition)|Processe uma divisória.| 
|[Invocar-ProcessTable](/powershell/module/sqlserver/invoke-processtable)|Processe uma mesa.|  
|[Fusão-Partição](/powershell/module/sqlserver/merge-partition)|Funda uma divisória.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Restaurar uma base de dados dos Serviços de Análise.| 
  

## <a name="related-information"></a>Informações relacionadas

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [Baixar módulo PowerShell do servidor SQL](/sql/ssms/download-sql-server-ps-module)   
* [Baixar SSMS](/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo SqlServer na Galeria PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programação do Modelo Tabular para o Nível de Compatibilidade 1200 e superior](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)