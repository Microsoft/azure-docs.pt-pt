---
title: 'Tutorial: Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online do RDS SQL Server para a base de dados do Azure SQL ou uma instância gerida da base de dados do Azure SQL | Documentos da Microsoft'
description: Aprenda a realizar uma migração online do SQL Server de RDS para a base de dados do Azure SQL ou uma instância gerida da base de dados do Azure SQL utilizando o serviço de migração de base de dados do Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 5691c10236b4aa3687551fe084ec5fefd9a3aeaa
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193386"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migrar o servidor de SQL de RDS para a base de dados do Azure SQL ou uma base de dados do Azure SQL instância gerida online com o DMS
Pode utilizar o serviço de migração de base de dados do Azure para migrar as bases de dados a partir de uma instância de servidor de SQL de RDS para [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) ou uma [instância gerida de base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) com período de indisponibilidade mínimo. Neste tutorial, migra os **Adventureworks2012** base de dados restaurada para um servidor de SQL de RDS instância do SQL Server 2012 (ou posterior) para a base de dados do Azure SQL ou uma base de dados do SQL Azure instância gerida utilizando a migração de base de dados do Azure Serviço.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Crie uma instância da base de dados do Azure SQL ou uma instância gerida da base de dados do Azure SQL. 
> * Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.
> * Transferir um relatório da migração.

