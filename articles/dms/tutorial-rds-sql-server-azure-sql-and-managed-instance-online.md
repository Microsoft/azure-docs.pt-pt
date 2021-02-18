---
title: 'Tutorial: Migrar o Servidor RDS SQL online para a Base de Dados SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line do RDS SQL Server para Azure SQL Database de uma única base de dados ou caso gerido utilizando o Serviço de Migração da Base de Dados Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 484598c7543e146618b6d2ab1f12bdf13710946b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091366"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Tutorial: Migrar o Servidor RDS SQL para a base de dados Azure SQL ou uma instância gerida Azure SQL online usando DMS

Pode utilizar o Serviço de Migração da Base de Dados Azure para migrar as bases de dados de uma instância do Servidor RDS SQL para [a Base de Dados Azure SQL](/azure/sql-database/) ou uma [Instância Gerida Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) com tempo de inatividade mínimo. Neste tutorial, migra a base de **dados Adventureworks2012** restaurada para uma instância do SERVIDOR RDS SQL do SQL Server 2012 (ou mais tarde) para a Base de Dados SQL ou uma SqL Managed Instance utilizando o Serviço de Migração da Base de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Crie uma base de dados na Base de Dados Azure SQL ou numa SqL Managed Instance. 
> * Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.
> * Transferir um relatório da migração.

