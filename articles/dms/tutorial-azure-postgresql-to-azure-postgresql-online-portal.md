---
title: 'Tutorial: Migrar Azure DB para PostgreSQL a Azure DB para PostgreSQL online através do portal Azure'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de um Azure DB para PostgreSQL para outra Base de Dados Azure para PostgreSQL utilizando o Azure Database Migration Service através do portal Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: ef840abdfdb51e2472615ffabf0b49545b6fef3f
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938428"
---
# <a name="tutorial-migrate-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Tutorial: Migrar Azure DB para PostgreSQL - Servidor Único para Azure DB para PostgreSQL - Servidor Único online usando DMS através do portal Azure

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma [Base de Dados Azure para postgresQL - Instância de servidor único](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) para a mesma versão ou versão diferente da Base de Dados Azure para pós-SQL - Instância de servidor único ou base de dados Azure para PostgreSQL - Servidor Flexível com tempo de inatividade mínimo. Neste tutorial, migra a base de dados de **amostras de DVD Rental** de uma Base de Dados Azure para PostgreSQL v10 para Azure Database for PostgreSQL - Single Server utilizando a atividade de migração on-line no Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migrar o esquema da amostra utilizando o utilitário pg_dump.
> * Crie uma instância do Azure Database Migration Service.
> * Criar um projeto de migração no Azure Database Migration Service.
> * Executar a migração.
> * Monitorizar a migração.
> * Executar corte de migração.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium. Encriptamos o disco para evitar o roubo de dados durante o processo de migração

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de um exemplo do Azure Database Migration Service na mesma região Azure que a base de dados-alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

