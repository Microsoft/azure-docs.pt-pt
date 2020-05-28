---
title: Referência de conteúdo de início rápido de base de dados única
description: Encontre uma referência de conteúdo de todos os quickstarts que o ajudarão rapidamente a começar com bases de dados únicas na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: c4a699a6b531feee98f9c6f83ba19cfef9d27620
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054606"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Começar com bases de dados únicas na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Uma única base de dados](../index.yml) é totalmente gerida base de dados PaaS como um serviço (DbaaS) que é o motor de armazenamento ideal para as aplicações modernas nascidas na nuvem. Nesta secção, você aprenderá a configurar rapidamente e criar uma única base de dados na Base de Dados Azure SQL.

## <a name="quickstart-overview"></a>Descrição geral do Início Rápido

Nesta secção, você verá uma visão geral dos artigos disponíveis que podem ajudá-lo a começar rapidamente com bases de dados individuais. Os seguintes arranques rápidos permitem-lhe criar rapidamente uma única base de dados, configurar uma regra de firewall ao nível do servidor e, em seguida, importar uma base de dados para a nova base de dados única usando um `.bacpac` ficheiro:

- [Crie uma única base de dados utilizando o portal Azure](single-database-create-quickstart.md).
- Depois de criar a base de dados, terá de proteger a [sua base de dados configurando as regras de firewall](firewall-create-server-level-portal-quickstart.md).
- Se tiver uma base de dados existente no SQL Server que pretende migrar para a Base de Dados SQL do Azure, deverá instalar o [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) que irá analisar as suas bases de dados no SQL Server e encontrar qualquer problema que possa bloquear a migração. Se não encontrar qualquer problema, pode exportar a sua base de dados como `.bacpac` ficheiro e [importá-la através do portal Azure ou SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Automatização de operações de gestão

Pode utilizar o PowerShell ou o Azure CLI para criar, configurar e escalar a sua base de dados.

- [Criar e configurar uma única base de dados utilizando o PowerShell](scripts/create-and-configure-database-powershell.md) ou [o Azure CLI](scripts/create-and-configure-database-cli.md)
- [Atualize a sua única base de dados e dimensione recursos utilizando powerShell](scripts/monitor-and-scale-database-powershell.md) ou [Azure CLI](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrar para uma única base de dados com o mínimo de tempo de inatividade

Estes quickstarts permitem-lhe criar ou importar rapidamente a sua base de dados para o Azure utilizando um `.bacpac` ficheiro. No entanto, `.bacpac` e `.dacpac` os ficheiros são projetados para mover rapidamente bases de dados em diferentes versões do SQL Server e dentro do Azure SQL, ou para implementar a integração contínua no seu pipeline DevOps. No entanto, este método não foi concebido para a migração das suas bases de dados de produção com o mínimo de tempo de inatividade, pois teria de parar de adicionar novos dados, esperar que a exportação da base de dados de origem para um ficheiro seja concluída e, em seguida, aguardar que a importação para a Base de `.bacpac` Dados Azure SQL seja concluída. Toda esta espera resulta em tempo de inatividade da sua aplicação, especialmente em grandes bases de dados. Para mover a sua base de dados de produção, precisa de uma melhor forma de migrar que garanta o mínimo tempo de inatividade da migração. Para isso, utilize o Serviço de Migração de [Dados (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) para migrar a sua base de dados com o tempo mínimo de inatividade.. O DMS consegue isso empurrando incrementalmente as alterações feitas na sua base de dados de origem para a base de dados única que está a ser restaurada. Desta forma, pode mudar rapidamente a sua aplicação de origem para base de dados de destino com o tempo mínimo de inatividade.

## <a name="hands-on-learning-modules"></a>Módulos de aprendizagem práticos

Os seguintes módulos Microsoft Learn ajudam-no a aprender gratuitamente sobre a Base de Dados Azure SQL.

- [Fornecer uma base de dados na Base de Dados SQL para armazenar dados de aplicações](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Desenvolver e configurar uma aplicação ASP.NET que questiona uma Base de Dados Azure SQL](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Proteger a sua Base de Dados SQL do Azure](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Próximos passos

- Encontre uma [lista de funcionalidades suportadas de alto nível na Base de Dados Azure SQL](features-comparison.md).
- Saiba como tornar a sua [base de dados mais segura](secure-database-tutorial.md).
- Encontre como utilizar uma única base de [dados em Azure SQL Database](how-to-content-reference-guide.md).
- Encontre mais scripts de amostra escritos no [PowerShell](powershell-script-content-guide.md) e [no Azure CLI](az-cli-script-samples-content-guide.md).
- Saiba mais sobre a [Gestão DaPI](single-database-manage.md) que pode utilizar para configurar as suas bases de dados.
- Identifique a base de [dados Azure SQL ou sKu de instância gerida sql para a sua base de dados no local](/sql/dma/dma-sku-recommend-sql-db/).
