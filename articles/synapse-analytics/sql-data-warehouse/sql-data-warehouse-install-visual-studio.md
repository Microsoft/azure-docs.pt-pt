---
title: Instalar Visual Studio 2019
description: Instale ferramentas de desenvolvimento de servidores visuais e SQL (SSDT) para Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 456d7eb0cabd7ebede6415e3ce92d7403ecef7ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368759"
---
# <a name="getting-started-with-visual-studio-2019"></a>Introdução ao Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) é uma única ferramenta que lhe permite fazer o seguinte:

- Conecte-se, questione e desenvolva aplicações
- Aproveite um explorador de objetos para explorar visualmente todos os objetos do seu modelo de dados, incluindo tabelas, vistas, procedimentos armazenados e etc.
- Gerei scripts de definição de dados T-SQL (DDL) para os seus objetos
- Desenvolva o seu armazém de dados utilizando uma abordagem baseada no Estado com projetos de base de dados SSDT
- Integre o seu projeto de base de dados com sistemas de controlo de fontes como o Git com o Azure Repos
- Configurar oleodutos de integração e implantação contínuas com servidores de automação como o Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalar Visual Studio 2019

Consulte [o Download Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para descarregar e instalar o Visual Studio **16.3 e acima**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação autónoma do SSDT já não é necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funcionalidades não suportadas no SSDT

Há alturas em que as versões de funcionalidades para Synapse SQL podem não incluir suporte para SSDT. As seguintes funcionalidades não são atualmente apoiadas:


- [Gestão da carga de trabalho](sql-data-warehouse-workload-management.md) - grupos de carga de trabalho e classificadores
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (incluindo funções avaliadas em tabelas)
  - Envie um [bilhete de apoio ou voto](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para obter o suporte da funcionalidade.
  - Envie um [bilhete de apoio ou voto](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para obter o suporte da funcionalidade.
- Tabelas com uma [coluna de identidade](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Algumas funcionalidades T-SQL, tais como:
   - *Cláusula DENTRO DO GRUPO* na função de corda [STRING_AGG.](/sql/t-sql/functions/string-agg-transact-sql)

## <a name="next-steps"></a>Passos seguintes

Agora que tem a versão mais recente do SSDT, está pronto para [ligar](sql-data-warehouse-query-visual-studio.md) à sua piscina SQL.
