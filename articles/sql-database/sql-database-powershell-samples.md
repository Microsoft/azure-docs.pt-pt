---
title: Exemplos de scripts do Azure PowerShell para a Base de Dados SQL | Microsoft Docs
description: Exemplos de scripts do Azure PowerShell para ajudar a criar e gerir servidores de Base de Dados SQL do Azure, conjuntos elásticos, bases de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/29/2018
ms.openlocfilehash: c53980dc2d29fbfb7e59dcba7252e0b0448d14d3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840015"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Exemplos do Azure PowerShell para a Base de Dados SQL do Azure

A tabela seguinte inclui ligações para scripts de exemplo do Azure PowerShell para a Base de Dados SQL do Azure.

| |  |
|---|---|
|**Criar e configurar a instância gerida, base de dados e um conjunto elástico**||
| [Criar uma base de dados e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria uma base de dados SQL do Azure e configura uma regra de firewall ao nível do servidor. |
| [Criar conjuntos elásticos e mover bases de dados agrupadas](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell cria conjuntos elásticos da Base de Dados SQL do Azure, move bases de dados agrupadas e altera os tamanhos de computação.|
| [Criar e gerir uma Instância Gerida](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Este script do PowerShell mostra como criar e gerir uma Instância Gerida com o Azure PowerShell |
| [Criar e gerir uma instância gerida com o modelo Azure Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell mostra-lhe como criar e gerir uma instância gerida com o modelo do Azure PowerShell e do Azure Resource Manager.|
|**Configurar georreplicação e ativação pós-falha**||
| [Configurar e efetuar a ativação pós-falha de uma base de dados através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure e realiza a ativação pós-falha para uma réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de uma base de dados agrupada através de georreplicação ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura a georreplicação ativa para uma base de dados SQL do Azure num conjunto elástico SQL e realiza a ativação pós-falha para uma réplica secundária. |
| [Configurar e efetuar a ativação pós-falha de um grupo de ativação pós-falha para uma base de dados](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura um grupo de ativação pós-falha para uma instância de servidor da Base de Dados SQL do Azure, adiciona uma base de dados ao grupo de ativação pós-falha e efetua a ativação pós-falha para o servidor secundário |
|**Dimensionar uma base de dados e um conjunto elástico**||
| [Dimensionar uma base de dados](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitoriza as métricas de desempenho de uma base de dados SQL do Azure, dimensiona-a para um tamanho de computação superior e cria uma regra de alerta numa das métricas de desempenho. |
| [Dimensionar um conjunto elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell monitoriza as métricas de desempenho de um conjunto elástico da Base de Dados SQL do Azure, dimensiona-o para um tamanho de computação superior e cria uma regra de alerta numa das métricas de desempenho.  |
| **Auditoria e deteção de ameaças** |
| [Configurar a auditoria e a deteção de ameaças](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell configura as políticas de auditoria e deteção de ameaças para uma base de dados SQL do Azure. |
| **Restaurar, copiar e importar uma base de dados**||
| [Restaurar uma base de dados](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell restaura uma base de dados SQL do Azure a partir de uma cópia de segurança georredundante e restaura uma base de dados SQL do Azure eliminada para a cópia de segurança mais recente. |
| [Copiar uma base de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell cria uma cópia de uma base de dados SQL do Azure existente num novo servidor SQL do Azure. |
| [Importar uma base de dados de um ficheiro bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script do PowerShell importa uma base de dados para um servidor SQL do Azure a partir de um ficheiro bacpac. |
| **Sincronizar dados entre bases de dados**||
| [Sincronizar dados entre bases de dados SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados entre várias bases de dados SQL do Azure. |
| [Sincronizar dados entre a Base de Dados SQL e o SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell configura a Sincronização de Dados entre uma base de dados SQL do Azure e uma base de dados do SQL Server no local. |
| [Atualizar o esquema de Sincronização de Dados SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script do PowerShell adiciona ou remove itens do esquema de Sincronização de Dados. |
|||
