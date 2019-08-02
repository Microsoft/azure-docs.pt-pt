---
title: Gerenciar bancos de dados SQL do Azure usando a automação do Azure | Microsoft Docs
description: Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar bancos de dados SQL do Azure em escala.
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
ms.openlocfilehash: bb195d5809ef0e2af2a4975a263a9739db1cdfa6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567742"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gerenciando bancos de dados SQL do Azure usando a automação do Azure

Este guia apresentará o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus bancos de dados SQL do Azure.

## <a name="what-is-azure-automation"></a>O que é a Automatização do Azure?

A [automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Usando a automação do Azure, tarefas de longa execução, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A automação do Azure fornece um mecanismo de execução de fluxo de trabalho com alta confiabilidade e alta disponibilidade, e isso pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na automação do Azure, os processos podem ser iniciados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas ocorram exatamente quando necessário.

Diminua a sobrecarga operacional e libere a equipe de ti/DevOps para se concentrar no trabalho que agrega valor comercial, movendo suas tarefas de gerenciamento de nuvem para serem executadas automaticamente pela automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Como a automação do Azure pode ajudar a gerenciar bancos de dados SQL do Azure?

O banco de dados SQL do Azure pode ser gerenciado na automação do Azure usando os cmdlets do [PowerShell do banco de dados SQL do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) que estão disponíveis nas [ferramentas de Azure PowerShell](/powershell/azure/overview). A automação do Azure tem esses cmdlets do PowerShell do banco de dados SQL do Azure disponíveis prontos para uso, para que você possa executar todas as suas tarefas de gerenciamento de BD SQL dentro do serviço. Você também pode emparelhar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e em sistemas de terceiros.

A automação do Azure também tem a capacidade de se comunicar com os SQL Servers diretamente, emitindo comandos SQL usando o PowerShell.

A [Galeria de runbook da automação do Azure](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks de comunidade e equipe de produto para começar a automatizar o gerenciamento de bancos de dados SQL do Azure, outros serviços do Azure e sistemas de terceiros. Os runbooks da Galeria incluem:

- [Executar consultas SQL em um banco de dados SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Dimensionar verticalmente (para cima ou para baixo) um banco de dados SQL do Azure em um agendamento](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Truncar uma tabela SQL se seu banco de dados se aproximar de seu tamanho máximo](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexar tabelas em um banco de dados SQL do Azure se elas estiverem altamente fragmentadas](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu os conceitos básicos da automação do Azure e como ele pode ser usado para gerenciar bancos de dados SQL do Azure, siga estes links para saber mais sobre a automação do Azure.

- [Visão geral da automação do Azure](../automation/automation-intro.md)
- [O meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
- [Automação do Azure: Seu agente SQL na nuvem](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 