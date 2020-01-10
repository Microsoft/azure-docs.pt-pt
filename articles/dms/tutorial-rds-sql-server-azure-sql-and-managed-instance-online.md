---
title: 'Tutorial: migrar o RDS SQL Server online para o banco de dados SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a executar uma migração online do RDS SQL Server para o banco de dados SQL do Azure ou instância gerenciada usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 52a6ee282e12f0ece5f16c1fa67c38f07f9d86e7
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751286"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: migrar RDS SQL Server para o banco de dados SQL do Azure ou uma instância gerenciada do banco de dados SQL do Azure online usando DMS
Você pode usar o serviço de migração de banco de dados do Azure para migrar os bancos de dados de uma instância do RDS SQL Server para o banco de dados [SQL do Azure](https://docs.microsoft.com/azure/sql-database/) ou uma [instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) com tempo de Neste tutorial, você migra o banco de dados **Adventureworks2012** restaurado para uma instância do RDS SQL Server do SQL Server 2012 (ou posterior) para o banco de dados SQL do Azure ou uma instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Crie uma instância do banco de dados SQL do Azure ou uma instância gerenciada do banco de dados SQL do Azure. 
> * Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.
> * Transferir um relatório da migração.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium. Para obter mais informações, consulte a página [preços](https://azure.microsoft.com/pricing/details/database-migration/) do serviço de migração de banco de dados do Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do RDS SQL Server para o banco de dados SQL do Azure ou uma instância gerenciada do banco de dados SQL do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

* Crie um [banco de dados RDS SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* Crie uma instância do banco de dados SQL do Azure, que você faz seguindo os detalhes no artigo [criar um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Se você estiver migrando para uma instância gerenciada do banco de dados SQL do Azure, siga os detalhes no artigo [criar uma instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)e, em seguida, crie um banco de dados vazio chamado **AdventureWorks2012**. 
 
* Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 ou posterior.
* Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager. Se você estiver migrando para uma instância gerenciada do banco de dados SQL do Azure, certifique-se de criar a instância DMS na mesma rede virtual usada para a instância gerenciada do banco de dados SQL do Azure, mas em uma sub-rede diferente.  Como alternativa, se você usar uma rede virtual diferente para DMS, precisará criar um emparelhamento de rede virtual entre as duas redes virtuais. Para obter mais informações sobre como criar uma rede virtual, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração de rede virtual, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > * Ponto de extremidade de armazenamento
    > * Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet. 

* Verifique se as regras do grupo de segurança de rede de rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abrir a firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
* Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Forneça o intervalo de sub-rede da rede virtual usada para o serviço de migração de banco de dados do Azure.
* Verifique se as credenciais usadas para conectar-se à instância do RDS SQL Server de origem estão associadas a uma conta que seja membro da função de servidor "processadmin" e um membro das funções de banco de dados "db_owner" em todos os bancos que devem ser migrados.
* Verifique se as credenciais usadas para se conectar à instância do banco de dados SQL do Azure de destino têm a permissão CONTROL DATABASE nos bancos de dados SQL do Azure de destino e um membro da função sysadmin se estiver migrando para uma instância gerenciada do banco de dados SQL do Azure.
* A versão de SQL Server do RDS de origem deve ser SQL Server 2012 e superior. Para determinar a versão que a instância do SQL Server está a executar, leia o artigo [Como determinar a versão, edição e nível de atualização do SQL Server e respetivos componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Habilite o CDC (captura de dados de alterações) no banco de dados RDS SQL Server e todas as tabelas de usuário selecionadas para migração.
    > [!NOTE]
    > Você pode usar o script abaixo para habilitar o CDC em um banco de dados RDS SQL Server.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Você pode usar o script abaixo para habilitar o CDC em todas as tabelas.
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
* Desative os acionadores de base de dados na Base de Dados SQL do Azure de destino.
    > [!NOTE]
    > Pode encontrar os acionadores de base de dados na Base de Dados SQL do Azure com a seguinte consulta:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Para obter mais informações, leia o artigo [DESATIVAR ACIONADOR (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Use o DMA para migrar o esquema para o banco de dados SQL do Azure.

> [!NOTE]
> Antes de poder criar um projeto de migração no DMA, confirme que já aprovisionou uma base de dados SQL do Azure, conforme mencionado nos pré-requisitos. Para fins deste tutorial, o nome do banco de dados SQL do Azure é considerado **AdventureWorks2012**, mas você pode fornecer qualquer nome que desejar.

Para migrar o esquema **AdventureWorks2012** para a Base de Dados SQL do Azure, realize os passos seguintes:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3. Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do DMA deverá ser apresentada, conforme mostrado na imagem abaixo:

    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Selecione **Create** (Criar) para criar o projeto.
5. No DMA, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Selecione **Avançar**, em **conectar ao servidor de destino**, especifique os detalhes da conexão de destino para o banco de dados SQL do Azure, selecione **conectar**e, em seguida, selecione o banco de dados **AdventureWorksAzure** que você previsionou no banco de dados SQL do Azure.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Selecione **Next** (Seguinte) para avançar para o ecrã **Select objects** (Selecionar objetos), no qual pode especificar os objetos do esquema na base de dados **AdventureWorks2012** que têm de ser implementados na Base de Dados SQL do Azure.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

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

    A rede virtual fornece ao serviço de migração de banco de dados do Azure acesso ao SQL Server de origem e à instância de destino do banco de dados SQL do Azure.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço; para esta migração online, certifique-se de selecionar o tipo de preço premium.

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
4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **AWS RDS para SQL Server**, na caixa de texto **tipo de servidor de destino** , selecione **banco de dados SQL do Azure**.

    > [!NOTE]
    > Para tipo de servidor de destino, selecione **banco de dados SQL do Azure** para migrar para um banco de dados singleton do banco de dados SQL do Azure e para uma instância gerenciada do banco de dados SQL do Azure.

5. Na seção **escolher tipo de atividade** , selecione **migração de dados online**.

    > [!IMPORTANT]
    > Certifique-se de selecionar **migração de dados online**; Não há suporte para migrações offline para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **criar projeto somente** para criar o projeto de migração agora e executar a migração mais tarde.

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

    O Azure Database Migration Service seleciona automaticamente todas as tabelas de origem vazias que existem na instância da Base de Dados SQL do Azure de destino. Se você quiser remigrar tabelas que já incluem dados, você precisa selecionar explicitamente as tabelas nesta tela.

    ![Selecionar tabelas](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Selecione **salvar**, depois de definir as seguintes **Configurações avançadas de migração online**.

    | Definição | Descrição |
    | ------------- | ------------- |
    | **Número máximo de tabelas a serem carregadas em paralelo** | Especifica o número de tabelas que o DMS executa em paralelo durante a migração. O valor padrão é 5, mas pode ser definido como um valor ideal para atender às necessidades de migração específicas com base em quaisquer migrações de POC. |
    | **Quando a tabela de origem está truncada** | Especifica se DMS trunca a tabela de destino durante a migração. Essa configuração pode ser útil se uma ou mais tabelas forem truncadas como parte do processo de migração. |
    | **Definir configurações para dados de LOB (objetos grandes)** | Especifica se DMS migra dados LOB ilimitados ou limita os dados LOB migrados para um tamanho específico.  Quando há um limite nos dados LOB migrados, todos os dados LOB além desse limite são truncados. Para migrações de produção, é recomendável selecionar **permitir tamanho de LOB ilimitado** para evitar a perda de dados. Ao especificar para permitir o tamanho ilimitado de LOB, marque a caixa de seleção **migrar dados LOB em um único bloco quando o tamanho do LOB for menor que (KB) especificado** para melhorar o desempenho. |

    ![Definir configurações avançadas de migração online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

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
4. Quando o estado da migração de base de dados apresentar **Concluído**, ligue as suas aplicações à nova Base de Dados SQL do Azure de destino.

    ![Estado da Atividade - concluído](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao executar migrações online para banco de dados SQL do Azure, consulte o artigo [problemas conhecidos e soluções alternativas com migrações online do banco de dados SQL do Azure](known-issues-azure-sql-online.md).
* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o banco de dados SQL do Azure, consulte o artigo o [que é o serviço de banco de dados SQL do Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Para obter informações sobre instâncias gerenciadas do banco de dados SQL do Azure, consulte a página [instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
