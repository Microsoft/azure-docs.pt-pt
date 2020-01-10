---
title: Instalar o Visual Studio 2019
description: Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 01/07/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b2b7b2413a187c446aace4eb1e416dea1b4258d3
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750654"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>Introdução ao Visual Studio 2019 para SQL Data Warehouse
O SSDT (Visual Studio **2019** SQL Server Data Tools) é uma única ferramenta que permite que você faça o seguinte:

- Conecte-se, consulte e desenvolva aplicativos para SQL Data Warehouse 
- Aproveite um pesquisador de objetos para explorar visualmente todos os objetos em seu modelo de dados, incluindo tabelas, exibições, procedimentos armazenados e etc.
- Gerar scripts de linguagem de definição de dados (DDL) T-SQL para seus objetos
- Desenvolva seu data warehouse usando uma abordagem baseada em estado com projetos de banco de dados SSDT
- Integre seu projeto de banco de dados com sistemas de controle do código-fonte, como git com Azure Repos
- Configurar pipelines de implantação e integração contínua com servidores de automação, como o Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalar o Visual Studio 2019
Consulte [Baixe o Visual Studio 2019][] para baixar e instalar o visual Studio **16,3 e superior**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação autônoma do SSDT não é mais necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Recursos sem suporte no SSDT

Há ocasiões em que as versões de recursos para SQL Data Warehouse podem não incluir suporte para SSDT. Atualmente, não há suporte para os seguintes recursos:

- [Exibições materializadas](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (em andamento)
- [Índices Columnstore clusterizados ordenados](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (em andamento)
- [Instrução de cópia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (em andamento)
- [Gerenciamento de carga de trabalho](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) -grupos de carga de trabalho e classificadores (em andamento)
- [Segurança ao nível da linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Envie um tíquete de suporte ou vote [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para obter o recurso com suporte.
- [Máscara de dados dinâmica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Envie um tíquete de suporte ou vote [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para obter o recurso com suporte. 
- Função [Predict](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- Não há suporte para [tabelas com restrições](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) . Para esses objetos de tabela, defina a ação de Build como "None".

## <a name="next-steps"></a>Passos seguintes

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar][connect] ao seu SQL data warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Baixe o Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
