---
title: 'Tutorial: Migrar RDS PostgreSQL on-line para A Base de Dados Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de RDS PostgreSQL para Azure Database for PostgreSQL utilizando o Serviço de Migração de Bases de Dados Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/17/2020
ms.openlocfilehash: 33d39be071591071d88bb5a95c3cfcbb458d85a8
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255713"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Tutorial: Migrar RDS PostgreSQL para Azure DB para PostgreSQL online usando DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar bases de dados de uma instância RDS PostgreSQL para a Base de [Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) enquanto a base de dados de origem permanece online durante a migração. Por outras palavras, a migração pode ser alcançada com o mínimo de tempo de paragem para a aplicação. Neste tutorial, você migra a base de dados de amostras de aluguer de **DVD** de uma instância de RDS PostgreSQL 9.6 para Azure Database for PostgreSQL, utilizando a atividade de migração on-line no Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra utilizando o utilitário pg_dump.
> * Crie uma instância do Serviço de Migração de Bases de Dados Azure.
> * Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.
> * Executar a redução da migração.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium. Para mais informações, consulte a página de preços do Serviço de [Migração](https://azure.microsoft.com/pricing/details/database-migration/) da Base de Dados Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração on-line de uma instância no local de PostgreSQL para Azure Database para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Descarregue e instale a [edição comunitária PostgreSQL](https://www.postgresql.org/download/) 9.5, 9.6 ou 10. A versão de origem PostgreSQL Server deve ser 9.5.11, 9.6.7, 10 ou mais tarde. Para mais informações, consulte o artigo [Versões de Base de Dados PostgreSQL suportadas](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Além disso, a versão RDS PostgreSQL deve coincidir com a base de dados Azure para a versão PostgreSQL. Por exemplo, RDS PostgreSQL 9.5.11.5 só pode migrar para a Base de Dados Azure para PostgreSQL 9.5.11 e não para a versão 9.6.7.

* Crie uma instância de Base de [Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou [Azure Database para PostgreSQL - Hyperscale (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal). Consulte esta [secção](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) do documento para obter detalhes sobre como ligar-se ao Servidor PostgreSQL utilizando a pgAdmin.
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.
* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445 e 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a sua firewall windows para permitir que o Serviço de Migração da Base de Dados Azure aceda ao servidor de origem PostgreSQL, que por padrão é a porta TCP 5432.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma [regra](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de firewall ao nível do servidor para a Base de Dados Azure para o servidor PostgreSQL para permitir o acesso do Serviço de Migração de Bases de Dados Azure às bases de dados de destino. Forneça a gama de subredes da rede virtual utilizada para o Serviço de Migração de Bases de Dados Azure.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Configurar AWS RDS PostgreSQL para replicação

1. Para criar um novo grupo de parâmetros, siga as instruções fornecidas pela AWS no artigo [Trabalhar com grupos](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)de parâmetros DB .
2. Utilize o nome principal do utilizador para se ligar à fonte do Serviço de Migração de Bases de Dados Azure. Se utilizar uma conta diferente da conta principal do utilizador, a conta deve ter a função rds_superuser e a rds_replication. O papel rds_replication concede permissões para gerir slots lógicos e transmitir dados usando ranhuras lógicas.
3. Criar um novo grupo de parâmetros com a seguinte configuração:

    a. Defina o parâmetro rds.logical_replication no seu grupo de parâmetros DB para 1.

    b. max_wal_senders =[número de tarefas simultâneas] - O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, recomenda10 tarefas.

    c. max_replication_slots – = [número de slots], recomende o conjunto de cinco slots.

4. Associe o grupo de parâmetros que criou à instância RDS PostgreSQL.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extrair o esquema da base de dados de origem e aplicar-se à base de dados-alvo para completar a migração de todos os objetos de base de dados, tais como squemas de mesa, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é usar pg_dump com a opção -s. Para mais informações, consulte os [exemplos](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) no tutor ia do Pg_dump Postgres.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para despejar um ficheiro schema para a base de dados de aluguer de **DVD,** utilize o seguinte comando:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Crie uma base de dados vazia no serviço alvo, que é a Base de Dados Azure para PostgreSQL. Para ligar e criar uma base de dados, consulte um dos seguintes artigos:

    * [Criar uma Base de Dados Azure para servidor PostgreSQL utilizando o portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Criar uma Base de Dados Azure para o servidor PostgreSQL - Hyperscale (Citus) utilizando o portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Importar o esquema para o serviço alvo, que é a Base de Dados Azure para postgreSQL. Para restaurar o ficheiro de despejo de esquemas, execute o seguinte comando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script de chave estrangeira drop e adicionar script chave estrangeira no destino (Base de Dados Azure para PostgreSQL), executar o seguinte script em PgAdmin ou em psql:
  
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

5. Executar a chave estrangeira drop (que é a segunda coluna) no resultado da consulta para deixar cair a chave estrangeira.

6. Se tiver gatilhos (inserção ou gatilho de atualização) nos dados, irá impor a integridade dos dados no alvo antes de replicar dados a partir da fonte. A recomendação é desativar os gatilhos em todas as tabelas *do alvo* durante a migração e, em seguida, ativar os gatilhos após a migração estar completa.

    Para desativar os gatilhos na base de dados do alvo:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Bases de Dados Azure

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração de Bases de Dados Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à instância de origem PostgreSQL e à base de dados azure-alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

6. Selecione um nível de preços; para esta migração online, certifique-se de selecionar o nível de preços Premium: 4vCores.

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. No ecrã **azure Database Migration Services,** procure o nome da instância do Serviço de Migração da Base de Dados Azure que criou, selecione a instância e, em seguida, selecione + **Novo Projeto de Migração**.
3. No ecrã do **novo projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **AWS RDS para PostgreSQL,** e depois na caixa de texto **do tipo servidor Target,** selecione Base de **Dados Azure para PostgreSQL**.
4. Na secção **Escolha tipo de atividade,** selecione **migração de dados online**.

    > [!IMPORTANT]
    > Certifique-se de selecionar a **migração de dados online;** as migrações offline não são suportadas para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternadamente, pode escolher o **projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

5. Selecione **Guardar**.

6. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Por favor, tome nota dos pré-requisitos necessários para a criação de migração on-line na lâmina de criação do projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* No ecrã **Adicionar Detalhes de Origem,** especifique os detalhes de ligação para a instância de origem PostgreSQL.

   ![Detalhes da origem](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar**, e depois no ecrã de dados do **Target,** especifique os detalhes de ligação para o target Azure Database para o servidor PostgreSQL, que é pré-provisionado e tem o esquema de **Alugueres** de DVD implementado usando pg_dump.

    ![Detalhes do alvo](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Estado de Atividade - execução](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Em NOME DE BASE DE **DADOS,** selecione uma base de dados específica para chegar ao estado de migração para **operações completas** de carga de dados e **sincronização de dados incrementais.**

    **A carga completa de dados** mostra o estado inicial de migração da carga, enquanto a **sincronização** de dados incremental mostra o estado de captura de dados de alteração (CDC).

    ![Tela de inventário - carga completa de dados](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Tela de inventário - sincronização incremental de dados](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Após a carga completa inicial ser concluída, as bases de dados estão marcadas **Pronta seleção pronta a cortar**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Aguarde até que o contador **de alterações pendente** mostre **0** para garantir que todas as transações de entrada na base de dados de origem estão paradas, selecione a caixa de verificação **Confirmar** e, em seguida, selecione **Aplicar**.

    ![Tela completa de cutover](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados azure-alvo para a base de dados PostgreSQL.

A sua migração on-line de uma instância no local de RDS PostgreSQL para Azure Database para PostgreSQL está agora completa.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo O que é a Base de [Dados Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)
* Para outras questões, envie um e-mail para o pseudónimo [Ask Azure Database Migrations.](mailto:AskAzureDatabaseMigrations@service.microsoft.com)
