---
title: 'Tutorial: Migrar o MySQL online para a Base de Dados Azure para o MySQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line do MySQL no local para a Azure Database for MySQL utilizando o Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 754d8cc9e79bc100e87f56c6fc33102963e53e8d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818069"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Migrar o MySQL para a Base de Dados do Azure para MySQL online com o DMS

Pode utilizar o Serviço de Migração da Base de Dados Azure para migrar as bases de dados de um caso MySQL no local para [a Base de Dados Azure para o MySQL](../mysql/index.yml) com tempo de inatividade mínimo. Por outras palavras, a migração pode ser feita com um período de indisponibilidade mínimo para a aplicação. Neste tutorial, migra a base de dados de amostras de **Empregados** de um caso no local do MySQL 5.7 para Azure Database for MySQL utilizando uma atividade de migração online no Azure Database Migration Service.

> [!IMPORTANT]
> O cenário de migração online "MySQL to Azure Database for MySQL" está a ser substituído por um cenário de migração offline paralelo e altamente performante a 1 de junho de 2021. Para migrações on-line, você pode usar esta nova oferta juntamente com [a replicação de dados.](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) Em alternativa, utilize ferramentas de código aberto como [MyDumper/MyLoader](https://centminmod.com/mydumper.html) com replicação de dados para migrações online. 

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Migre o esquema de exemplo com o utilitário mysqldump.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de um exemplo do Azure Database Migration Service na mesma região Azure que a base de dados-alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

> [!NOTE]
> Comunicação sem preconceitos
>
> A Microsoft suporta um ambiente diversificado e inclusão. Este artigo contém referências à palavra _escravo._ O guia de estilo da Microsoft [para comunicação sem preconceitos](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) reconhece isto como uma palavra de exclusão. A palavra é usada neste artigo para consistência porque atualmente é a palavra que aparece no software. Quando o software for atualizado para remover a palavra, este artigo será atualizado para estar em alinhamento.
>


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Transferir e instalar a [edição de comunidade do MySQL](https://dev.mysql.com/downloads/mysql/) 5.6 ou 5.7. A versão do MySQL no local tem de corresponder à versão da Base de Dados do Azure para MySQL. Por exemplo, o MySQL 5.6 só pode ser migrado para a Base de Dados do Azure para MySQL 5.6 e não pode ser atualizado para a versão 5.7. As migrações de ou para o MySQL 8.0 não são suportadas.
* [Criar uma instância na Base de Dados do Azure para MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Consulte o artigo Use a [bancada de trabalho MySQL para ligar e consultar dados](../mysql/connect-workbench.md) para obter detalhes sobre como conectar e criar uma base de dados utilizando a aplicação Workbench.  
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos servidores de origem no local, utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração virtual da redeNet, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > * Ponto final de armazenamento
    > * Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede de Rede Virtual não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados Azure aceda à fonte MySQL Server, que por padrão é a porta TCP 3306.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.
* Crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) ao nível do servidor para a Base de Dados Azure para o MySQL para permitir o acesso do Serviço de Migração da Base de Dados Azure às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* O MySQL de origem tem de estar na edição de comunidade do MySQL suportada. Para determinar a versão da instância do MySQL, no utilitário MySQL ou no MySQL Workbench, execute o seguinte comando:

    ```
    SELECT @@version;
    ```

* A Base de Dados do Azure para MySQL suporta apenas tabelas InnoDB. Para converter tabelas MyISAM para InnoDB, veja o artigo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Converter Tabelas de MyISAM para InnoDB)

* Ativar o registo binário no ficheiro my.ini (Windows) ou my.cnf (Unix) na base de dados de origem, utilizando a seguinte configuração:

  * **server_id** = 1 ou superior (relevante apenas para o MySQL 5.6)
  * **log-bin** = \<path> (relevante apenas para o MySQL 5.6)    Por exemplo: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (recomenda-se não utilizar zero; relevante apenas para o MySQL 5.6)
  * **Binlog_row_image** = full (relevante apenas para o MySQL 5.6)
  * **log_slave_updates** = 1

* O utilizador tem de ter a função ReplicationAdmin com os seguintes privilégios:

  * **CLIENTE DE REPLICAÇÃO** - necessário apenas para tarefas de Processamento de Alterações. Por outras palavras, as tarefas de apenas Carregamento completo não precisam deste privilégio.
  * **RÉPLICA DE REPLICAÇÃO** - necessário apenas para tarefas de Processamento de Alterações. Por outras palavras, as tarefas de apenas Carregamento completo não precisam deste privilégio.
  * **SUPER** - necessário apenas em versões anteriores ao MySQL 5.6.6.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Para concluir todos os objetos de base de dados, como esquemas de tabela, índices e procedimentos armazenados, é necessário extrair o esquema da base de dados de origem e aplicar à base de dados. Para extrair o esquema, pode utilizar mysqldump com o parâmetro `--no-data`.

Assumindo que tem a base de dados de amostras mySQL **Employees** no sistema de instalações, o comando para fazer a migração de esquemas usando o mysqldump é:

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

Se tiver chaves externas no seu esquema, o carregamento inicial e a sincronização contínua da migração irão falhar.  Execute o seguinte script na bancada mySQL workbench para extrair o script de chave estrangeira gota e adicionar script de chave estrangeira.

```sql
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

> [!NOTE]
> O Azure DMS não suporta a ação referencial CASCADE, que ajuda a eliminar ou atualizar automaticamente uma linha de correspondência na tabela infantil quando uma linha é eliminada ou atualizada na tabela dos pais. Para obter mais informações, na documentação do MySQL, consulte a secção Ações Referenciais do artigo [Restrições DE CHAVE ESTRANGEIRA](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> O Azure DMS requer que deixe cair os constrangimentos de chaves estrangeiras no servidor de base de dados-alvo durante a carga inicial de dados, e não pode utilizar ações referenciais. Se a sua carga de trabalho depender da atualização de uma tabela de crianças relacionada através desta ação referencial, recomendamos que efetue uma [lixeira e restaure.](../mysql/concepts-migrate-dump-restore.md) 


> [!IMPORTANT]
> Se importar dados utilizando uma cópia de segurança, remova os comandos CREATE DEFINER manualmente ou utilizando o comando --skip-definer quando efetuar uma mysqldump. O DEFINER requer super privilégios para criar e é restringido na Base de Dados Azure para o MySQL.

Se tiver gatilhos na base de dados, irá impor a integridade dos dados no alvo antes da migração completa de dados da fonte. A recomendação é desativar os gatilhos em todas as tabelas do alvo durante a migração e, em seguida, ativar os gatilhos após a migração.

Execute o seguinte script na bancada mySQL workbench na base de dados alvo para extrair o script do gatilho de gota e adicionar script de gatilho.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Executar a consulta gerada do gatilho de queda (coluna DropQuery) no resultado para lançar os gatilhos na base de dados alvo. A consulta de adição do gatilho pode ser guardada, para ser usada após a conclusão da migração de dados.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-mysql-to-azure-mysql-online/01-portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-mysql-to-azure-mysql-online/02-01-portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-mysql-to-azure-mysql-online/02-02-portal-register-resource-provider.png)

## <a name="create-a-database-migration-service-instance"></a>Criar uma instância do Serviço de Migração de Bases de Dados

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/03-dms-portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/04-dms-portal-marketplace-create.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione um nível de preços e mude para o ecrã de rede. A capacidade de migração offline está disponível tanto no nível de preços Standard como Premium.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar configurações básicas do Serviço de Migração da Base de Dados Azure](media/tutorial-mysql-to-azure-mysql-online/05-dms-portal-create-basic.png)

5. Selecione uma rede virtual existente da lista ou forneça o nome da nova rede virtual a ser criada. Mova-se para a revisão + crie ecrã. Opcionalmente, pode adicionar tags ao serviço utilizando o ecrã das etiquetas.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e à instância de base de dados Azure SQL.

    ![Configurar configurar as definições da rede de serviços de migração da base de dados da Azure](media/tutorial-mysql-to-azure-mysql-online/06-dms-portal-create-networking.png)

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

6. Reveja as configurações e **selecione Criar** para criar o serviço.
    
    ![Azure Database Migration Service criar](media/tutorial-mysql-to-azure-mysql-online/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.  

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-mysql-to-azure-mysql-online/08-01-dms-portal-search-service.png)

2. Selecione a sua instância de serviço de migração a partir dos resultados da pesquisa e selecione + **Novo Projeto de Migração.**
    
    ![Criar um novo projeto de migração](media/tutorial-mysql-to-azure-mysql-online/08-02-dms-portal-new-project.png)

3. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de seleção **do tipo de servidor Fonte,** selecione **MySQL,** na caixa de seleção **do tipo de servidor Target,** selecione **Azure Database For MySQL** e na caixa de seleção **do tipo de atividade de migração,** selecione **migração de dados online**. Selecione **Criar e executar atividade**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

## <a name="configure-migration-project"></a>Projeto de migração de configuração

1. No ecrã **de origem Select,** especifique os detalhes da ligação para a origem da instância MySQL e selecione **Seguinte : Selecione o>>**

    ![Adicionar ecrã de detalhes de origem](media/tutorial-mysql-to-azure-mysql-online/10-dms-portal-project-mysql-source.png)

2. No ecrã **alvo Select,** especifique os detalhes da ligação para a base de dados Azure alvo para a instância MySQL e selecione **Seguinte : Selecione bases de dados>>**

    ![Adicionar ecrã de detalhes do alvo](media/tutorial-mysql-to-azure-mysql-online/11-dms-portal-project-mysql-target.png)

3. No ecrã de **bases de dados Select,** mapear a fonte e a base de dados-alvo para migração e selecionar **Seguinte : Configurar as definições de migração>>**. Pode selecionar a opção **Make Source Server Readonly** para tornar a fonte apenas para leitura, mas tenha cuidado com a definição do nível do servidor. Se selecionado, define todo o servidor apenas para leitura, e não apenas para as bases de dados selecionadas.
    
    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.
    ![Selecione o ecrã de detalhes da base de dados](media/tutorial-mysql-to-azure-mysql-online/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
   > Embora possa selecionar várias bases de dados neste passo, cada instância do Serviço de Migração da Base de Dados Azure suporta até 4 bases de dados para migração simultânea. Além disso, existe um limite de 10 casos de Serviço de Migração de Bases de Dados Azure por subscrição por região. Por exemplo, se tiver 80 bases de dados para migrar, pode migrar 40 delas para a mesma região simultaneamente, mas apenas se tiver criado 10 instâncias do Serviço de Migração da Base de Dados Azure.

4. No ecrã de definições de **migração configurar,** selecione as tabelas para fazer parte da migração e selecione **Seguinte : Resumo>>**. Se as tabelas-alvo tiverem quaisquer dados, não são selecionadas por padrão, mas pode selecioná-los explicitamente e serão truncados antes de iniciar a migração.

    ![Selecione tela de tabelas](media/tutorial-mysql-to-azure-mysql-online/13-dms-portal-project-mysql-select-tbl.png)

5. No ecrã **Resumo,** na caixa de texto **'Nome de Atividade',** especifique um nome para a atividade de migração e reveja o resumo para garantir que os detalhes da origem e do alvo correspondem ao que especificou anteriormente.

    ![Resumo do projeto de migração](media/tutorial-mysql-to-azure-mysql-online/14-dms-portal-project-mysql-activity-summary.png)

6. Selecione **Migração iniciar**. É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**. O **Estado** muda para **Correr** quando as migrações de mesa começam.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade de migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

     ![Estado da Atividade - concluída](media/tutorial-mysql-to-azure-mysql-online/15-dms-activity-completed.png)

2. Em **Nome da Base de Dados**, selecione uma base de dados específica para obter o estado da migração para as operações **Carregamento de dados completo** e **Sincronização de dados incremental**.

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).

     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-mysql-to-azure-mysql-online/16-dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-mysql-to-azure-mysql-online/17-dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Iniciar transferência](media/tutorial-mysql-to-azure-mysql-online/18-dms-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado da migração de base de dados apresentar **Concluído**, ligue as suas aplicações à nova Base de Dados SQL do Azure de destino.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para MySQL, veja o artigo [Problemas conhecidos e soluções alternativas com migrações online da Base de Dados do Azure para MySQL](known-issues-azure-mysql-online.md).
* Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo [O que é o Serviço de Migração da Base de Dados Azure?](./dms-overview.md)
* Para obter informações sobre a Base de Dados Azure para o MySQL, consulte o artigo [O que é a Base de Dados Azure para o MySQL?](../mysql/overview.md)
