---
title: Instalar o Estúdio Visual 2019
description: Instale ferramentas de desenvolvimento de estúdio visual e servidor SQL (SSDT) para Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f83ae9c8290a52381c8087b46da959d4723d7f4e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745162"
---
# <a name="getting-started-with-visual-studio-2019"></a>Começar com o Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) é uma única ferramenta que lhe permite fazer o seguinte:

- Conectar, consultar e desenvolver aplicações
- Aproveite um explorador de objetos para explorar visualmente todos os objetos do seu modelo de dados, incluindo tabelas, vistas, procedimentos armazenados, etc.
- Gere scripts de definição de dados T-SQL (DDL) para os seus objetos
- Desenvolva o seu armazém de dados utilizando uma abordagem estatal com projetos de base de dados SSDT
- Integre o seu projeto de base de dados com sistemas de controlo de fontes como git com Azure Repos
- Configurar gasodutos de integração e implementação contínuas com servidores de automação como o Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalar o Estúdio Visual 2019

Consulte [o Download Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para descarregar e instalar o Visual Studio **16.3 ou superior**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação Autónoma SSDT já não é necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Características não suportadas em SSDT

Há momentos em que os lançamentos de funcionalidades para Synapse SQL podem não incluir suporte para SSDT. As seguintes funcionalidades não são atualmente suportadas:

- [Vistas materializadas](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Índices de colunas agrupadas ordenadas](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [Declaração de cópia](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Gestão da carga de trabalho](sql-data-warehouse-workload-management.md) - grupos de carga de trabalho e classificadores
- [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Envie um [bilhete de apoio ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para obter o recurso suportado.
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Envie um [bilhete de apoio ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para obter o recurso suportado.
- [Mesas com restrições](sql-data-warehouse-table-constraints.md#table-constraints) não são suportadas. Para estes objetos de mesa, defina a ação de construção para "Nenhum".

## <a name="next-steps"></a>Passos seguintes

Agora que tem a versão mais recente do SSDT, está pronto para [se ligar](sql-data-warehouse-query-visual-studio.md) à sua piscina SQL.
