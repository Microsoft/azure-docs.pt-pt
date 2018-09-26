---
title: Gerir bases de dados SQL do Azure com a automatização do Azure | Documentos da Microsoft
description: Saiba mais sobre como o serviço de automatização do Azure pode ser utilizado para gerir bases de dados SQL do Azure em escala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 3360482c77dd262a81bebb885c95f72616c7764a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159538"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gerir bases de dados SQL do Azure com a automatização do Azure
Este guia apresenta-lhe para o serviço de automatização do Azure e como ele pode ser usado para simplificar a gestão das bases de dados SQL do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?
[A automatização do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar a gestão da cloud através de automatização de processos. Utilizar a automatização do Azure, as tarefas de longa execução, manuais, propenso a erros e repetidas com frequência podem ser automatizadas para aumentar a confiabilidade, a eficiência e a hora de valor para a sua organização.

A automatização do Azure fornece um motor de execução do fluxo de trabalho altamente fiável e de elevada disponibilidade que se dimensiona para atender às suas necessidades, à medida que aumenta a sua organização. Na automatização do Azure, processos podem ser iniciados manualmente, por sistemas de terceiros 3rd ou em intervalos agendados, de modo a que tarefas acontecem exatamente quando necessário.

Reduzir o overhead operacional e liberar IT / equipa do DevOps para se dedicar ao trabalho que adiciona business value movendo as tarefas de gestão na cloud a ser executada automaticamente pela automatização do Azure.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Como pode automatização do Azure ajudar a gerir bases de dados SQL do Azure?
Base de dados SQL do Azure podem ser gerido na automatização do Azure com o [cmdlets do PowerShell de base de dados SQL do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0#sql) que estão disponíveis no [ferramentas do Azure PowerShell](/powershell/azure/overview). A automatização do Azure tem estes cmdlets do PowerShell de base de dados SQL do Azure disponíveis de imediato, para que possa executar todas as suas tarefas de gestão de BD SQL no serviço. Também pode emparelhar estes cmdlets na automatização do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas em serviços do Azure e 3º sistemas de terceiros.

A automatização do Azure também tem a capacidade de comunicar com os servidores SQL diretamente, através da emissão de comandos SQL com o PowerShell.

O [Galeria de runbooks da automatização do Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de equipe e da Comunidade de produto para começar a automatizar a gestão de bases de dados SQL do Azure, outros serviços do Azure e o 3º sistemas de terceiros. Galeria runbooks incluem:

* [Executar consultas SQL numa base de dados do SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
* [Aumentar verticalmente (para cima ou para baixo) uma base de dados do SQL do Azure com base numa agenda](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
* [Truncar uma tabela SQL se o tamanho máximo se aproxima de seu banco de dados](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
* [Indexar as tabelas numa base de dados SQL do Azure se eles são muito fragmentados](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas da automatização do Azure e como ele pode ser usado para gerenciar bancos de dados SQL do Azure, siga estas ligações para saber mais sobre a automatização do Azure.

* [Descrição geral da automatização do Azure](../automation/automation-intro.md)
* [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem de automatização do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)
* [A automatização do Azure: O agente do SQL na Cloud](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 

