---
title: Início rápido-bancos de dados únicos no Azure SQL Database | Microsoft Docs
description: Saiba como começar rapidamente com bancos de dados individuais no banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 02/04/2019
ms.openlocfilehash: b4341cb7d1734cb25aae890cc282c67ef38d67bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566487"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introdução a bancos de dados individuais no banco de dados SQL do Azure

[Um banco de dados individual](sql-database-single-index.yml) é um DbaaS (banco de dados como serviço) de PaaS totalmente gerenciado, que é o mecanismo de armazenamento ideal para os aplicativos modernos gerados pela nuvem. Nesta seção, você aprenderá a configurar e criar rapidamente um banco de dados individual no banco de dados SQL.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Nesta seção, você verá uma visão geral dos artigos disponíveis que podem ajudá-lo a começar rapidamente com bancos de dados individuais. Os guias de início rápido a seguir permitem criar rapidamente um banco de dados individual, configurar uma regra de firewall de servidor de banco de dados e, em seguida, `.bacpac` importar um banco de dados para o novo banco de dados individual usando um arquivo:

- [Crie um banco de dados individual usando o portal do Azure](sql-database-single-database-get-started.md).
- Depois de criar o banco de dados, você precisaria [proteger seu banco de dados Configurando regras de firewall](sql-database-server-level-firewall-rule.md).
- Se você tiver um banco de dados existente no SQL Server que deseja migrar para o Azure, deverá instalar o [Assistente de migração de dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) que analisará seus bancos de dados no SQL Server e encontrará qualquer problema que possa bloquear a migração para a implantação de banco de dados individual Option. Se você não encontrar nenhum problema, poderá exportar seu banco de dados `.bacpac` como arquivo e importá [-lo usando o portal do Azure ou SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatizando operações de gerenciamento

Você pode usar o PowerShell ou o CLI do Azure para criar, configurar e dimensionar seu banco de dados.

- [Criar e configurar um banco de dados individual usando o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
- [Criar e configurar um banco de dados individual usando CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md)
- [Atualizar seu banco de dados individual e dimensionar os recursos usando o PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Atualize seu banco de dados individual e dimensione os recursos usando CLI do Azure](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrando para um banco de dados individual com tempo de inatividade mínimo

Esses guias de início rápido permitem criar ou importar rapidamente seu banco de dados para o `.bacpac` Azure usando um arquivo. No entanto `.dacpac` , `.bacpac` os arquivos são projetados para mover rapidamente os bancos de dados em diferentes versões do SQL Server e opções de implantação no banco de dados SQL do Azure ou para implementar a integração contínua em seu pipeline do DevOps. No entanto, esse método não é projetado para a migração de seus bancos de dados de produção com tempo de inatividade mínimo, porque você precisará parar de adicionar novos dados, esperar que a `.bacpac` exportação do banco de dados de origem para um arquivo seja concluída e aguardar a importação para Banco de dados SQL do Azure a ser concluído. Toda essa espera resulta em tempo de inatividade do seu aplicativo, especialmente para bancos de dados grandes. Para mover seu banco de dados de produção, você precisa de uma maneira melhor de migrar que garanta tempo de inatividade mínimo de migração. Para isso, use o [serviço de migração de dados (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) para migrar seu banco de dado com o mínimo de tempo de inatividade. O DMS realiza isso incrementalmente as alterações feitas no banco de dados de origem para o banco de dados individual que está sendo restaurado. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para destino com o tempo de inatividade mínimo.

## <a name="next-steps"></a>Passos Seguintes

- Encontre uma [lista de alto nível dos recursos com suporte no banco de dados SQL do Azure](sql-database-features.md).
- Saiba como tornar seu [banco de dados mais seguro](sql-database-security-tutorial.md).
- Encontre instruções mais avançadas [sobre como usar um único banco de dados no banco de dados SQL do Azure](sql-database-howto-single-database.md).
- Encontre mais scripts de exemplo escritos no [PowerShell](sql-database-powershell-samples.md) e [CLI do Azure](sql-database-cli-samples.md).
- Saiba mais sobre a [API de gerenciamento](sql-database-single-databases-manage.md) que você pode usar para configurar seus bancos de dados.
- [Identifique o banco de dados SQL do Azure/instância gerenciada SKU correto para seu banco de dados local](/sql/dma/dma-sku-recommend-sql-db/).