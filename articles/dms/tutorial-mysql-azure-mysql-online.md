---
title: 'Tutorial: Migrar mySQL on-line para Azure Database para MySQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line do MySQL no local para a Base de Dados Azure para mySQL utilizando o Serviço de Migração de Bases de Dados Azure.
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
ms.openlocfilehash: 7c8087a01bb71657e816be89b6a562dd4783b271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240745"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o MySQL para a Base de Dados do Azure para MySQL online com o DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar as bases de dados de uma instância MySQL no local para a Base de [Dados Azure para MySQL](https://docs.microsoft.com/azure/mysql/) com o mínimo de tempo de inatividade. Por outras palavras, a migração pode ser feita com um período de indisponibilidade mínimo para a aplicação. Neste tutorial, migra a base de dados de amostras **dos Colaboradores** de uma instância no local do MySQL 5.7 para a Base de Dados Azure para o MySQL utilizando uma atividade de migração on-line no Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migre o esquema de exemplo com o utilitário mysqldump.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Bases de Dados Azure na mesma região do Azure que a base de dados alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Transferir e instalar a [edição de comunidade do MySQL](https://dev.mysql.com/downloads/mysql/) 5.6 ou 5.7. A versão do MySQL no local tem de corresponder à versão da Base de Dados do Azure para MySQL. Por exemplo, o MySQL 5.6 só pode ser migrado para a Base de Dados do Azure para MySQL 5.6 e não pode ser atualizado para a versão 5.7.
* [Criar uma instância na Base de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal). Veja o artigo [Utilizar o MySQL Workbench para se ligar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-workbench) para obter detalhes sobre como ligar e criar uma base de dados com o portal do Azure.  
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtualNet, se utilizar o ExpressRoute com o peering de rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
    >
    > * Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)de segurança da rede .
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a sua firewall windows para permitir que o Serviço de Migração da Base de Dados Azure aceda à fonte MySQL Server, que por padrão é a porta TCP 3306.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração.
* Crie uma [regra](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de firewall ao nível do servidor para a Base de Dados Azure para o MySQL para permitir o acesso do Serviço de Migração de Bases de Dados Azure às bases de dados-alvo. Forneça a gama de subredes da rede virtual utilizada para o Serviço de Migração de Bases de Dados Azure.
* O MySQL de origem tem de estar na edição de comunidade do MySQL suportada. Para determinar a versão da instância do MySQL, no utilitário MySQL ou no MySQL Workbench, execute o seguinte comando:

    ```
    SELECT @@version;
    ```

* A Base de Dados do Azure para MySQL suporta apenas tabelas InnoDB. Para converter tabelas MyISAM para InnoDB, veja o artigo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Converter Tabelas de MyISAM para InnoDB)

* Ativar o registo binário no ficheiro my.ini (Windows) ou my.cnf (Unix) na base de dados de origem, utilizando a seguinte configuração:

  * **server_id** = 1 ou superior (relevante apenas para o MySQL 5.6)
  * > de via **de log-bin** = \<(relevante apenas para MySQL 5.6) Por exemplo: log-bin = E:\MySQL_logs\\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (recomenda-se não usar zero; relevante apenas para MySQL 5.6)
  * **Binlog_row_image** = full (relevante apenas para o MySQL 5.6)
  * **log_slave_updates** = 1

* O utilizador tem de ter a função ReplicationAdmin com os seguintes privilégios:

  * **CLIENTE DE REPLICAÇÃO** - necessário apenas para tarefas de Processamento de Alterações. Por outras palavras, as tarefas de apenas Carregamento completo não precisam deste privilégio.
  * **RÉPLICA DE REPLICAÇÃO** - necessário apenas para tarefas de Processamento de Alterações. Por outras palavras, as tarefas de apenas Carregamento completo não precisam deste privilégio.
  * **SUPER** - necessário apenas em versões anteriores ao MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados, é necessário extrair o esquema da base de dados de origem e aplicar à base de dados. Para extrair o esquema, pode utilizar mysqldump com o parâmetro `--no-data`.

