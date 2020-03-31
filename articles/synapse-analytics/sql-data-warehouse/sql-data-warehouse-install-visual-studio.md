---
title: Instalar o Estúdio Visual 2019
description: Instale ferramentas de desenvolvimento de estúdio visual e servidor SQL (SSDT) para SQL Analytics
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
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351631"
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
Consulte [o Download Visual Studio 2019][] para descarregar e instalar o Visual Studio **16.3 ou superior**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação Autónoma SSDT já não é necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Características não suportadas em SSDT

Há momentos em que os lançamentos de funcionalidades para o SQL Analytics podem não incluir suporte para SSDT. As seguintes funcionalidades não são atualmente suportadas:

- [Pontos de vista materializados](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (em curso)
- [Índices de colunas agrupadas ordenados](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (em curso)
- [Declaração DE CÓPIA](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (em curso)
- [Gestão da carga de trabalho](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - grupos de carga de trabalho e classificadores (em curso)
- [Segurança ao Nível da Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Envie aqui um bilhete de apoio ou [vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) aqui para obter o recurso apoiado.
- [Máscara de dados dinâmica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Envie aqui um bilhete de apoio ou [vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) aqui para obter o recurso apoiado. 
- [FUNÇÃO PREVISÃO](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- [Mesas com restrições](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) não são suportadas. Para estes objetos de mesa, defina a ação de construção para "Nenhum".

## <a name="next-steps"></a>Passos seguintes

Agora que tem a versão mais recente do SSDT, está pronto para [se ligar](sql-data-warehouse-query-visual-studio.md) à sua piscina SQL.




<!--Other-->

[Baixar Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