> [!NOTE]
> Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium. Para obter mais informações, veja o Azure Database Migration Service [preços](https://azure.microsoft.com/pricing/details/database-migration/) página.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server de RDS para a base de dados do Azure SQL ou uma instância gerida da base de dados do Azure SQL.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

- Criar uma [base de dados do SQL Server do RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
- Criar uma instância do SQL Database do Azure, que pode fazer ao seguir o detalhe no artigo [criar uma base de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Se estiver a migrar para uma instância gerida da base de dados do Azure SQL, siga os detalhes no artigo [criar uma instância gerida da base de dados do Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)e, em seguida, crie uma base de dados vazia com o nome **AdventureWorks2012**. 
 
- Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 ou posterior.
- Crie uma rede Virtual do Azure (VNET) para o serviço de migração de base de dados do Azure com o modelo de implementação Azure Resource Manager. Se estiver a migrar para uma instância gerida da base de dados do Azure SQL, certifique-se criar a instância DMS na mesma VNET utilizada para a instância gerida da base de dados do Azure SQL, mas numa sub-rede diferente.  Em alternativa, se utilizar uma VNET diferente para DMS, terá de criar um VNET peering entre duas VNETs.
 
    > [!NOTE]
    > Durante a configuração VNET, se utilizar o ExpressRoute com peering de rede para a Microsoft, adicione o seguinte serviço [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) à sub-rede na qual o serviço será aprovisionado:
    > - Ponto de extremidade de destino da base de dados (por exemplo, ponto de extremidade do SQL, ponto final do Cosmos DB etc.)
    > - Ponto final de armazenamento
    > - Ponto final de barramento de serviço
    >
    > Esta configuração é necessária porque o serviço de migração de base de dados do Azure não tem conectividade à internet. 
 
- Certifique-se de que as regras do grupo de segurança de rede de VNET não bloqueiam as seguintes portas de comunicação 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego dos NSGs das VNETs do Azure, veja o artigo [Filter network traffic with network security groups](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Filtrar tráfego de rede com grupos de segurança de rede).
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abrir a firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
- Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Fornecer o intervalo de sub-redes da VNET utilizada no Azure Database Migration Service.
- Certifique-se de que as credenciais utilizadas para ligar à instância do SQL Server de RDS de origem estão associadas uma conta que seja membro da função de servidor "Processadmin" e um membro das funções de base de dados "db_owner" em todas as bases de dados que estão a ser migrados.
- Certifique-se de que as credenciais utilizadas para ligar à instância de base de dados do Azure SQL de destino tem permissão de base de dados de CONTROLE sobre as bases de dados do SQL do Azure de destino e um membro da função sysadmin, se a instância gerida de migrar para uma base de dados do SQL do Azure.
- A versão de RDS SQL Server de origem tem de ser SQL Server 2012 e superior. Para determinar a versão que a instância do SQL Server está a executar, leia o artigo [Como determinar a versão, edição e nível de atualização do SQL Server e respetivos componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Ative a captura de dados de alteração (CDC) na base de dados do SQL Server de RDS e todas as tabelas de utilizador selecionadas para migração.
    > [!NOTE]
    > Pode utilizar o script abaixo para ativar o CDC numa base de dados do SQL Server de RDS.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Pode utilizar o script abaixo para ativar o CDC em todas as tabelas.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
- Desative os acionadores de base de dados na Base de Dados SQL do Azure de destino.
    > [!NOTE]
    > Pode encontrar os acionadores de base de dados na Base de Dados SQL do Azure com a seguinte consulta:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Para obter mais informações, leia o artigo [DESATIVAR ACIONADOR (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Utilize o DMA para migrar o esquema para a base de dados do Azure SQL.

> [!NOTE]
> Antes de poder criar um projeto de migração no DMA, confirme que já aprovisionou uma base de dados SQL do Azure, conforme mencionado nos pré-requisitos. Para efeitos deste tutorial, o nome da base de dados do SQL Azure é considerado **AdventureWorks2012**, mas pode fornecer o nome que desejar.

Para migrar o esquema **AdventureWorks2012** para a Base de Dados SQL do Azure, realize os passos seguintes:

1.  No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2.  Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3.  Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do DMA deverá ser apresentada, conforme mostrado na imagem abaixo:
    
    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4.  Selecione **Create** (Criar) para criar o projeto.
5.  No DMA, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6.  Selecione **próxima**, em **ligar ao servidor de destino**, especifique os detalhes de ligação de destino para a base de dados SQL do Azure, selecione **Connect**e, em seguida, selecione o **AdventureWorksAzure** previamente aprovisionadas na base de dados do Azure SQL da base de dados.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7.  Selecione **Next** (Seguinte) para avançar para o ecrã **Select objects** (Selecionar objetos), no qual pode especificar os objetos do esquema na base de dados **AdventureWorks2012** que têm de ser implementados na Base de Dados SQL do Azure.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8.  Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9.  Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

    ![Implementar o esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration
1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.
 
   ![Mostrar subscrições no portal](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)
       
2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.
 
    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)
    
3.  Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.
 
    ![Registar o fornecedor de recursos](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância
1.  No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2.  No ecrã **Azure Database Migration Service**, selecione **Criar**.
 
    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3.  No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do Azure Database Migration Service. 

5. Selecione uma rede virtual (VNET) já existente ou crie uma nova.

    A VNET fornece ao Azure Database Migration Service acesso à instância do SQL Server de origem e à instância da Base de Dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, veja o artigo [Criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço; para essa migração online, certifique-se de que selecione o escalão de preço Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

     ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7.  Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.
 
      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.
 
     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)
 
3. Selecione + **Novo Projeto de Migração**.
4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **AWS RDS para o SQL Server**, no  **Tipo de servidor de destino** caixa de texto, selecione **base de dados do Azure SQL**.
5. Na **escolha o tipo de atividade** secção, selecione **migração de dados Online**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Em alternativa, pode escolher **Apenas criar projeto** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    ![Criar e Executar uma Atividade do Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)
 
## <a name="specify-source-details"></a>Especificar os detalhes da origem
1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação da instância do SQL Server de origem.
 
    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino
1. Selecione **Guardar** e, no ecrã, **Detalhes do destino da migração**, especifique os detalhes da ligação do servidor da Base de Dados SQL do Azure de destino, que é a Base de Dados SQL do Azure pré-aprovisionada na qual o esquema de **AdventureWorks2012** foi implementada com o DMA.

    ![Selecionar o Destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Selecione **Guardar**, no ecrã **Selecionar tabelas**, expanda a lista de tabelas e reveja a lista dos campos afetados.

    O Azure Database Migration Service seleciona automaticamente todas as tabelas de origem vazias que existem na instância da Base de Dados SQL do Azure de destino. Se quiser remigrate tabelas que já incluem dados, tem de selecionar explicitamente as tabelas neste ecrã.

    ![Selecionar tabelas](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4.  Selecione **salvar**, depois de definir o seguinte procedimento **definições de migração online avançadas**.
    
    | Definição | Descrição |
    | ------------- | ------------- |
    | **Número máximo de tabelas para carregar em paralelo** | Especifica o número de tabelas que o DMS é executado em paralelo durante a migração. O valor predefinido é 5, mas pode ser definido como um valor ideal para atender às necessidades específicas de migração com base em qualquer migrações de prova de conceito. |
    | **Quando a tabela de origem é truncada** | Especifica se o DMS trunca a tabela de destino durante a migração. Esta definição pode ser útil se uma ou mais tabelas são truncadas como parte do processo de migração. |
    | **Configurar as definições para os dados de objetos grandes (LOB)** | Especifica se o DMS efetua a migração de dados ilimitados de LOB ou limites dos dados LOB migrado para um tamanho específico.  Quando existe um limite nos dados LOB migrados, todos os dados LOB além esse limite são truncados. Para migrações de produção, é recomendado que selecione **permitir que o tamanho LOB ilimitado** para evitar a perda de dados. Ao especificar para permitir que o tamanho ilimitado de LOB, selecione o **LOB do departamento de migrar os dados num único bloco quando o tamanho LOB é inferior a (KB) especificados** caixa de verificação para melhorar o desempenho. |
    
    ![Configurar definições avançadas de migração online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5.  Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração
- Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

    ![Estado da Atividade - a inicializar](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração
1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

2. Clique numa base de dados específica para obter o estado da migração para as operações **Carregamento de dados completo** e **Sincronização de dados incrementais**.

    ![Estado da Atividade - em progresso](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração
Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Iniciar transferência](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)
 
2.  Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3.  Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado da migração de base de dados apresentar **Concluído**, ligue as suas aplicações à nova Base de Dados SQL do Azure de destino.
 
    ![Estado da Atividade - concluído](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Passos Seguintes
- Para obter informações sobre problemas conhecidos e limitações ao efetuar as migrações de online para DatabaseL de SQL do Azure, consulte o artigo [conhecidos de problemas e soluções alternativas com migrações de base de dados do Azure SQL online](known-issues-azure-sql-online.md).
- Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Para obter informações sobre a base de dados do Azure SQL, consulte o artigo [o que é o serviço de base de dados do Azure SQL?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
- Para obter informações sobre a base de dados do Azure SQL instâncias geridas, consulte a página [instância gerida da base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
