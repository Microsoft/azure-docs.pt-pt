---
title: 'Tutorial: Utilizar o serviço de migração de base de dados do Azure para uma migração online do RDS MySQL para a base de dados do Azure para MySQL | Documentos da Microsoft'
description: Aprenda a efetuar uma migração online do RDS MySQL à base de dados do Azure para MySQL utilizando o serviço de migração de base de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/30/2019
ms.openlocfilehash: 3f5a69454356d16fd6f3c5dddb063fcf8e450014
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943661"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o RDS MySQL para o banco de dados do Azure para MySQL online com o DMS

Pode utilizar o serviço de migração de base de dados do Azure para migrar bases de dados a partir de uma instância de RDS MySQL para [base de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) enquanto a base de dados de origem permanece online durante a migração. Em outras palavras, a migração pode ser obtida com o período de indisponibilidade mínimo para a aplicação. Neste tutorial, migra os **funcionários** de dados de exemplo de uma instância do RDS MySQL para a base de dados do Azure para MySQL através da atividade de migração online no serviço de migração de base de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migre o esquema de exemplo, utilizando os utilitários mysqldump e mysql.
> * Crie uma instância do serviço de migração de base de dados do Azure.
> * Crie um projeto de migração com o Azure Database Migration Service.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium. Para obter mais informações, veja o Azure Database Migration Service [preços](https://azure.microsoft.com/pricing/details/database-migration/) página.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como efetuar uma migração online de uma instância do RDS MySQL para a base de dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Certifique-se de que o servidor MySQL de origem está a executar uma edição de Comunidade do MySQL suportada. Para determinar a versão da sua instância do MySQL, o utilitário mysql ou o MySQL Workbench, execute o comando:

    ```
    SELECT @@version;
    ```

    Para obter mais informações, consulte o artigo [suportado base de dados Azure para MySQL versões](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Transferir e instalar o [MySQL **funcionários** base de dados de exemplo](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Criar uma instância do [base de dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Criar uma rede Virtual do Azure (VNet) para o Azure Database Migration Service com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Certifique-se de que as regras do grupo de segurança de rede de VNet não bloqueiam as seguintes portas de comunicação de entrada para o Azure Database Migration Service: 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do Azure VNet NSG, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o serviço de migração de base de dados do Azure aceder ao servidor MySQL de origem, que, por predefinição, é a porta TCP 3306.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
* Criar ao nível do servidor [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para a base de dados do Azure para o servidor MySQL permitir o acesso de serviço de migração de base de dados do Azure para as bases de dados de destino. Forneça o intervalo de sub-rede da VNet utilizada para o Azure Database Migration Service.

> [!NOTE]
> Base de dados do Azure para MySQL só suporta InnoDB tabelas. Para converter tabelas MyISAM para InnoDB, consulte o artigo [converter tabelas de MyISAM para InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) .

### <a name="set-up-aws-rds-mysql-for-replication"></a>Configurar o AWS RDS MySQL para replicação

1. Para criar um novo grupo de parâmetro, siga as instruções fornecidas pelo AWS no artigo [ficheiros de registo de base de dados MySQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html), na **formato de registo binário** secção.
2. Crie um novo grupo de parâmetro com a seguinte configuração:
    * binlog_format = row
    * binlog_checksum = NONE
3. Guarde o novo grupo de parâmetro.

## <a name="migrate-the-schema"></a>Migrar o esquema

1. Extraia o esquema da base de dados de origem e aplicam-se para a base de dados de destino para concluir a migração de todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados.

    A maneira mais fácil de migrar apenas o esquema é utilizar mysqldump com o-- parâmetro sem dados. O comando para migrar o esquema é:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Por exemplo, para Despejar um ficheiro de esquema para o **funcionários** bases de dados, utilize o seguinte comando:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importe o esquema para o serviço de destino, o que é a base de dados do Azure para MySQL. Para restaurar o ficheiro de informação de esquema, execute o seguinte comando:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Por exemplo, para importar o esquema para o **funcionários** base de dados:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar. Para extrair o script drop de chave externa e adicionar o script de chave estrangeira no destino (base de dados do Azure para MySQL), execute o seguinte script no Workbench do MySQL:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
        KCU.TABLE_NAME,
        KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Execute a chave estrangeira de soltar (que é a segunda coluna) no resultado da consulta para remover a chave estrangeira.

5. Se tiver acionadores (insert ou update acionar) nos dados, ele irá impor a integridade dos dados no destino antes de replicar os dados da origem de. A recomendação é desabilitar disparadores em todas as tabelas *no destino* durante a migração e, em seguida, habilite os disparadores após a migração concluída.

    Para desabilitar disparadores no banco de dados de destino:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se existirem instâncias do tipo de dados ' ENUM ' em todas as tabelas, recomendamos que Atualize temporariamente para o tipo de dados 'variados de caractere' na tabela de destino. Quando a replicação de dados estiver concluída, em seguida, reverta o tipo de dados para ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do serviço de migração de base de dados do Azure e, em seguida, selecione **fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do serviço de migração de base de dados do Azure

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do serviço de migração de base de dados do Azure.

5. Selecione uma VNet já existente ou crie um novo.

    A VNet fornece o Azure Database Migration Service com acesso à instância do MySQL de origem e a base de dados do Azure de destino para a instância do MySQL.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço; para essa migração online, certifique-se de que selecionar o Premium: escalão de preço 4vCores.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing). Se precisar de ajuda para escolher o certo Azure Database Migration Service escalão de preço, consulte as recomendações apresentadas no lançamento [aqui](https://go.microsoft.com/fwlink/?linkid=861067).

     ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

     ![Localizar a instância do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **MySQL**e, em seguida, no **destino tipo de servidor** caixa de texto, selecione **AzureDbForMySQL**.
5. Na **escolha o tipo de atividade** secção, selecione **migração de dados Online**.

    > [!IMPORTANT]
    > Verifique se seleciona **migração de dados Online**; offline as migrações não são suportadas para este cenário.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Em alternativa, pode escolher **criar projeto apenas** para criar o projeto de migração e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    > [!NOTE]
    > Tome nota dos pré-requisitos necessários para configurar a migração online no painel de criação do projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* Sobre o **detalhe da origem de migração** ecrã, especifique os detalhes de ligação para a instância do MySQL de origem.

   ![Detalhes da origem](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **salvar**e, em seguida, no **detalhes de destino** ecrã, especifique os detalhes de ligação para a base de dados do Azure de destino para o servidor MySQL, o que é previamente aprovisionado e tem o **funcionários** esquema implementado utilizar MySQLDump.

    ![Selecionar o Destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados de destino contém o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

    ![Estado da atividade - em execução](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Sob **nome da base de dados**, selecione uma base de dados específica para obter o estado de migração para **carregamento de dados completo** e **sincronização de dados incrementais** operações.

    **Carregamento de dados completos** mostra o estado de migração de carregamento inicial, enquanto **sincronização de dados incrementais** mostra alterar o estado de captura (CDC) de dados.

    ![Carregamento de dados completos de ecrã do inventário-](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Ecrã de inventário - sincronização de dados incremental](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a carga completa inicial, as bases de dados são marcados **pronto para Cutover**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Recomeçar cortar](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado de migração de base de dados é apresentado **concluído**, conectar seus aplicativos para o destino nova base de dados do Azure para MySQL.

A migração online de uma instância no local do MySQL para a base de dados do Azure para MySQL está agora concluída.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre a base de dados do Azure para MySQL, consulte o artigo [o que é a base de dados do Azure para MySQL?](https://docs.microsoft.com/azure/mysql/overview).
* Para outras perguntas, envie um e-mail a [faça migrações de base de dados do Azure](mailto:AskAzureDatabaseMigrations@service.microsoft.com) alias.
