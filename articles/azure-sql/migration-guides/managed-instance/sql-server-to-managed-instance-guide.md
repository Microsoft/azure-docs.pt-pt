---
title: 'SQL Server para SQL Managed Instance: Guia de migração'
description: Siga este guia para migrar as bases de dados do SQL Server para Azure SQL Managed Instance.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 67f5665225bc1297d0eb1b1e1da954fb47660dee
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488965"
---
# <a name="migration-guide-sql-server-to-sql-managed-instance"></a>Guia de migração: SQL Server para SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Este guia ajuda-o a migrar a sua instância sql server para Azure SQL Managed Instance. 

Pode migrar o SQL Server a funcionar no local ou em: 

- SQL Server nas Máquinas Virtuais  
- Amazon Web Services (AWS) EC2 
- Serviço de Base de Dados Relacional da Amazon (AWS RDS) 
- Motor compute (Plataforma Google Cloud - GCP)  
- Cloud SQL para SQL Server (Google Cloud Platform – GCP) 

Para obter mais informações sobre migração, consulte a visão geral da [migração.](sql-server-to-managed-instance-overview.md) Para outros cenários, consulte o [Guia de Migração da Base de Dados.](https://datamigration.microsoft.com/)

:::image type="content" source="media/sql-server-to-managed-instance-overview/migration-process-flow-small.png" alt-text="Fluxo de processo de migração":::

## <a name="prerequisites"></a>Pré-requisitos 

Para migrar o seu SQL Server para Azure SQL Managed Instance, certifique-se de que passa pelos seguintes pré-requisitos: 

- Escolha um [método de migração](sql-server-to-managed-instance-overview.md#compare-migration-options) e as ferramentas correspondentes que são necessárias para o método escolhido
- Instale [o Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) numa máquina que possa ligar-se ao seu servidor SQL de origem


## <a name="pre-migration"></a>Pré-migração

Depois de verificar que o seu ambiente de origem é suportado, comece com a fase de pré-migração. Descubra todas as fontes de dados existentes, avalie a viabilidade da migração e identifique quaisquer problemas de bloqueio que possam impedir a sua migração.  

### <a name="discover"></a>Detetar

Na fase Discover, verifique a rede para identificar todas as instâncias e funcionalidades do SQL Server utilizadas pela sua organização. 

Use [a Azure Migrate](../../../migrate/migrate-services-overview.md) para avaliar a adequação da migração dos servidores no local, realize o dimensionamento baseado no desempenho e forneça estimativas de custos para executá-los em Azure. 

Em alternativa, utilize o [Microsoft Assessment and Planning Toolkit (o "MAP Toolkit")](https://www.microsoft.com/download/details.aspx?id=7826) para avaliar a sua atual infraestrutura de TI. O conjunto de ferramentas fornece uma poderosa ferramenta de inventário, avaliação e relatório para simplificar o processo de planeamento da migração. 

Para obter mais informações sobre as ferramentas disponíveis para a fase Discover, consulte [serviços e ferramentas disponíveis para cenários de migração de dados.](../../../dms/dms-tools-matrix.md) 

### <a name="assess"></a>Avaliação 

[!INCLUDE [assess-estate-with-azure-migrate](../../../../includes/azure-migrate-to-assess-sql-data-estate.md)]

Após a descoberta de fontes de dados, avalie quaisquer instâncias do SQL Server no local que possam ser migradas para Azure SQL Managed Instance para identificar bloqueadores de migração ou problemas de compatibilidade. 

Pode utilizar o Data Migration Assistant (versão 4.1 e posterior) para avaliar bases de dados para obter: 

- [Recomendações-alvo do Azure](/sql/dma/dma-assess-sql-data-estate-to-sqldb)
- [Recomendações do Azure SKU](/sql/dma/dma-sku-recommend-sql-db)

Para avaliar o seu ambiente utilizando a Avaliação da Migração da Base de Dados, siga estes passos: 

1. Abra o [Assistente de Migração de Dados (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). 
1. Selecione **File** e, em seguida, escolha **Nova avaliação**. 
1. Especifique um nome de projeto, selecione o SQL Server como o tipo de servidor de origem e, em seguida, selecione Azure SQL Managed Instance como o tipo de servidor alvo. 
1. Selecione os tipos(s) dos relatórios de avaliação que pretende gerar. Por exemplo, compatibilidade de bases de dados e paridade de recursos. Com base no tipo de avaliação, as permissões necessárias no servidor SQL de origem podem ser diferentes.  A DMA irá destacar as permissões necessárias para o conselheiro escolhido antes de executar a avaliação.
    - A categoria **de paridade** de recursos fornece um conjunto abrangente de recomendações, alternativas disponíveis em Azure, e medidas mitigadoras para ajudá-lo a planear o seu projeto de migração. (permissões sysadmin necessárias)
    - A categoria **de problemas de compatibilidade** identifica questões de compatibilidade de recursos parcialmente suportadas ou não apoiadas que podem bloquear a migração, bem como recomendações para as abordar `CONNECT SQL` `VIEW SERVER STATE` (, e `VIEW ANY DEFINITION` permissões necessárias).
1. Especifique os dados de ligação de origem para o seu SqL Server e ligue-se à base de dados de origem.
1. Selecione **Avaliação de início**. 
1. Quando o processo estiver concluído, selecione e reveja os relatórios de avaliação para o bloqueio da migração e questões de paridade de recursos. O relatório de avaliação também pode ser exportado para um ficheiro que pode ser partilhado com outras equipas ou pessoal da sua organização. 
1. Determine o nível de compatibilidade da base de dados que minimize os esforços pós-migração.  
1. Identifique o melhor SKU de Instância Gerida Azure SQL para a sua carga de trabalho no local. 

Para saber mais, consulte [Realizar uma avaliação de migração do SQL Server com o Data Migration Assistant](/sql/dma/dma-assesssqlonprem).

Se a SQL Managed Instance não for um alvo adequado para a sua carga de trabalho, o SQL Server em VMs Azure pode ser um alvo alternativo viável para o seu negócio. 

#### <a name="scaled-assessments-and-analysis"></a>Avaliações e Análises Em Escala

O Assistente de Migração de Dados apoia a realização de avaliações em escala e a consolidação dos relatórios de avaliação para análise. Se tiver vários servidores e bases de dados que precisam de ser avaliados e analisados em escala para fornecer uma visão mais ampla da propriedade de dados, clique nos seguintes links para saber mais.

- [Realização de avaliações em escala utilizando o PowerShell](/sql/dma/dma-consolidatereports)
- [Analisar relatórios de avaliação utilizando o Power BI](/sql/dma/dma-consolidatereports#dma-reports)

> [!IMPORTANT]
>As avaliações de execução em escala para várias bases de dados também podem ser automatizadas usando o [Utilitário da Linha de Comando da DMA,](/sql/dma/dma-commandline) que também permite que os resultados sejam enviados para [Azure Migrate](/sql/dma/dma-assess-sql-data-estate-to-sqldb#view-target-readiness-assessment-results) para uma análise mais aprofundada e prontidão de destino.

### <a name="create-a-performance-baseline"></a>Criar uma linha de base de desempenho

Se precisar de comparar o desempenho da sua carga de trabalho numa SqL Managed Instance com a sua carga de trabalho original em execução no SQL Server, crie uma linha de base de desempenho para usar para comparação. Consulte a [linha de base de desempenho](sql-server-to-managed-instance-performance-baseline.md) para saber mais. 

### <a name="create-sql-managed-instance"></a>Criar Instância Gerida do SQL 

Com base nas informações na fase de descoberta e avaliação, crie um alvo de tamanho adequado SQL Managed Instance. Pode fazê-lo utilizando o [portal Azure,](../../managed-instance/instance-create-quickstart.md) [o PowerShell](../../managed-instance/scripts/create-configure-managed-instance-powershell.md)ou um [Modelo Azure Resource Manager (ARM).](../../managed-instance/create-template-quickstart.md) 


## <a name="migrate"></a>Migrate

Depois de ter concluído as tarefas associadas à fase de pré-migração, está pronto para executar o esquema e a migração de dados. 

Migrar os seus dados utilizando o [seu método de migração](sql-server-to-managed-instance-overview.md#compare-migration-options)escolhido. 

Este guia descreve as duas opções mais populares - Azure Database Migration Service (DMS) e backup nativo e restaurar. 

### <a name="database-migration-service"></a>Database Migration Service

Para realizar migrações utilizando DMS, siga os passos abaixo:

1. Registe o fornecedor de recursos **Microsoft.DataMigration** na sua subscrição se estiver a efetuar isto pela primeira vez.
1. Crie um Azure Database Migration Service Instance em uma localização desejada à sua escolha (de preferência na mesma região que o seu target Azure SQL Managed Instance) e selecione uma rede virtual existente ou crie uma nova para hospedar o seu exemplo DMS.
1. Depois de criar a sua instância DMS, crie um novo projeto de migração e especifique o tipo de servidor de origem como **SQL Server** e o tipo de servidor-alvo como **Azure SQL Database Managed Instance**. Escolha o tipo de atividade na lâmina de criação de projeto - migração de dados on-line ou offline. 
1.  Especifique os detalhes do SqL Server de origem na página de detalhes **da fonte de migração** e os detalhes da instância gerida Azure SQL na página de detalhes **do alvo da migração.** Selecione **Seguinte**.
1. Escolha a base de dados que pretende migrar. 
1. Forneça definições de configuração para especificar a **Partilha de Rede SMB** que contém os ficheiros de cópia de segurança da sua base de dados. Utilize credenciais do Utilizador do Windows com DMS que possam aceder à partilha de rede. Forneça os detalhes da sua **conta de Armazenamento Azure**. 
1. Reveja o resumo da migração e escolha **a migração Run.** Em seguida, pode monitorizar a atividade de migração e verificar o progresso da migração da sua base de dados.
1. Depois de restaurar a base de dados, escolha **Start cutover**. O processo de migração copia a cópia de segurança do registo traseiro assim que a disponibilizar na partilha da rede SMB e restaurá-la no alvo. 
1. Pare todo o tráfego de entrada na sua base de dados de origem e atualize o fio de ligação à nova base de dados Azure SQL Managed Instance. 

Para obter um tutorial passo a passo detalhado desta opção de migração, consulte [o Servidor SQL migratório para um Azure SQL Managed Instance on-line utilizando DMS](../../../dms/tutorial-sql-server-managed-instance-online.md). 
   


### <a name="backup-and-restore"></a>Cópia de segurança e restauro 

Uma das principais capacidades do Azure SQL Managed Instance para permitir a migração rápida e fácil da base de dados é a restauração nativa da cópia de segurança da base de dados ( `.bak` ) ficheiros armazenados no [Azure Storage](https://azure.microsoft.com/services/storage/). Cópia de segurança e restauro é uma operação assíncronea baseada no tamanho da sua base de dados. 

O diagrama a seguir fornece uma visão geral de alto nível do processo:

![O diagrama mostra o SQL Server com uma seta marcada como BACKUP / Upload para URL fluindo para O Armazenamento Azure e uma segunda seta marcada RESTAURADA do URL que flui do Armazenamento Azure para uma Instância Gerida de SQL.](./media/sql-server-to-managed-instance-overview/migration-restore.png)

> [!NOTE]
> O momento de pegar na cópia de segurança, carregá-la para o armazenamento Azure e realizar uma operação de restauro nativa para Azure SQL Managed Instance baseia-se no tamanho da base de dados. Fator um tempo de inatividade suficiente para acomodar a operação para grandes bases de dados. 


Para migrar usando cópia de segurança e restaurar, siga estes passos: 

1. Resquim a base de dados para o armazenamento de bolhas Azure. Por exemplo, use [backup para url](/sql/relational-databases/backup-restore/sql-server-backup-to-url) no [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Utilize a [Ferramenta Microsoft Azure](https://go.microsoft.com/fwlink/?LinkID=324399) para suportar bases de dados mais cedo do que o SQL Server 2012 SP1 CU2. 
1. Conecte-se ao seu Azure SQL Managed Instance usando o SQL Server Management Studio. 
1. Crie uma credencial utilizando uma Assinatura de Acesso Partilhado para aceder à sua conta de armazenamento Azure Blob com as cópias de segurança da sua base de dados. Por exemplo:

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```
1. Restaurar a cópia de segurança do recipiente de armazenamento Azure. Por exemplo: 

    ```sql
   RESTORE DATABASE [TargetDatabaseName] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

1. Assim que a restauração estiver concluída, consulte a base de dados no **Object Explorer** dentro do SQL Server Management Studio. 

Para saber mais sobre esta opção de migração, consulte [Restaurar uma base de dados para Azure SQL Managed Instance com SSMS](../../managed-instance/restore-sample-database-quickstart.md).

> [!NOTE]
> Uma operação de restauro de base de dados é assíncrol e recímível. Pode obter um erro no SQL Server Management Studio se a ligação se partir ou se expirar uma pausa. A Azure SQL Database continuará a tentar restaurar a base de dados em segundo plano, e pode acompanhar o progresso da restauração utilizando as [vistas sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) e [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)



## <a name="data-sync-and-cutover"></a>Sincronização de dados e corte

Ao utilizar opções de migração que replicam/sincronizam continuamente os dados mudam de origem para alvo, os dados de origem e esquema podem mudar e derivar do alvo. Durante a sincronização de dados, certifique-se de que todas as alterações na fonte são capturadas e aplicadas ao alvo durante o processo de migração. 

Depois de verificar se os dados são os mesmos tanto na fonte como no alvo, pode cortar da fonte para o ambiente alvo. É importante planear o processo de corte com equipas de negócios/aplicações para garantir que a interrupção mínima durante o corte não afete a continuidade do negócio. 

> [!IMPORTANT]
> Para obter mais informações sobre os passos específicos associados à realização de um corte como parte das migrações utilizando DMS, consulte [o corte de migração de desempenho](../../../dms/tutorial-sql-server-managed-instance-online.md#performing-migration-cutover).


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

Certifique-se de aproveitar as funcionalidades avançadas baseadas na nuvem oferecidas pela SQL Managed Instance, tais como [alta disponibilidade incorporada,](../../database/high-availability-sla.md) [deteção de ameaças,](../../database/azure-defender-for-sql.md) [monitorização e afinação da sua carga de trabalho.](../../database/monitor-tune-overview.md) 

[O Azure SQL Analytics](../../../azure-monitor/insights/azure-sql.md) permite-lhe monitorizar um grande conjunto de casos geridos de forma centralizada.

Algumas funcionalidades do SQL Server só estão disponíveis quando o [nível de compatibilidade](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) da base de dados for alterado para o nível de compatibilidade mais recente (150). 


## <a name="next-steps"></a>Passos seguintes

- Para uma matriz dos serviços e ferramentas da Microsoft e de terceiros que estão disponíveis para o ajudar com vários cenários de base de dados e migração de dados, bem como tarefas especiais, consulte [o Serviço e ferramentas para a migração de dados.](../../../dms/dms-tools-matrix.md)

- Para saber mais sobre a Azure SQL Managed Instance consulte:
   - [Níveis de serviço em Azure SQL Gestão De Instância](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Diferenças entre o SQL Server e o Azure SQL Managed Instance](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Custo total da calculadora de propriedade](https://azure.microsoft.com/pricing/tco/calculator/) 


- Para saber mais sobre o ciclo de enquadramento e adoção para migrações em nuvem, ver
   -  [Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [As melhores práticas para custos e cargas de trabalho de tamanho migram para Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Para avaliar a camada de acesso à aplicação, consulte o [Kit de Ferramentas de Migração do Acesso a Dados (Pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Para mais detalhes sobre como realizar testes da Camada A/B do Acesso de Dados consulte [o Assistente de Experimentação da Base de Dados](/sql/dea/database-experimentation-assistant-overview).