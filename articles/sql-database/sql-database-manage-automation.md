---
title: Gerir bases de dados com a Automação Azure
description: Saiba como o serviço De automação Azure pode ser usado para gerir bases de dados Azure SQL em escala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73822812"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gerir bases de dados Azure SQL utilizando a Automação Azure

Este guia irá apresentá-lo ao serviço de Automação Azure e como pode ser usado para simplificar a gestão das suas bases de dados Azure SQL.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?

[A Azure Automation](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão da nuvem através da automatização de processos. A utilização de tarefas de longa duração, manual, propensaa a erros e frequentemente repetidas pode ser automatizada para aumentar a fiabilidade, eficiência e tempo para valorizar a sua organização.

A Azure Automation fornece um motor de execução de fluxo de trabalho com elevada fiabilidade e elevada disponibilidade, e que escalas para atender às suas necessidades à medida que a sua organização cresce. Na Automatização Azure, os processos podem ser iniciados manualmente, por sistemas de terceiros, ou em intervalos programados para que as tarefas aconteçam exatamente quando necessário.

Baixar as despesas operacionais e libertar os colaboradores de TI/DevOps para se concentrarem no trabalho que acrescenta valor ao negócio, movendo as suas tarefas de gestão de nuvem para serem executados automaticamente pela Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Como pode a Azure Automation ajudar a gerir as bases de dados Azure SQL?

A Base de Dados Azure SQL pode ser gerida na Automatização Azure utilizando os [cmdlets powerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) de base de dados Azure SQL que estão disponíveis nas [ferramentas Azure PowerShell](/powershell/azure/overview). A Azure Automation dispõe destes cmdlets de Base de Dados Azure SQL PowerShell disponíveis fora da caixa, para que possa executar todas as suas tarefas de gestão SQL DB dentro do serviço. Também pode emparelhar estes cmdlets na Automatização Azure com os cmdlets para outros serviços Azure, para automatizar tarefas complexas em serviços Azure e em sistemas de terceiros.

A Azure Automation também tem a capacidade de comunicar diretamente com os servidores SQL, através da emissão de comandos SQL utilizando o PowerShell.

A [galeria de runbook Azure Automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) contém uma variedade de equipe de produtos e livros comunitários para começar a automatizar a gestão de bases de dados Azure SQL, outros serviços Azure e sistemas de terceiros. Os livros de corridas de galerias incluem:

- [Executar consultas SQL contra uma base de dados do Servidor SQL](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Escala vertical (para cima ou para baixo) uma base de dados Azure SQL em um horário](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Trunca uma tabela SQL se a sua base de dados se aproximar do seu tamanho máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Tabelas de índices numa base de dados Azure SQL se estiverem altamente fragmentadas](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu o básico da Automação Azure e como pode ser usado para gerir bases de dados Azure SQL, siga estes links para saber mais sobre a Automação Azure.

- [Descrição Geral da Automatização do Azure](../automation/automation-intro.md)
- [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
- [Automação Azure: O seu agente SQL na nuvem](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 