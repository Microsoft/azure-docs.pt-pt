---
title: SQL Server para SQL Database - Guia de migração
description: Siga este guia para migrar as bases de dados do SQL Server para a Base de Dados Azure SQL.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: ee4abaf3c9f6aa70ba14920711c8917994254649
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326986"
---
# <a name="migration-guide-sql-server-to-sql-database"></a>Guia de migração: SQL Server para SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Este guia ajuda-o a migrar a sua instância SQL Server para a Base de Dados Azure SQL. 

Pode migrar o SQL Server a funcionar no local ou em: 

- SQL Server nas Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de Base de Dados Relacional da Amazon (AWS RDS) 
- Motor compute (Plataforma Google Cloud - GCP)  
- Cloud SQL para SQL Server (Google Cloud Platform – GCP) 

Para obter mais informações sobre migração, consulte a visão geral da [migração.](sql-server-to-sql-database-overview.md) Para outros cenários, consulte o [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/)

:::image type="content" source="media/sql-server-to-database-overview/migration-process-flow-small.png" alt-text="Fluxo de processo de migração":::

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar o seu SQL Server para Azure SQL Database, certifique-se de que tem os seguintes pré-requisitos: 

- Um método de [migração](sql-server-to-sql-database-overview.md#compare-migration-options) escolhido e ferramentas correspondentes 
- [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) instalado numa máquina que pode ligar-se ao seu servidor SQL de origem
- Uma [base de dados Azure SQL](../../database/single-database-create-quickstart.md)


## <a name="pre-migration"></a>Pré-migração

Depois de verificar que o seu ambiente de origem é suportado, comece com a fase de pré-migração. Descubra todas as fontes de dados existentes, avalie a viabilidade da migração e identifique quaisquer problemas de bloqueio que possam impedir a sua migração. 

### <a name="discover"></a>Detetar

Na fase Discover, verifique a rede para identificar todas as instâncias e funcionalidades do SQL Server utilizadas pela sua organização. 

Use [a Azure Migrate](../../../migrate/migrate-services-overview.md) para avaliar a adequação da migração dos servidores no local, realize o dimensionamento baseado no desempenho e forneça estimativas de custos para executá-los em Azure. 

Em alternativa, utilize o [Microsoft Assessment and Planning Toolkit (o "MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) para avaliar a sua atual infraestrutura de TI. O conjunto de ferramentas fornece uma poderosa ferramenta de inventário, avaliação e relatório para simplificar o processo de planeamento da migração. 

Para obter mais informações sobre as ferramentas disponíveis para a fase Discover, consulte [serviços e ferramentas disponíveis para cenários de migração de dados.](../../../dms/dms-tools-matrix.md) 

### <a name="assess"></a>Avaliar 

Após a descoberta de fontes de dados, avalie qualquer base de dados do SQL Server no local que possa ser migrada para a Base de Dados Azure SQL para identificar bloqueadores de migração ou problemas de compatibilidade. 

Pode utilizar o Data Migration Assistant (versão 4.1 e posterior) para avaliar bases de dados para obter: 

- [Recomendações-alvo do Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Recomendações do Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Para avaliar o seu ambiente utilizando a Avaliação da Migração da Base de Dados, siga estes passos: 

1. Abra o [Assistente de Migração de Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selecione **File** e, em seguida, escolha **Nova avaliação**. 
1. Especifique um nome de projeto, selecione o SQL Server como o tipo de servidor de origem e, em seguida, selecione Azure SQL Database como o tipo de servidor alvo. 
1. Selecione os tipos(s) dos relatórios de avaliação que pretende gerar. Por exemplo, compatibilidade de bases de dados e paridade de recursos. Com base no tipo de avaliação, as permissões necessárias no servidor SQL de origem podem ser diferentes.  A DMA irá destacar as permissões necessárias para o conselheiro escolhido antes de executar a avaliação.
    - A categoria **de paridade** de recursos fornece um conjunto abrangente de recomendações, alternativas disponíveis em Azure, e medidas mitigadoras para ajudá-lo a planear o seu projeto de migração. (permissões sysadmin necessárias)
    - A categoria **de problemas de compatibilidade** identifica questões de compatibilidade de recursos parcialmente suportadas ou não apoiadas que podem bloquear a migração, bem como recomendações para as abordar `CONNECT SQL` `VIEW SERVER STATE` (, e `VIEW ANY DEFINITION` permissões necessárias).
1. Especifique os dados de ligação de origem para o seu SqL Server e ligue-se à base de dados de origem.
1. Selecione **Avaliação de início**. 
1. Após o fim do processo, selecione e reveja os relatórios de avaliação para o bloqueio da migração e questões de paridade de recursos. O relatório de avaliação também pode ser exportado para um ficheiro que pode ser partilhado com outras equipas ou pessoal da sua organização. 
1. Determine o nível de compatibilidade da base de dados que minimize os esforços pós-migração.  
1. Identifique a melhor SKU de Base de Dados Azure SQL para a sua carga de trabalho no local. 

Para saber mais, consulte [Realizar uma avaliação de migração do SQL Server com o Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Se a avaliação encontrar vários bloqueadores para confirmar que a sua base de dados não está pronta para uma migração da Base de Dados Azure SQL, então, em alternativa, considere:

- [Azure SQL Caso Gerido](../managed-instance/sql-server-to-managed-instance-overview.md) se houver várias dependências de instâncias
- [SQL Server em Máquinas Virtuais Azure](../virtual-machines/sql-server-to-sql-on-azure-vm-migration-overview.md) se tanto a Base de Dados SQL como a SqL Managed Instance não forem alvos adequados. 



#### <a name="scaled-assessments-and-analysis"></a>Avaliações e Análises Em Escala
O Assistente de Migração de Dados apoia a realização de avaliações em escala e a consolidação dos relatórios de avaliação para análise. 

Se tiver vários servidores e bases de dados que precisam de ser avaliados e analisados em escala para fornecer uma visão mais ampla da propriedade de dados, consulte os seguintes links para saber mais:

- [Realização de avaliações em escala utilizando o PowerShell](/sql/dma/dma-consolidatereports)
- [Analisar relatórios de avaliação utilizando o Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
> As avaliações em escala para várias bases de dados, especialmente as grandes, também podem ser automatizadas utilizando o [Utilitário da Linha de Comando DMA](/sql/dma/dma-commandline) e enviadas para a [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) para uma análise mais aprofundada e prontidão de destino.

## <a name="migrate"></a>Migrate

Depois de ter concluído as tarefas associadas à fase de pré-migração, está pronto para executar o esquema e a migração de dados. 

Migrar os seus dados utilizando o [seu método de migração](sql-server-to-sql-database-overview.md#compare-migration-options)escolhido. 

Este guia descreve as duas opções mais populares - Data Migration Assistant e Azure Database Migration Service. 

### <a name="data-migration-assistant-dma"></a>Assistente de Migração de Dados (DMA)

Para migrar uma base de dados do SQL Server para a Base de Dados Azure SQL utilizando o DMA, siga estes passos: 

1. Faça o download e instale o [Assistente de Migração da Base de Dados.](https://www.microsoft.com/download/details.aspx?id=53595)
1. Crie um novo projeto e selecione **a Migração** como o tipo de projeto.
1. Desconfiem do tipo de servidor de origem para **SQL Server** e o tipo de servidor-alvo para **Azure SQL Database**, selecione o âmbito de migração como **Schema e dados** e selecione **Criar**.
1. No projeto de migração, especifique os detalhes do servidor de origem, tais como o nome do servidor, credenciais para ligar ao servidor e a base de dados de origem para migrar.
1. Nos dados do servidor alvo, especifique o nome do servidor do servidor Azure SQL Database, credenciais para ligar ao servidor e à base de dados-alvo para migrar.
1. Selecione os objetos de esquema e inserte-os na base de dados target Azure SQL.
1. Por fim, selecione **Iniciar a migração** de dados e monitorizar o progresso da migração.

Para obter um tutorial detalhado, consulte [o Servidor SQL ou o SqL Server em VMs Azure para Azure SQL Database utilizando o Assistente de Migração de Dados](/sql/dma/dma-migrateonpremsqltosqldb). 


> [!NOTE]
> - Dimensione a sua base de dados para um nível de serviço mais elevado e tamanho de cálculo durante o processo de importação para maximizar a velocidade de importação, fornecendo mais recursos. Em seguida, pode reduzir verticalmente depois de a importação ser bem-sucedida.</br>
> - O nível de compatibilidade da base de dados importada baseia-se no nível de compatibilidade da sua base de dados de origem. 


### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

Para migrar bases de dados do SQL Server para a Base de Dados Azure SQL utilizando DMS, siga os passos abaixo:

1. Caso ainda não tenha registado o fornecedor de recursos **Microsoft.DataMigration** na sua subscrição. 
1. Crie uma Instância de Serviço de Migração de Bases de Dados Azure num local desejado à sua escolha (de preferência na mesma região que o seu target Azure SQL Database). Selecione uma rede virtual existente ou crie uma nova para hospedar a sua instância DMS.
1. Após a criação da sua instância DMS, crie um novo projeto de migração e especifique o tipo de servidor de origem como **SQL Server** e o tipo de servidor-alvo como **Base de Dados Azure SQL**. Escolha a **migração de dados offline** como o tipo de atividade na lâmina de criação de projetos de migração.
1. Especifique os detalhes do SQL Server de origem na página de detalhes **da fonte de migração** e os detalhes da Base de Dados Azure SQL alvo na página de detalhes **do destino migração.**
1. Mapear as bases de dados de origem e alvo para migração e, em seguida, selecione as tabelas que pretende migrar.
1. Reveja o resumo da migração e selecione **A migração run**. Em seguida, pode monitorizar a atividade de migração e verificar o progresso da migração da sua base de dados.

Para obter um tutorial detalhado, consulte [o Servidor SQL de Migração para uma Base de Dados Azure SQL utilizando DMS](../../../dms/tutorial-sql-server-to-azure-sql.md). 

## <a name="data-sync-and-cutover"></a>Sincronização de dados e corte

Ao utilizar opções de migração que replicam/sincronizam continuamente os dados mudam de origem para alvo, os dados de origem e esquema podem mudar e derivar do alvo. Durante a sincronização de dados, certifique-se de que todas as alterações na fonte são capturadas e aplicadas ao alvo durante o processo de migração. 

Depois de verificar se os dados são os mesmos tanto na fonte como no alvo, pode cortar da fonte para o ambiente alvo. É importante planear o processo de corte com equipas de negócios/aplicações para garantir que a interrupção mínima durante o corte não afete a continuidade do negócio. 

> [!IMPORTANT]
> Para obter mais informações sobre os passos específicos associados à realização de um corte como parte das migrações utilizando DMS, consulte [o corte de migração de desempenho](../../../dms/tutorial-sql-server-azure-sql-online.md#perform-migration-cutover).


## <a name="post-migration"></a>Pós-migração

Depois de ter concluído com sucesso a fase de migração, passe por uma série de tarefas pós-migração para garantir que tudo está funcionando de forma suave e eficiente. 

A fase pós-migração é crucial para conciliar quaisquer problemas de precisão de dados e verificar a completude, bem como abordar problemas de desempenho com a carga de trabalho. 

### <a name="remediate-applications"></a>Aplicações corretivas 

Após a migração dos dados para o ambiente alvo, todas as aplicações que anteriormente consumiam a fonte precisam de começar a consumir o alvo. A realização deste facto exigirá, em alguns casos, alterações nas aplicações.

### <a name="perform-tests"></a>Realizar testes

A abordagem de ensaio para a migração de bases de dados consiste nas seguintes atividades:

1. **Desenvolver testes de validação**: Para testar a migração da base de dados, é necessário utilizar consultas SQL. Tem de criar as consultas de validação para correr contra a fonte e as bases de dados-alvo. As suas consultas de validação devem abranger o âmbito que definiu.
1. **Configurar ambiente de teste**: O ambiente de ensaio deve conter uma cópia da base de dados de origem e da base de dados-alvo. Certifique-se de isolar o ambiente de teste.
1. **Executar testes de validação**: Escada os testes de validação contra a fonte e o alvo e, em seguida, analise os resultados.
1. **Executar testes de desempenho**: Executar teste de desempenho contra a fonte e o alvo e, em seguida, analisar e comparar os resultados.

   > [!NOTE]
   > Para obter assistência para desenvolver e executar testes de validação pós-migração, considere a Solução de Qualidade de Dados disponível a partir do parceiro [QuerySurge](https://www.querysurge.com/company/partners/microsoft). 


## <a name="leverage-advanced-features"></a>Alavancar recursos avançados 

Certifique-se de que aproveita as funcionalidades avançadas baseadas na nuvem oferecidas pela SQL Database, tais como [alta disponibilidade incorporada,](../../database/high-availability-sla.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [monitorização e afinação da sua carga de trabalho.](../../database/monitor-tune-overview.md) 

Algumas funcionalidades do SQL Server só estão disponíveis quando o [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) da base de dados for alterado para o nível de compatibilidade mais recente (150). 

Para saber mais, consulte [a gestão da Base de Dados Azure SQL após a migração](../../database/manage-data-after-migrating-to-database.md)


## <a name="next-steps"></a>Passos seguintes

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Base de Dados SQL consulte:
    - [Uma visão geral da Base de Dados Azure SQL](../../database/sql-database-paas-overview.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).