Assumindo que tem a base de dados de amostras dos **Funcionários** MySQL no sistema no local, o comando para fazer migração de esquemas usando mysqldump é:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Por exemplo:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Para importar o esquema para a Base de Dados do Azure para MySQL de destino, execute o seguinte comando:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Por exemplo:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar.  Execute o seguinte script na Bancada de Trabalho MySQL para extrair o script de chave estrangeira drop e adicionar script chave estrangeira.

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
  AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
  GROUP BY SchemaName;
 ```

Execute o script de remoção de chave externa (que é a segunda coluna) no resultado da consulta para remover a chave externa.

> [!IMPORTANT]
> Se importar dados utilizando uma cópia de segurança, remova manualmente os comandos CREATE DEFINER ou utilizando o comando --skip-skip-definer ao executar um mysqldump. O DEFINER requer super privilégios para criar e é restringido na Base de Dados Azure para o MySQL.

Se tiver um gatilho nos dados (insira ou atualizar o gatilho), irá impor a integridade dos dados no alvo antes dos dados replicados a partir da fonte. A recomendação é desativar os acionadores em todas as tabelas no destino durante a migração e, em seguida, ativar os acionadores após a conclusão da migração.

Para desativar os gatilhos na base de dados do alvo, utilize o seguinte comando:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte SQL Server e à instância de base de dados Azure SQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

5. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

      ![Localize todas as instâncias do Serviço de Migração da Base de Dados Azure](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. No ecrã dos Serviços de Migração da Base de **Dados Azure,** procure o nome da instância do Serviço de Migração de Bases de Dados Azure que criou e, em seguida, selecione a instância.

     ![Localize a sua instância do Serviço de Migração de Bases de Dados Azure](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **MySQL**, na caixa de texto **Tipo de servidor de destino**, selecione **AzureDbForMySQL**.
5. Na secção **Escolher tipo de atividade**, selecione **Migração de dados online**

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternadamente, pode escolher o **projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**, tenha em atenção os requisitos para utilizar com êxito o DMS para migrar dados e, em seguida, selecione **Criar e executar atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Adicionar Detalhes da Origem**, especifique os detalhes da ligação para a instância do MySQL de origem.

    ![Ecrã Adicionar Detalhes da Origem](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar** e, no ecrã **Detalhes do destino**, especifique os detalhes de ligação do servidor da Base de Dados do Azure para MySQL de destino, que é a instância pré-aprovisionada da Base de Dados do Azure para MySQL na qual o esquema de **Colaboradores** foi implementado através do mysqldump.

    ![Ecrã Detalhes do destino](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    ![Mapear para as bases de dados de destino](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Embora possa selecionar várias bases de dados neste passo, cada instância do Serviço de Migração de Bases de Dados Azure suporta até quatro bases de dados para migração simultânea. Além disso, há um limite de dois casos do Serviço de Migração de Bases de Dados Azure por região numa subscrição. Por exemplo, se tiver 40 bases de dados para migrar, só pode migrar oito delas simultaneamente, e apenas se tiver criado dois casos de Serviço de Migração de Bases de Dados Azure.

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

    A janela da atividade migratória aparece, e o **Estado** da atividade está **a rubricar.**

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

     ![Estado da Atividade - concluída](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Em **Nome da Base de Dados**, selecione uma base de dados específica para obter o estado da migração para as operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).

     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Iniciar transferência](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado da migração de base de dados apresentar **Concluído**, ligue as suas aplicações à nova Base de Dados SQL do Azure de destino.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para MySQL, veja o artigo [Problemas conhecidos e soluções alternativas com migrações online da Base de Dados do Azure para MySQL](known-issues-azure-mysql-online.md).
* Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo O que é o Serviço de Migração da Base de [Dados Azure?](https://docs.microsoft.com/azure/dms/dms-overview)
* Para obter informações sobre a Base de Dados Azure para mySQL, consulte o artigo O que é a Base de [Dados Azure para mySQL?](https://docs.microsoft.com/azure/mysql/overview)
