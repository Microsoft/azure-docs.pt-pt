---
title: Gerir serviços de análise azure com powerShell / Microsoft Docs
description: Descreve os serviços de análise azure PowerShell cmdlets para tarefas administrativas comuns, tais como a criação de servidores, a suspensão de operações ou a alteração do nível de serviço.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572700"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gerir o Azure Analysis Services com o PowerShell

Este artigo descreve os cmdlets PowerShell usados para executar o servidor de serviços de análise Azure e tarefas de gestão de bases de dados. 

As tarefas de gestão de recursos do servidor, como criar ou eliminar um servidor, suspender ou retomar as operações do servidor, ou alterar o nível de serviço (nível) utilizam os cmdlets dos Serviços de Análise do Azure. Outras tarefas para gerir bases de dados como adicionar ou remover membros de papéis, processar ou dividir cmdlets incluídos no mesmo módulo SqlServer que os Serviços de Análise do Servidor SQL.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Permissões

A maioria das tarefas powerShell requer que tenha privilégios admin no servidor de Serviços de Análise que está a gerir. As tarefas programadas da PowerShell são operações sem supervisão. A conta ou diretor de serviço que executa o programador deve ter privilégios de Administração no servidor de Serviços de Análise. 

Para operações de servidor utilizando cmdlets Azure PowerShell, a sua conta ou o programador de funcionamento da conta também deve pertencer à função proprietário do recurso no Controlo de Acesso baseado em [Papel Azure (RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Operações de recursos e servidores 

Instalar módulo - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentação - [Az.AnalysisServices referência](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operações de base de dados

As operações de base de dados dos Serviços de Análise Azure utilizam o mesmo módulo SqlServer que os Serviços de Análise do Servidor SQL. No entanto, nem todos os cmdlets são suportados para os Serviços de Análise Azure. 

O módulo SqlServer fornece cmdlets de gestão de base de dados específicos de tarefas, bem como o cmdlet invoca-ASCmd de propósito geral que aceita uma consulta ou script de linguagem de script modelo tabular (TMSL). Os seguintes cmdlets no módulo SqlServer são suportados para serviços de análise Azure.

Instalar módulo - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentação - [Referência SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlets suportados

|Cmdlet|Descrição|
|------------|-----------------| 
|[Membro adum](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Adicione um membro a uma função de base de dados.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Backup de uma base de dados de Serviços de Análise.|  
|[Remover-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Remova um membro de uma função de base de dados.|   
|[Invocar-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Execute um script TMSL.|
|[Invocar-ProcessASBase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|Processar uma base de dados.|  
|[Invocar-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|Processe uma partição.| 
|[Invocar-Processtable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|Processe uma mesa.|  
|[Divisão de fusão](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|Fundir uma divisória.|  
|[Restaurar-ASBase de Dados](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Restaurar uma base de dados de Serviços de Análise.| 
  

## <a name="related-information"></a>Informações relacionadas

* [PowerShell do SQL Server](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [Descarregue o módulo PowerShell do servidor SQL](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Baixar SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [Módulo SqlServer na Galeria PowerShell](https://www.powershellgallery.com/packages/SqlServer)    
* [Programação de modelos tabular para o nível 1200 de compatibilidade](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)
