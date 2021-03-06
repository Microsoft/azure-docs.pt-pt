---
title: 'Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL online através do portal Azure'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line de PostgreSQL no local para a Base de Dados Azure para PostgreSQL utilizando o Azure Database Migration Service através do portal Azure.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: d28c45b2d0fc1a123f44020f42c4d2c59c593cb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709925"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Tutorial: Migrar PostgreSQL para Azure DB para PostgreSQL on-line usando DMS através do portal Azure

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância postgreSQL no local para [a Azure Database para PostgreSQL](../postgresql/index.yml) com tempo de inatividade mínimo para a aplicação. Neste tutorial, migra a base de dados de amostras **de DVD Rental** de um caso no local de PostgreSQL 9.6 para Azure Database for PostgreSQL utilizando a atividade de migração on-line no Azure Database Migration Service.

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

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Faça o download e instale [a edição comunitária postgreSQL](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 ou 10. A versão do Servidor PostgreSQL de origem deve ser 9.4, 9.5, 9.6, 10 ou 11. Para obter mais informações, consulte o artigo [Versões postgresQL suportadas na base de dados.](../postgresql/concepts-supported-versions.md)

    Note também que a base de dados Azure alvo para a versão PostgreSQL deve ser igual ou mais tarde à versão PostgreSQL no local. Por exemplo, o PostgreSQL 9.6 pode migrar para a Base de Dados Azure para PostgreSQL 9.6, 10 ou 11, mas não para Azure Database para PostgreSQL 9.5.

* [Crie uma base de dados Azure para servidor PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) ou [crie uma base de dados Azure para o servidor PostgreSQL - Hyperscale (Citus).](../postgresql/quickstart-create-hyperscale-portal.md)
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede (NSG) para a sua rede virtual não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao Servidor PostgreSQL de origem, que por padrão é a porta TCP 5432.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Crie uma regra de [firewall](../postgresql/concepts-firewall-rules.md) ao nível do servidor para a Base de Dados Azure para PostgreSQL para permitir que o Azure Database Migration Service aceda às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* Ative a replicação lógica no ficheiro postgresql.config e defina os seguintes parâmetros:

  * wal_level = **logical**
  * max_replication_slots = [número de slots], recomendar a definição para **cinco slots**
  * max_wal_senders = [número de tarefas simultâneas] – O parâmetro max_wal_senders define o número de tarefas simultâneas que podem ser executadas, definição recomendada de **10 tarefas**

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
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Para obter mais informações sobre como utilizar o utilitário pg_dump, veja os exemplos no tutorial [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Crie uma base de dados vazia no ambiente de destino, que é a Base de Dados do Azure para PostgreSQL.

    Para obter mais informações sobre como conectar e criar uma base de dados, consulte o artigo [Criar uma Base de Dados Azure para servidor PostgreSQL no portal Azure](../postgresql/quickstart-create-server-database-portal.md) ou Criar uma Base de [Dados Azure para postgresQL - Hiperescala (Citus) no portal Azure](../postgresql/quickstart-create-hyperscale-portal.md).

    > [!NOTE]
    > Um caso de Base de Dados Azure para PostgreSQL - Hiperescala (Citus) tem apenas uma única base de dados: **citus**.

3. Importe o esquema para a base de dados de destino que criou ao restaurar o ficheiro de captura de esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Por exemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
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

   ![Mostrar subscrições no portal](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. No ecrã do **Serviço de Migração Create,** especifique um nome, a subscrição, um grupo de recursos novo ou existente e a localização do serviço.

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Serviço de Migração da Base de Dados Azure o acesso ao servidor PostgreSQL de origem e à base de dados Azure alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

5. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Selecione **Review + criar** para criar o serviço.

   A criação de serviços estará concluída dentro de cerca de 10 a 15 minutos.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. No ecrã dos **Serviços de Migração da Base de Dados Azure,** procure o nome do serviço de migração da base de dados Azure que criou, selecione o caso e, em seguida, selecione + **Novo Projeto de Migração**.

3. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **PostgreSQL,** na caixa de texto **do tipo do servidor Target,** selecione **Azure Database for PostgreSQL**.

4. Na secção Escolha o **tipo de atividade,** selecione Migração de **dados Online.**

    ![Criar projeto de Serviço de Migração de Bases de Dados Azure](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

5. **Selecione Guardar**, note os requisitos para utilizar com sucesso o Serviço de Migração da Base de Dados Azure para migrar dados e, em seguida, selecione **Criar e executar a atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **'Adicionar Detalhes fonte',** especifique os detalhes da ligação para a origem da instância PostgreSQL.

    ![Ecrã Adicionar Detalhes da Origem](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de detalhes do **Target,** especifique os detalhes de ligação para o servidor target Hyperscale (Citus), que é a instância pré-a provisionada de Hyperscale (Citus) para a qual o esquema **de aluguer de DVD** foi implantado utilizando pg_dump.

    ![Ecrã Detalhes do destino](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    ![Mapa para o ecrã de bases de dados alvo](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **Selecione Guardar** e, em seguida, no ecrã **de definições de migração,** aceite os valores predefinidos.

    ![Tela de configurações de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Tela de resumo da migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela de atividade de migração aparece, e o **estado** da atividade deve atualizar para mostrar como **Backup em Progresso**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

     ![Monitorizar o processo de migração](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Quando a migração estiver concluída, em **Data de Dados,** selecione uma base de dados específica para chegar ao estado de migração para **operações de sincronização de dados completas** e **incrementais.**

   > [!NOTE]
   > **A carga completa de dados** mostra o estado inicial da migração da carga, enquanto o sync incremental de **dados** mostra o estado da captura de dados de alteração (CDC).

     ![Detalhes completos da carga de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Detalhes incrementais do sincronização de dados](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Aguarde até que o contador **de alterações pendentes** mostre **0** para garantir que todas as transações recebidas na base de dados de origem sejam interrompidas, selecione a caixa de verificação **Confirmar** e, em seguida, selecione **Apply**.

    ![Tela de corte completa](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Quando o estado de migração da base de dados mostrar **concluído,** [recrie as sequências](https://wiki.postgresql.org/wiki/Fixing_Sequences) (se aplicável) e ligue as suas aplicações à nova instância-alvo da Base de Dados Azure para PostgreSQL.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](./dms-overview.md).
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo [O que é a Base de Dados Azure para PostgreSQL?](../postgresql/overview.md)