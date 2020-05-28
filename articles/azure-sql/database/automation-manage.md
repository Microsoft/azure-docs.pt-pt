---
title: Gerir bases de dados com a Automação Azure
description: Saiba como o serviço De automação Azure pode ser usado para gerir a Base de Dados Azure SQL em escala.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4b5378b5df36c158c3f401f214730b4f493f5def
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84043612"
---
# <a name="managing-azure-sql-database-using-azure-automation"></a>Gerir a Base de Dados Azure SQL utilizando a Automação Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb.md)]

Este guia irá apresentá-lo ao serviço de Automação Azure e como pode ser utilizado para simplificar a gestão de bases de dados na base de dados Azure SQL.

## <a name="what-is-azure-automation"></a>O que é a Automação Azure

[A Azure Automation](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão da nuvem através da automatização de processos. A utilização de tarefas de longa duração, manual, propensaa a erros e frequentemente repetidas pode ser automatizada para aumentar a fiabilidade, eficiência e tempo para valorizar a sua organização. Para obter informações sobre o início, consulte [a introdução da Automação Azure](../../automation/automation-intro.md)

A Azure Automation fornece um motor de execução de fluxo de trabalho com elevada fiabilidade e elevada disponibilidade, e que escalas para atender às suas necessidades à medida que a sua organização cresce. Na Automatização Azure, os processos podem ser iniciados manualmente, por sistemas de terceiros, ou em intervalos programados para que as tarefas aconteçam exatamente quando necessário.

Baixar as despesas operacionais e libertar os colaboradores de TI/DevOps para se concentrarem no trabalho que acrescenta valor ao negócio, movendo as suas tarefas de gestão de nuvem para serem executados automaticamente pela Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-sql-database"></a>Como é que a Azure Automation pode ajudar a gerir a Base de Dados Azure SQL

A Base de Dados Azure SQL pode ser gerida na Automatização Azure utilizando [cmdlets PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) que estão disponíveis nas [ferramentas Azure PowerShell](/powershell/azure/overview). A Azure Automation dispõe destes cmdlets de Base de Dados Azure SQL PowerShell disponíveis fora da caixa, para que possa executar todas as suas tarefas de gestão da Base de Dados SQL dentro do serviço. Também pode emparelhar estes cmdlets na Automatização Azure com os cmdlets para outros serviços Azure, para automatizar tarefas complexas em serviços Azure e em sistemas de terceiros.

A Azure Automation também tem a capacidade de comunicar diretamente com os servidores SQL, através da emissão de comandos SQL utilizando o PowerShell.

O livro de corridas e galerias de módulos para a [Azure Automation](../../automation/automation-runbook-gallery.md) uma variedade de livros de execução da Microsoft e da comunidade que você pode importar para a Automação Azure. Para utilizar um, faça o download de um livro de execução da galeria, ou pode importar diretamente livros de execução a partir da galeria, ou da sua conta De automação no portal Azure.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu o básico da Automação Azure e como pode ser usado para gerir a Base de Dados Azure SQL, siga estes links para saber mais sobre a Automação Azure.

- [Descrição Geral da Automatização do Azure](../../automation/automation-intro.md)
- [O meu primeiro runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
