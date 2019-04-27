---
title: Início rápido - bases de dados individuais na base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como começar a utilizar rapidamente com bases de dados individuais na base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 0dc9d18ea4dae6ea0f45d1a91392eb1994e6e80e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60331694"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Guia de introdução bases de dados individuais na base de dados do Azure SQL

[Uma base de dados](sql-database-single-index.yml) totalmente gerido base de dados de PaaS como um serviço (DbaaS), que é o mecanismo de armazenamento ideal para as aplicações modernas na cloud. Nesta seção, aprenderá como configurar rapidamente e criar uma base de dados na base de dados SQL.

## <a name="quickstart-overview"></a>Descrição geral do guia de introdução

Nesta secção, irá ver uma descrição geral dos artigos disponíveis que podem ajudar a começar rapidamente bases de dados individuais. Os inícios Rápidos seguintes permitem-lhe criar rapidamente uma base de dados, configurar uma regra de firewall do servidor de base de dados e, em seguida, importar uma base de dados para a nova base de dados individual utilizando um `.bacpac` ficheiro:

- [Criar uma base de dados com o portal do Azure](sql-database-single-database-get-started.md).
- Depois de criar a base de dados, precisaria [proteger a sua base de dados através da configuração de regras de firewall](sql-database-server-level-firewall-rule.md).
- Se tiver uma base de dados no SQL Server que pretende migrar para o Azure, deve instalar [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) que irá analisar seus bancos de dados no SQL Server e encontrar qualquer problema que poderia bloquear a migração para o único opção de implementação de base de dados. Se não encontrar algum problema, pode exportar a base de dados como `.bacpac` ficheiros e [importá-lo a utilizar o portal do Azure ou o SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatização de operações de gestão

Pode utilizar o PowerShell ou a CLI do Azure para criar, configurar e dimensionar a sua base de dados.

- [Criar e configurar uma base de dados com o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Criar e configurar uma base de dados com a CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)
- [Atualizar os seus recursos de base de dados e dimensionamento únicos com o PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Atualizar os seus recursos do banco de dados e dimensionamento únicos, com a CLI do Azure](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrar para uma base de dados com o tempo de inatividade mínimo

Estes inícios rápidos permitem-lhe rapidamente criar ou importar a base de dados para o Azure com um `.bacpac` ficheiro. No entanto, `.bacpac` e `.dacpac` ficheiros foram concebidos para mover rapidamente bases de dados nas diferentes versões do SQL Server e opções de implementação na base de dados do Azure SQL ou para implementar a integração contínua no seu pipeline de DevOps. No entanto, esse método não foi concebido para a migração de seus bancos de dados de produção com o tempo de inatividade mínimo, uma vez que precisaria parar a adição de novos dados, aguarde que a exportação da base de dados de origem para um `.bacpac` de ficheiros para concluir e, em seguida, aguarde que a importação em SQL Database do Azure para concluir. Tudo desta espera resulta em tempo de inatividade do seu aplicativo, especialmente para grandes bancos de dados. Para mover a base de dados de produção, precisa de uma forma melhor para migrar que garante o período de indisponibilidade mínimo de migração. Para tal, utilize o [serviço de migração de dados (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) para migrar a sua base de dados com o período de indisponibilidade mínimo.... O DMS faz isso ao incrementalmente enviar as alterações efetuadas na base de dados de origem para a base de dados individual a ser restaurada. Dessa forma, pode alternar rapidamente seu aplicativo de origem para a base de dados de destino com o período de indisponibilidade mínimo.

## <a name="next-steps"></a>Passos Seguintes

- Encontrar um [lista de alto nível das funcionalidades suportadas na base de dados do Azure SQL](sql-database-features.md).
- Saiba como tornar sua [base de dados mais seguro](sql-database-security-tutorial.md).
- Encontre mais avançadas como-para [como utilizar uma base de dados na base de dados do Azure SQL](sql-database-howto-single-database.md).
- Encontre mais de exemplos de scripts escritos em [PowerShell](sql-database-powershell-samples.md) e [CLI do Azure](sql-database-cli-samples.md).
- Saiba mais sobre o [gestão de API](sql-database-single-databases-manage.md) que pode utilizar para configurar seus bancos de dados.