> [!NOTE]
> A utilização do Serviço de Migração da Base de Dados Azure para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço [de Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração on-line do RDS SQL Server para a SQL Database ou uma SQL Managed Instance.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Criar uma [base de dados do SERVIDOR RDS SQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Crie uma base de dados na Base de Dados Azure SQL no portal Azure](../azure-sql/database/single-database-create-quickstart.md) ou [crie uma base de dados em SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md), e, em seguida, crie uma base de dados vazia chamada **AdventureWorks2012**. 
* Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 ou posterior.
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure. Se estiver a migrar para uma SQL Managed Instance, certifique-se de criar a instância DMS na mesma rede virtual utilizada para a SQL Managed Instance, mas numa sub-rede diferente.  Em alternativa, se utilizar uma rede virtual diferente para DMS, precisa de criar uma rede virtual que espreita entre as duas redes virtuais. Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet. 

* Certifique-se de que as regras do Grupo de Segurança da Rede de Rede Virtual não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abrir a firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Para a Base de Dados SQL, crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) ao nível do servidor para permitir o acesso do Serviço de Migração da Base de Dados Azure à base de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* Certifique-se de que as credenciais utilizadas para ligar à origem rds SQL Server estão associadas a uma conta que é membro do servidor "Processadmin" e um membro das funções de base de dados "db_owner" em todas as bases de dados que devem ser migradas.
* Certifique-se de que as credenciais utilizadas para ligar à base de dados-alvo têm permissão de BASE DE DADOS de CONTROLO na base de dados-alvo na Base de Dados SQL e um membro da função sysadmin se migrar para uma base de dados em SQL Managed Instance.
* A versão source RDS SQL Server deve ser SQL Server 2012 ou acima. Para determinar a versão que a instância do SQL Server está a executar, leia o artigo [Como determinar a versão, edição e nível de atualização do SQL Server e respetivos componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Ativar a Captura de Dados de Alteração (CDC) na base de dados do SERVIDOR RDS SQL e em todas as tabelas de utilizadores selecionadas para migração.
    > [!NOTE]
    > Pode utilizar o script abaixo para ativar o CDC numa base de dados do SERVIDOR RDS SQL.
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
* Desativar os gatilhos da base de dados do alvo.
    > [!NOTE]
    > Pode encontrar os gatilhos da base de dados na base de dados-alvo utilizando a seguinte consulta:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Para obter mais informações, leia o artigo [DESATIVAR ACIONADOR (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Utilize O DMA para migrar o esquema.

> [!NOTE]
> Antes de criar um projeto de migração em DMA, certifique-se de que já aprovisionou uma base de dados na Base de Dados SQL ou na SQL Managed Instance, conforme mencionado nos pré-requisitos. Para efeitos deste tutorial, o nome da base de dados é assumido como **AdventureWorks2012,** mas pode fornecer o nome que desejar.

Para migrar o esquema **AdventureWorks2012,** execute os seguintes passos:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).

    > [!NOTE]
    > Para o tipo de servidor target, selecione **Azure SQL Database** para migrar tanto para a Base de Dados Azure SQL como para a SQL Managed Instance.

3. Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do DMA deverá ser apresentada, conforme mostrado na imagem abaixo:

    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Selecione **Create** (Criar) para criar o projeto.
5. No DMA, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Selecione **Seguinte**, em **Connect to target server**, especifique os dados de conexão-alvo para a base de dados na Base de Dados SQL ou na SQL Managed Instance, selecione **Connect** e, em seguida, selecione a base de dados **AdventureWorksAzure** que já a provisionou.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Selecione **Seguinte** para avançar para o ecrã **de objetos Select,** no qual pode especificar os objetos de esquema na base de dados **AdventureWorks2012** que precisam de ser implantados.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. **Selecione Implementar o esquema** para implementar o esquema e, em seguida, depois de o esquema ser implantado, verifique se o alvo existe qualquer anomalia.

    ![Implementar o esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do Azure Database Migration Service. 

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e à base de dados SQL alvo ou sql Managed Instance.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

6. Selecione um nível de preços; para esta migração on-line, não se esqueça de selecionar o nível de preços Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

     ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo do servidor Fonte,** selecione **AWS RDS para O Servidor SQL,** na caixa de texto **do tipo servidor Target,** selecione **base de dados Azure SQL**.

    > [!NOTE]
    > Para o tipo de servidor target, selecione **Azure SQL Database** para migrar tanto para a Base de Dados SQL como para a SQL Managed Instance.

5. Na secção Escolha o **tipo de atividade,** selecione Migração de **dados Online.**

    > [!IMPORTANT]
    > Certifique-se de selecionar **migração de dados online;** as migrações offline não são apoiadas para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    ![Criar e Executar uma Atividade do Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação da instância do SQL Server de origem.

    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas usando um certificado auto-assinado não fornecem uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. **Selecione Guardar** e, em seguida, no ecrã de **detalhes do destino migratório,** especifique os detalhes da ligação para a base de dados-alvo em Azure.

    ![Selecionar o Destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Selecione **Guardar**, no ecrã **Selecionar tabelas**, expanda a lista de tabelas e reveja a lista dos campos afetados.

    O Serviço de Migração da Base de Dados Azure seleciona todas as tabelas de origem vazias existentes na base de dados-alvo. Se pretender remigrar as tabelas que já incluem dados, tem de selecionar explicitamente as tabelas deste ecrã.

    ![Selecionar tabelas](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. **Selecione Guardar**, depois de definir as **seguintes definições avançadas de migração on-line**.

    | Definições | Descrição |
    | ------------- | ------------- |
    | **Número máximo de tabelas para carregar em paralelo** | Especifica o número de tabelas que o DMS executa em paralelo durante a migração. O valor padrão é 5, mas pode ser definido como um valor ideal para atender às necessidades específicas de migração com base em quaisquer migrações de POC. |
    | **Quando a tabela de origem é truncada** | Especifica se o DMS trunca a tabela-alvo durante a migração. Esta definição pode ser útil se uma ou mais tabelas forem truncadas como parte do processo de migração. |
    | **Configurações de configuração para dados de objetos grandes (LOB)** | Especifica se o DMS migra dados LOB ilimitados ou limita os dados LOB migrados para um tamanho específico.  Quando há um limite nos dados lob migrados, quaisquer dados lob para além desse limite são truncados. Para migrações de produção, é recomendado selecionar **Permitir tamanho lob ilimitado** para evitar a perda de dados. Ao especificar para permitir o tamanho lob ilimitado, selecione os **dados do LoB migratório num único bloco quando o tamanho LOB for inferior ao (KB) especificado** caixa de verificação para melhorar o desempenho. |

    ![Definir definições avançadas de migração on-line](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

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

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados-alvo.

    ![Estado da Atividade - concluído](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre questões e limitações conhecidas na realização de migrações online para Azure, consulte o artigo [Questões conhecidas e soluções alternativas com migrações online.](known-issues-azure-sql-online.md)
* Para obter informações sobre o Serviço de Migração de Bases de Dados, consulte o artigo [O que é o Serviço de Migração da Base de Dados?](./dms-overview.md)
* Para obter informações sobre a SqL Database, consulte o artigo [O que é o serviço de Base de Dados SQL?](../azure-sql/database/sql-database-paas-overview.md)
* Para obter informações sobre as ocorrências geridas pela SQL, consulte o artigo [O que é sql Caso gerido](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).