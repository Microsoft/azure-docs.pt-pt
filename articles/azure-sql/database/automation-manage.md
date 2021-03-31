---
title: Gerir bases de dados com a Azure Automation
description: Saiba como o serviço de Automação Azure pode ser usado para gerir a Base de Dados Azure SQL em escala.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91327583"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Gerir bases de dados na Base de Dados Azure SQL utilizando a Azure Automation

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este guia irá apresentá-lo ao serviço Azure Automation, e como pode ser usado para simplificar a gestão de bases de dados na Base de Dados Azure SQL.

## <a name="about-azure-automation"></a>Sobre a Azure Automation

[A Azure Automation](https://azure.microsoft.com/services/automation/) é um serviço Azure para simplificar a gestão de nuvem através da automatização de processos. A utilização de Azure Automation, tarefas de longa duração, manual, propensas a erros e tarefas frequentemente repetidas pode ser automatizada para aumentar a fiabilidade, eficiência e tempo de valor para a sua organização. Para obter informações sobre o início, consulte [a introdução da Azure Automation](../../automation/automation-intro.md)

A Azure Automation fornece um motor de execução de fluxo de trabalho com alta fiabilidade e elevada disponibilidade, e que escala para atender às suas necessidades à medida que a sua organização cresce. Na Azure Automation, os processos podem ser iniciados manualmente, por sistemas de terceiros ou em intervalos programados para que as tarefas ocorram exatamente quando necessário.

Baixar a sobrecarga operacional e libertar pessoal de TI/DevOps para se concentrar no trabalho que adiciona valor de negócio, movendo as suas tarefas de gestão de nuvem para ser executado automaticamente pela Azure Automation.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Como a Azure Automation pode ajudar a gerir as suas bases de dados

Com a Azure Automation, pode gerir bases de dados na Base de Dados Azure SQL utilizando [cmdlets PowerShell](/powershell/module/servicemanagement/azure.service/#sql) que estão disponíveis nas [ferramentas Azure PowerShell](/powershell/azure/). A Azure Automation tem estes cmdlets Azure SQL Database PowerShell disponíveis fora da caixa, para que possa executar todas as suas tarefas de gestão da Base de Dados SQL dentro do serviço. Pode também emparelhar estes cmdlets na Azure Automation com os cmdlets para outros serviços Azure, para automatizar tarefas complexas em todos os serviços da Azure e em sistemas de terceiros.

A Azure Automation também tem a capacidade de comunicar diretamente com os servidores SQL, através da emissão de comandos SQL utilizando o PowerShell.

As galerias de runbook e módulos da [Azure Automation](../../automation/automation-runbook-gallery.md) oferecem uma variedade de runbooks da Microsoft e da comunidade que você pode importar para a Azure Automation. Para utilizar um, faça o download de um livro de contas da galeria, ou pode importar diretamente livros de execução da galeria ou da sua conta Automation no portal Azure.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu os fundamentos da Azure Automation e como pode ser usado para gerir a Azure SQL Database, siga estes links para saber mais sobre a Azure Automation.

- [Descrição Geral da Automatização do Azure](../../automation/automation-intro.md)
- [O meu primeiro runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
