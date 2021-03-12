---
title: 'Oráculo para Azure Synapse Analytics: Guia de migração'
description: As secções seguintes fornecem uma visão geral do que está envolvido na migração de uma solução de base de dados Oracle existente para a Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 08/25/2020
ms.openlocfilehash: 6b5412b24ce6da3476e0c80f31fb07e3647fe5a2
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225795"
---
# <a name="migration-guide-migrate-oracle-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Guia de migração: Migrar o armazém de dados da Oracle para uma piscina SQL dedicada em Azure Synapse Analytics
As secções seguintes fornecem uma visão geral do que está envolvido na migração de uma solução existente de armazém de dados oracle para a Azure Synapse Analytics.

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

## <a name="prerequisites"></a>Pré-requisitos
Para migrar o seu armazém de dados oracle para a Azure Synapse Analytics, certifique-se de que tem os seguintes pré-requisitos: 

- Um armazém de dados ou carga de trabalho de Analytics 
- SSMA para a Oracle converter objetos Oráculos para SQL Server. Consulte [as bases de dados do Oráculo Migratório para o SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) para obter mais informações. 
- Versão mais recente da ferramenta [Azure Synapse Pathway](https://www.microsoft.com/en-us/download/details.aspx?id=102787) para migrar objetos do SQL Server para objetos Azure Synapse.
- Uma [piscina SQL dedicada](../get-started-create-workspace.md) no espaço de trabalho Azure Synapse.  


## <a name="pre-migration"></a>Pré-migração
Depois de tomar a decisão de migrar uma solução existente para o Azure Synapse Analytics, é importante planear a migração antes de começar. Um objetivo primordial do planeamento é garantir que os seus dados, esquemas de mesa e código são compatíveis com a Azure Synapse Analytics. Existem algumas diferenças de compatibilidade entre o seu sistema atual e o SQL Data Warehouse que terá de trabalhar. Além disso, migrar grandes quantidades de dados para Azure leva tempo. Um planeamento cuidadoso acelerará o processo de entrega dos seus dados ao Azure. Outro objetivo chave do planeamento é ajustar o seu design para garantir que a sua solução tire o máximo partido do desempenho de alta consulta que o Azure Synapse Analytics foi concebido para fornecer. Projetar armazéns de dados para escala introduz padrões de design únicos, por isso as abordagens tradicionais nem sempre são as melhores. Embora alguns ajustes de design possam ser feitos após a migração, fazer alterações mais cedo no processo irá economizar tempo mais tarde.

## <a name="azure-synapse-pathway"></a>Caminho da Sinapse Azure
Um dos bloqueadores críticos que os clientes enfrentam é a tradução do seu código SQL ao migrar de um sistema para outro. [O Azure Synapse Pathway](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ajuda-o a fazer upgrade para uma plataforma moderna de armazém de dados, automatizando a tradução de código do seu armazém de dados existente. É uma ferramenta gratuita, intuitiva e fácil de usar que automatiza a tradução de código permitindo uma migração mais rápida para a Azure Synapse Analytics.

## <a name="migrate"></a>Migrate
Realizar uma migração bem sucedida requer que migrar os seus esquemas de mesa, código e dados. Para obter orientações mais detalhadas sobre estes tópicos, consulte:
- O artigo [Migrar os seus esquemas.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop)
- O artigo [Migrar o seu código.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop)
- O artigo [Migrar os seus dados.](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop)

## <a name="additional-resources"></a>Recursos adicionais 
- O CAT (Customer Advisory Team) tem algumas grandes orientações da Azure Synapse Analytics (anteriormente SQL Data Warehouse) publicadas como postagens de blog. Não se esqueça de dar uma olhada no seu artigo, [migrando dados para o Azure SQL Data Warehouse na prática,](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice)para obter orientações adicionais sobre migração.
- Consulte o livro branco [Escolhendo o caminho de migração da sua base de dados para Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/choosing-your-database-migration-path-to-azure/Choosing_your_database_migration_path_to_Azure.pdf) para obter informações e recomendações adicionais.
- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de migração de dados e dados, bem como tarefas especiais, consulte o serviço de [artigos e ferramentas para a migração de dados.](https://docs.microsoft.com/azure/dms/dms-tools-matrix)

## <a name="migration-assets-from-real-world-engagements"></a>Ativos de migração de compromissos no mundo real
Para obter assistência adicional para completar este cenário de migração, consulte os seguintes recursos, que foram desenvolvidos em apoio de um envolvimento de projetos de migração no mundo real.

| Título/ligação                              | Description                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e ferramenta de avaliação da carga de trabalho de dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Esta ferramenta fornece plataformas-alvo sugeridas "melhor ajuste", prontidão na nuvem e nível de remediação de aplicações/bases de dados para uma determinada carga de trabalho. Oferece um cálculo simples e de um clique e uma geração de relatórios que ajuda muito a acelerar as grandes avaliações imobiliárias, fornecendo e automatizada e uniforme processo de decisão da plataforma-alvo. |
| [Tratamento de problemas de codificação de dados ao carregar dados para Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Este blog destina-se a fornecer informações sobre alguns dos problemas de codificação de dados que poderá encontrar ao utilizar o PolyBase para carregar dados para o SQL Data Warehouse. Este artigo também fornece algumas opções que você pode usar para superar tais problemas e carregar os dados com sucesso. |
| [Obtenção de tamanhos de mesa na piscina SQL Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Uma das tarefas-chave que um arquiteto deve executar é obter métricas sobre um novo ambiente pós-migração: recolher tempos de carga desde as instalações até à nuvem, recolhendo tempos de carga da PolyBase, etc. Destas tarefas, uma das mais importantes é determinar o tamanho de armazenamento no SQL Data Warehouse em comparação com a plataforma atual do cliente. |
| [Utilitário para mover logins de servidor SQL nas instalações para Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Um script PowerShell que cria um script de comando T-SQL para recriar logins e selecionar utilizadores de bases de dados de um SqL Server "nas instalações" para um serviço Azure SQL PaaS. A ferramenta permite o mapeamento automático das contas AD do Windows para contas AZure AD ou pode fazer procuras UPN para cada login contra as instalações do Windows Ative Directy. A ferramenta move opcionalmente os logins nativos do SQL Server também. As funções personalizadas de servidor e base de dados são scriptadas, bem como a filiação de funções e a função de base de dados e permissões do utilizador. As bases de dados contidas ainda não estão suportadas e apenas um subconjunto de possíveis permissões do SQL Server são scriptados; ou seja, as autorizações de concessão com subvenção não são suportadas (árvores de permissão complexas). Mais detalhes estão disponíveis no documento de suporte e o script tem comentários para facilitar a compreensão. |

> [!NOTE]
> Estes recursos acima foram desenvolvidos como parte do Data Migration Jumpstart Program (DM Jumpstart), que é patrocinado pela equipa de engenharia do Azure Data Group. A principal carta da DM Jumpstart é desbloquear e acelerar a modernização complexa e competir oportunidades de migração da plataforma de dados para a plataforma de dados Azure Data da Microsoft. Se acha que a sua organização estaria interessada em participar no programa DM Jumpstart, contacte a sua equipa de conta e peça para que apresentem uma nomeação.

## <a name="videos"></a>Vídeos
- Para obter uma visão geral do Guia de Migração da Base de Dados Azure e das informações que contém, consulte o vídeo [Como Utilizar o Guia de Migração da Base de Dados](https://azure.microsoft.com/resources/videos/how-to-use-the-azure-database-migration-guide/).
- Para uma caminhada pelas fases do processo de migração e detalhes sobre as ferramentas e serviços específicos recomendados para a avaliação e migração, consulte o vídeo [visão geral da viagem de migração e as ferramentas/serviços recomendados para a realização de avaliação e migração.](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)