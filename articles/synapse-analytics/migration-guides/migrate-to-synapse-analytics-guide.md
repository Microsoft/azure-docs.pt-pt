---
title: 'Azure Synapse Analytics: Guia de migração'
description: Siga este guia para migrar as suas bases de dados para a Azure Synapse Analytics dedicada à piscina SQL.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: e27e7e0bb4616508c8f137260bde68de9420f9cf
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644974"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrando um armazém de dados para uma piscina de SQL dedicada em Azure Synapse Analytics 
As secções seguintes fornecem uma visão geral do que está envolvido na migração de uma solução de armazém de dados existente para a piscina de SQL dedicada Azure Synapse.

## <a name="overview"></a>Descrição Geral
Antes de migrar, deve verificar se o Azure Synapse Analytics é a melhor solução para a sua carga de trabalho. Azure Synapse Analytics é um sistema distribuído projetado para executar análises em grandes dados. Migrar para a Azure Synapse Analytics requer algumas mudanças de design que não são difíceis de entender, mas que podem levar algum tempo a implementar. Se o seu negócio necessitar de um armazém de dados de classe empresarial, os benefícios valem o esforço. No entanto, se não necessitar da potência da Azure Synapse Analytics, é mais rentável utilizar o [SQL Server](https://docs.microsoft.com/sql/sql-server/) ou [a Azure SQL Database](https://docs.microsoft.com/azure/azure-sql/).

Considere usar a Azure Synapse Analytics quando:
- Tenha um ou mais Terabytes de dados.
- Planeia executar análises em quantidades substanciais de dados.
- Precisa da capacidade de escalar a computação e armazenamento.
- Quer economizar em custos fazendo uma pausa nos recursos de computação quando não precisa deles.

Em vez de Azure Synapse Analytics, considere outras opções para cargas de trabalho operacionais (OLTP) que têm:
- Leituras e escritos de alta frequência.
- Um grande número de singleton seleciona.
- Grandes volumes de inserções de linha única.
- Necessidades de processamento de linha a linha.
- Formatos incompatíveis (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
Um dos bloqueadores críticos que os clientes enfrentam é a tradução dos seus objetos de base de dados ao migrar de um sistema para outro. [O Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ajuda-o a fazer upgrade para uma plataforma moderna de armazém de dados, automatizando a tradução de objetos do seu armazém de dados existente. É uma ferramenta gratuita, intuitiva e fácil de usar que automatiza a tradução de código permitindo uma migração mais rápida para a Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos
# <a name="migrate-from-sql-server"></a>[Migrar a partir do SQL Server](#tab/migratefromSQLServer)
Para migrar o seu armazém de dados SQL Server para a Azure Synapse Analytics, certifique-se de que tem os seguintes pré-requisitos: 

- Um armazém de dados ou carga de trabalho de Analytics 
- Descarregue a mais recente ferramenta [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos Azure Synapse.
- Uma [piscina SQL dedicada](../get-started-create-workspace.md) no espaço de trabalho Azure Synapse. 

# <a name="migrate-from-netezza"></a>[Migrar de Netezza](#tab/migratefromNetezza)
Para migrar o seu armazém de dados Netezza para a Azure Synapse Analytics, certifique-se de que tem os seguintes pré-requisitos: 

- Descarregue a mais recente ferramenta [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos Azure Synapse.
- Uma [piscina SQL dedicada](../get-started-create-workspace.md) no espaço de trabalho Azure Synapse.

Para mais informações visite [soluções Azure Synapse Analytics e migração para netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrar de Snowflake](#tab/migratefromSnowflake)
Para migrar o seu armazém de dados snowflake para a Azure Synapse Analytics, certifique-se de que tem os seguintes pré-requisitos: 

- Descarregue a mais recente ferramenta [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos de Floco de Neve para objetos Azure Synapse.
- Uma [piscina SQL dedicada](../get-started-create-workspace.md) no espaço de trabalho Azure Synapse. 

# <a name="migrate-from-oracle"></a>[Migrar do Oráculo](#tab/migratefromOracle)
Para migrar o seu armazém de dados oracle para a Azure Synapse Analytics, certifique-se de que tem os seguintes pré-requisitos: 

- Um armazém de dados ou carga de trabalho de Analytics 
- Faça o download do SSMA para o Oracle para converter objetos Oracle no SQL Server. Consulte [as bases de dados do Oráculo Migratório para o SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) para obter mais informações. 
- Descarregue a versão mais recente da ferramenta [Azure Synapse Pathway](https://www.microsoft.com/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos Azure Synapse.
- Uma [piscina SQL dedicada](../get-started-create-workspace.md) no espaço de trabalho Azure Synapse. 

Para mais informações visite [soluções Azure Synapse Analytics e migração para um armazém de dados oracle.](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata)

---

## <a name="pre-migration"></a>Pré-migração
Depois de tomar a decisão de migrar uma solução existente para o Azure Synapse Analytics, é importante planear a migração antes de começar. Um objetivo primordial do planeamento é garantir que os seus dados, esquemas de mesa e código são compatíveis com a Azure Synapse Analytics. Existem algumas diferenças de compatibilidade entre o seu sistema atual e o Azure Synapse Analytics que terá de trabalhar. Além disso, migrar grandes quantidades de dados para Azure leva tempo. Um planeamento cuidadoso acelerará o processo de entrega dos seus dados ao Azure. Outro objetivo chave do planeamento é ajustar o seu design para garantir que a sua solução tire o máximo partido do desempenho de alta consulta que o Azure Synapse Analytics foi concebido para fornecer. Projetar armazéns de dados para escala introduz padrões de design únicos, por isso as abordagens tradicionais nem sempre são as melhores. Embora alguns ajustes de design possam ser feitos após a migração, fazer alterações mais cedo no processo irá economizar tempo mais tarde.

## <a name="migrate"></a>Migrate
Realizar uma migração bem sucedida requer que migrar os seus esquemas de mesa, código e dados. Para obter orientações mais detalhadas sobre estes tópicos, consulte:
- O artigo [Considere o design da tabela.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview)
- O artigo [Considere a alteração de código.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques)
- O artigo [Migrar os seus dados.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading)
- O artigo [Considere Gestão da Carga de Trabalho.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management)

## <a name="additional-resources"></a>Recursos adicionais 
- O CAT (Customer Advisory Team) tem algumas grandes orientações da Azure Synapse Analytics (anteriormente SQL DW) publicadas como publicações de blog. Não se esqueça de dar uma olhada no seu artigo, [migrando dados para o Azure SQL Data Warehouse na prática,](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)para obter orientações adicionais sobre migração.

## <a name="migration-assets-from-real-world-engagements"></a>Ativos de migração de compromissos no mundo real
Para obter assistência adicional para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos em apoio de um envolvimento de projetos de migração no mundo real.

| Título/ligação                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda muito a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo. |
| [Tratamento de problemas de codificação de dados ao carregar dados para Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Este blog destina-se a fornecer informações sobre alguns dos problemas de codificação de dados que poderá encontrar ao utilizar o PolyBase para carregar dados para o SQL Data Warehouse. Este artigo também fornece algumas opções que você pode usar para superar tais problemas e carregar os dados com sucesso. |
| [Obtenção de tamanhos de mesa em Azure Synapse Analytics piscina SQL dedicada](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Uma das tarefas-chave que um arquiteto deve executar é obter métricas sobre um novo ambiente pós-migração: recolher tempos de carga desde as instalações até à nuvem, recolhendo tempos de carga da PolyBase, etc. Destas tarefas, uma das mais importantes é determinar o tamanho de armazenamento no SQL Data Warehouse em comparação com a plataforma atual do cliente. |
| [Utilitário para mover logins de servidor SQL nas instalações para Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Um script PowerShell que cria um script de comando T-SQL para recriar logins e selecionar utilizadores de bases de dados de um SqL Server "nas instalações" para um serviço Azure SQL PaaS. A ferramenta permite o mapeamento automático das contas AD do Windows para contas AZure AD ou pode fazer procuras UPN para cada login contra as instalações do Windows Ative Directy. A ferramenta move opcionalmente os logins nativos do SQL Server também. As funções personalizadas de servidor e base de dados são scriptadas, bem como a filiação de funções e a função de base de dados e permissões do utilizador. As bases de dados contidas ainda não estão suportadas e apenas um subconjunto de possíveis permissões do SQL Server são scriptados; ou seja, as autorizações de concessão com subvenção não são suportadas (árvores de permissão complexas). Mais detalhes estão disponíveis no documento de suporte e o script tem comentários para facilitar a compreensão. |

A equipa de Engenharia Data SQL desenvolveu estes recursos. A principal carta desta equipa é desbloquear e acelerar a modernização complexa dos projetos de migração da plataforma de dados para a plataforma de dados Azure da Microsoft.

## <a name="videos"></a>Vídeos
- Veja como a [Walgreens emigrou o seu sistema](https://www.youtube.com/watch?v=86dhd8N1lH4) de inventário de retalho com cerca de 100TB de dados da Netezza para a Azure Synapse Analytics (anteriormente SQL DW) em tempo recorde. 