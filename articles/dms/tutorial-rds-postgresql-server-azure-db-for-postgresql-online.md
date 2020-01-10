---
title: 'Tutorial: migrar o RDS PostgreSQL online para o banco de dados do Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba como executar uma migração online do RDS PostgreSQL para o banco de dados do Azure para PostgreSQL usando o serviço de migração de banco de dados do Azure.
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
ms.openlocfilehash: 0930afeb02c79c9b3cf1da791e8cc5cda83c2820
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751263"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Tutorial: migrar o RDS PostgreSQL para o banco de dados do Azure para PostgreSQL online usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para migrar bancos de dados de uma instância do do RDS do Azure para [PostgreSQL](https://docs.microsoft.com/azure/postgresql/) , enquanto o banco de dados de origem permanece online durante a migração. Em outras palavras, a migração pode ser obtida com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você migra o banco de dados de exemplo de **aluguel de DVD** de uma instância do RDS PostgreSQL 9,6 para o banco de dados do Azure para PostgreSQL usando a atividade de migração online no serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migre o esquema de exemplo usando o utilitário pg_dump.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium. Para obter mais informações, consulte a página [preços](https://azure.microsoft.com/pricing/details/database-migration/) do serviço de migração de banco de dados do Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como executar uma migração online de uma instância local do PostgreSQL para o banco de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Baixe e instale o [PostgreSQL Community edition](https://www.postgresql.org/download/) 9,5, 9,6 ou 10. A versão do servidor PostgreSQL de origem deve ser 9.5.11, 9.6.7, 10 ou posterior. Para obter mais informações, consulte o artigo [versões de banco de dados PostgreSQL com suporte](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Além disso, a versão do RDS PostgreSQL deve corresponder à versão do banco de dados do Azure para PostgreSQL. Por exemplo, o RDS PostgreSQL 9.5.11.5 só pode migrar para o banco de dados do Azure para PostgreSQL 9.5.11 e não para a versão 9.6.7.

* Crie uma instância do [banco de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal). Consulte esta [seção](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) do documento para obter detalhes sobre como se conectar ao servidor PostgreSQL usando o pgAdmin.
* Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma rede virtual, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.
* Certifique-se de que suas regras de grupo de segurança de rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o servidor do banco de dados do Azure para PostgreSQL para permitir o acesso ao serviço de migração de banco de dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-rede da rede virtual usada para o serviço de migração de banco de dados do Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Configurar o AWS RDS PostgreSQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas por AWS no artigo [trabalhando com grupos de parâmetros de BD](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Use o nome de usuário mestre para se conectar à origem do serviço de migração de banco de dados do Azure. Se você usar uma conta que não seja a conta de usuário mestre, a conta deverá ter a função rds_superuser e a função rds_replication. A função rds_replication concede permissões para gerenciar Slots lógicos e transmitir dados usando Slots lógicos.
3. Crie um novo grupo de parâmetros com a seguinte configuração: a. Defina o parâmetro RDS. logical_replication em seu grupo de parâmetros do BD como 1.
    b. max_wal_senders = [número de tarefas simultâneas]-o parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, recomendamos 10 tarefas.
    c. max_replication_slots – = [número de Slots], recomendado definido como cinco slots.
4. Associe o grupo de parâmetros que você criou à instância do RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extraia o esquema do banco de dados de origem e aplique-o ao banco de dados de destino para concluir a migração de todos os objetos de banco de dados, como esquemas de tabela, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é usar pg_dump com a opção-s. Para obter mais informações, consulte os [exemplos](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) no tutorial de Pg_dump do Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para despejar um arquivo de esquema para o banco de dados **dvdrental** , use o seguinte comando:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Crie um banco de dados vazio no serviço de destino, que é o banco de dados do Azure para PostgreSQL. Para se conectar e criar um banco de dados, consulte um dos seguintes artigos:

    * [Criar um banco de dados do Azure para o servidor PostgreSQL usando o portal do Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Criar uma Base de Dados do Azure para PostgreSQL com a CLI do Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-azure-cli)

3. Importe o esquema para o serviço de destino, que é o banco de dados do Azure para PostgreSQL. Para restaurar o arquivo de despejo de esquema, execute o seguinte comando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script drop Foreign Key e adicionar o script Foreign Key no destino (banco de dados do Azure para PostgreSQL), execute o seguinte script em PgAdmin ou em psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Execute a chave de remoção estrangeira (que é a segunda coluna) no resultado da consulta para descartar a chave estrangeira.

6. Se você tiver gatilhos (inserir ou atualizar gatilho) nos dados, ele imporá a integridade dos dados no destino antes de replicar os dados da origem. A recomendação é desabilitar os gatilhos em todas as tabelas *no destino durante a* migração e, em seguida, habilitar os gatilhos após a conclusão da migração.

    Para desabilitar gatilhos no banco de dados de destino:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do serviço de migração de banco de dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione o local no qual você deseja criar a instância do serviço de migração de banco de dados do Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece o serviço de migração de banco de dados do Azure com acesso à instância PostgreSQL de origem e à instância de destino do banco de dados do Azure para PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um tipo de preço; para esta migração online, certifique-se de selecionar o tipo de preço premium: 4vCores.

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings4.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **AWS RDS para PostgreSQL**e, em seguida, na caixa de texto **tipo de servidor de destino** , selecione **banco de dados do Azure para PostgreSQL**.
5. Na seção **escolher tipo de atividade** , selecione **migração de dados online**.

    > [!IMPORTANT]
    > Certifique-se de selecionar **migração de dados online**; Não há suporte para migrações offline para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **criar projeto somente** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Anote os pré-requisitos necessários para configurar a migração online na folha de criação do projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* Na tela **detalhes da fonte de migração** , especifique os detalhes de conexão para a instância PostgreSQL de origem.

   ![Detalhes da origem](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details4.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **salvar**e, em seguida, na tela **detalhes de destino** , especifique os detalhes de conexão do banco de dados do Azure de destino para o servidor PostgreSQL, que é previamente provisionado e tem o esquema de **locações de DVD** implantado usando pg_dump.

    ![Selecionar o Destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-select-target4.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o banco de dados de destino contém o mesmo nome do banco de dados de origem, o serviço de migração de banco de dados do Azure seleciona o banco de dados de destino por padrão

    ![Mapear para as bases de dados de destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-targets-activity5.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Status da atividade-em execução](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Em **nome do banco**de dados, selecione um banco de dado específico para obter o status de migração para operações de sincronização de dados **incrementais** e **carregamento completo** .

    O **carregamento de dados completo** mostra o status de migração de carga inicial, enquanto a **sincronização de dados incremental** mostra o status da captura de dados de alteração (CDC).

    ![Tela de inventário-carregamento de dados completo](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Tela de inventário-sincronização de dados incremental](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Após a conclusão da carga inicial completa, os bancos de dados são marcados **como prontos para a transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Começar a cortar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o status da migração de banco de dados mostrar **concluído**, conecte seus aplicativos ao novo banco de dados de destino do banco de dados do Azure para PostgreSQL.

A migração online de uma instância local do PostgreSQL para o banco de dados do Azure para PostgreSQL agora está concluída.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o banco de dados do Azure para PostgreSQL, consulte o artigo [o que é o banco de dados do Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Para outras perguntas, envie um email para o alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) .