> [!IMPORTANT]
> A migração da Base de Dados de Azure para PostgreSQL é suportada para a versão 10 do PostgreSQL e posterior. Também pode utilizar este tutorial para migrar de uma Base de Dados Azure para o caso PostgreSQL para outra base de dados Azure para o caso PostgreSQL ou hiperescala (Citus).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Verifique [o estado dos cenários de migração suportados pelo Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status) para combinações de migrações e versões suportadas. 
* Uma base de dados Azure existente para a versão 10 [do PostgreSQL](https://docs.microsoft.com/azure/postgresql/) e posterior instância com a base **de dados de aluguer de DVD.** O Serviço de Migração da Base de Dados Azure não suporta a migração do Azure DB para postgreSQL 9.5 ou 9.6.

    Note também que a base de dados Azure alvo para a versão PostgreSQL deve ser igual ou mais tarde à versão PostgreSQL no local. Por exemplo, o PostgreSQL 10 pode migrar para a Base de Dados Azure para PostgreSQL 10, ou 11, mas não para a Base de Dados Azure para PostgreSQL 9.6.

* [Crie uma base de dados Azure para servidor PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou [crie uma base de dados Azure para o servidor PostgreSQL - Hyperscale (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal) como o servidor de base de dados-alvo para migrar dados para dentro.
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure. Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos quickstart com detalhes passo a passo.

* Certifique-se de que as regras do Grupo de Segurança da Rede (NSG) para a sua rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração da Base de Dados Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Crie uma regra de [firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para a base de dados Azure para a fonte PostgreSQL para permitir que o Serviço de Migração da Base de Dados de Azure aceda às bases de dados de origem. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* Crie uma regra de [firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para a base de dados Azure para o alvo PostgreSQL para permitir que o Serviço de Migração da Base de Dados de Azure aceda às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* [Ativar a replicação lógica](https://docs.microsoft.com/azure/postgresql/concepts-logical) no Azure DB para fonte PostgreSQL. 
* Desagreja os seguintes parâmetros do Servidor na Base de Dados Azure para o caso PostgreSQL sendo usado como fonte:

  * max_replication_slots = [número de slots], recomendar a definição para **dez slots**
  * max_wal_senders = [número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, definição recomendada de **10 tarefas**

> [!NOTE]
> Os parâmetros do servidor acima são estáticos e exigirão um reboot da sua Base de Dados Azure para a instância PostgreSQL para que eles produzam efeito. Para obter mais informações sobre os parâmetros do servidor de toggling, consulte [a Base de Dados de Azure Configurar para parâmetros do servidor PostgreSQL](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal).

> [!IMPORTANT]
> Todas as tabelas na base de dados existente precisam de uma chave primária para garantir que as alterações podem ser sincronizadas na base de dados-alvo.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados, é necessário extrair o esquema da base de dados de origem e aplicar à base de dados.

1. Utilize o comando pg_dump -s para criar um ficheiro de captura de esquema para uma base de dados.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por exemplo, para criar um ficheiro de despejo de esquema para a base **de dados dvdrental:**

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como utilizar o utilitário pg_dump, veja os exemplos no tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie uma base de dados vazia no ambiente de destino, que é a Base de Dados do Azure para PostgreSQL.

    Para obter mais informações sobre como conectar e criar uma base de dados, consulte o artigo [Criar uma Base de Dados Azure para servidor PostgreSQL no portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) ou Criar uma Base de [Dados Azure para postgresQL - Hiperescala (Citus) no portal Azure](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > Um caso de Base de Dados Azure para PostgreSQL - Hiperescala (Citus) tem apenas uma única base de dados: **citus**.

3. Importe o esquema para a base de dados de destino que criou ao restaurar o ficheiro de captura de esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Para extrair o script de chave estrangeira gota e adicioná-lo no destino (Azure Database for PostgreSQL), em PgAdmin ou em psql, executar o seguinte script.

   > [!IMPORTANT]
   > As teclas estranhas no seu esquema farão com que a carga inicial e a sincronização contínua da migração falhem.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta.

6. Para desativar os gatilhos na base de dados alvo, execute o script abaixo.

   > [!IMPORTANT]
   > Os gatilhos (inserir ou atualizar) nos dados impõem a integridade dos dados no alvo antes de os dados serem replicados a partir da fonte. Como resultado, recomenda-se que desative os gatilhos em todas as tabelas **do alvo** durante a migração e, em seguida, reativar os gatilhos após a migração estar completa.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. No ecrã do **Serviço de Migração Create,** especifique um nome, a subscrição, um grupo de recursos novo ou existente e a localização do serviço.

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Serviço de Migração da Base de Dados Azure o acesso ao servidor PostgreSQL de origem e à base de dados Azure alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](https://aka.ms/DMSVnet).

5. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **Review + criar** para criar o serviço.

   A criação de serviços estará concluída dentro de cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. No ecrã dos **Serviços de Migração da Base de Dados Azure,** procure o nome do serviço de migração da base de dados Azure que criou, selecione o caso e, em seguida, selecione + **Novo Projeto de Migração**.

3. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **PostgreSQL,** na caixa de texto **do tipo do servidor Target,** selecione **Azure Database for PostgreSQL**.
    > [!NOTE]
    > Escolha **PostgreSQL** no **tipo de servidor Source,** mesmo que o servidor de origem seja uma base de dados Azure para a instância **PostgreSQL.**  

4. Na secção Escolha o **tipo de atividade,** selecione Migração de **dados Online.**

    ![Criar projeto de Serviço de Migração de Bases de Dados Azure](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

5. **Selecione Guardar**, note os requisitos para utilizar com sucesso o Serviço de Migração da Base de Dados Azure para migrar dados e, em seguida, selecione **Criar e executar a atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **'Adicionar Detalhes fonte',** especifique os detalhes da ligação para a origem da instância PostgreSQL.

    ![Ecrã Adicionar Detalhes da Origem](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Pode encontrar detalhes como "Nome do servidor", "porta do servidor", "nome da base de dados", etc. na Base de Dados Azure para o portal **PostgreSQL.**

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de detalhes do **Target,** especifique os detalhes de ligação para o servidor target Hyperscale (Citus), que é a instância pré-a provisionada de Hyperscale (Citus) para a qual o esquema **de aluguer de DVD** foi implantado utilizando pg_dump.

    ![Ecrã Detalhes do destino](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Pode migrar de uma base de dados Azure para o caso PostgreSQL para outra Base de Dados Azure para a instância de um único servidor pós-SQL ou para um servidor de Hiperescala (Citus).

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    ![Mapa para o ecrã de bases de dados alvo](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **Selecione Guardar**e, em seguida, no ecrã **de definições de migração,** aceite os valores predefinidos.

    ![Tela de configurações de migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Tela de resumo da migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela de atividade de migração aparece, e o **estado** da atividade deve atualizar para mostrar como **Backup em Progresso**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

     ![Monitorizar o processo de migração](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração estiver concluída, em **Data de Dados,** selecione uma base de dados específica para chegar ao estado de migração para **operações de sincronização de dados completas** e **incrementais.**

   > [!NOTE]
   > **A carga completa de dados** mostra o estado inicial da migração da carga, enquanto o sync incremental de **dados** mostra o estado da captura de dados de alteração (CDC).

     ![Detalhes completos da carga de dados](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes incrementais do sincronização de dados](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Aguarde até que o contador **de alterações pendentes** mostre **0** para garantir que todas as transações recebidas na base de dados de origem sejam interrompidas, selecione a caixa de verificação **Confirmar** e, em seguida, selecione **Apply**.

    ![Tela de corte completa](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o estado de migração da base de dados mostrar **concluído,** [recrie as sequências](https://wiki.postgresql.org/wiki/Fixing_Sequences) (se aplicável) e ligue as suas aplicações à nova instância-alvo da Base de Dados Azure para PostgreSQL.
 
> [!NOTE]
> O Serviço de Migração da Base de Dados Azure pode ser utilizado para realizar atualizações de grandes versões com tempo de inatividade reduzido na Base de Dados Azure para PostgreSQL - Servidor Único. Primeiro configura uma base de dados-alvo com a versão postgreSQL, configurações de rede e parâmetros mais elevados. Em seguida, pode iniciar a migração para as bases de dados-alvo utilizando o procedimento acima explicado. Depois de cortar para o servidor de base de dados alvo, pode atualizar a cadeia de ligação da aplicação para apontar para o servidor de base de dados alvo. 

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo [O que é a Base de Dados Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)
