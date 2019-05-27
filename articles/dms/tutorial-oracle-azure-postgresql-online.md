---
title: 'Tutorial: Utilizar o serviço de migração de base de dados do Azure para efetuar uma migração online do Oracle para a base de dados do Azure para PostgreSQL | Documentos da Microsoft'
description: Aprenda a efetuar uma migração online do Oracle no local ou em máquinas virtuais para a base de dados do Azure para PostgreSQL com o Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243805"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Migrar Oracle para o banco de dados do Azure para PostgreSQL online com o DMS (pré-visualização)

Pode utilizar o serviço de migração de base de dados do Azure para migrar as bases de dados Oracle bases de dados alojadas no local ou em máquinas virtuais para [base de dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) com período de indisponibilidade mínimo. Em outras palavras, pode concluir a migração com período de indisponibilidade mínimo para a aplicação. Neste tutorial, migra os **RH** base de dados de exemplo de uma local ou instância de máquina virtual do Oracle 11g à base de dados do Azure para PostgreSQL através da atividade de migração online no serviço de migração de base de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Avalie o esforço de migração usando a ferramenta de ora2pg.
> * Migre o esquema de exemplo utilizando a ferramenta de ora2pg.
> * Crie uma instância do serviço de migração de base de dados do Azure.
> * Crie um projeto de migração com o Azure Database Migration Service.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> Utilizar o Azure Database Migration Service para efetuar uma migração online requer a criação de uma instância com base no escalão de preço Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do serviço de migração de base de dados do Azure na mesma região do Azure como a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como efetuar uma migração online do Oracle para a base de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Transfira e instale [Oracle 11g Release 2 (Standard Edition, um do Standard Edition ou Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Transferir o exemplo **RH** da base de dados do [aqui](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Transferir e instalar ora2pg em qualquer uma [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) ou [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Criar uma instância na Base de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Ligar à instância e criar uma base de dados usando a instrução desta [documento](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Criar uma rede Virtual do Azure (VNet) para o Azure Database Migration Service com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local através de um [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNet, veja a [documentação das redes virtuais](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

  > [!NOTE]
  > Durante a configuração de VNet, se utilizar o ExpressRoute com peering de rede para a Microsoft, adicione o seguinte serviço [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) à sub-rede na qual o serviço será aprovisionado:
  > * Ponto de extremidade de destino da base de dados (por exemplo, ponto de extremidade do SQL, ponto final do Cosmos DB etc.)
  > * Ponto final de armazenamento
  > * Ponto final de barramento de serviço
  >
  > Esta configuração é necessária porque o serviço de migração de base de dados do Azure não tem conectividade à internet.

* Certifique-se de que as regras do grupo de segurança de rede (NSG) do VNet não bloqueiam as seguintes portas de comunicação de entrada para o Azure Database Migration Service: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do Azure VNet NSG, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o serviço de migração de base de dados do Azure aceder ao servidor Oracle de origem, que, por predefinição, é a porta TCP 1521.
* Quando utilizar uma aplicação de firewall na frente de suas bases de dados de origem, terá de adicionar regras de firewall para permitir que o serviço de migração de base de dados do Azure acessar os bancos de dados de origem para migração.
* Criar ao nível do servidor [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) para base de dados do Azure para PostgreSQL permitir o acesso de serviço de migração de base de dados do Azure para as bases de dados de destino. Forneça o intervalo de sub-rede da VNet utilizada para o Azure Database Migration Service.
* Ative o acesso para as bases de dados do Oracle de origem.

  > [!NOTE]
  > A função DBA é necessária para um utilizador ligar à origem de Oracle.

  * Os registos de Refazer do arquivo é necessário para sincronização incremental no serviço de migração de base de dados do Azure para capturar alterações de dados. Siga estes passos para configurar a origem de Oracle:
    * Inicie sessão com o privilégio SYSDBA executando o seguinte comando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Encerre a instância de base de dados ao executar o comando seguinte.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Aguarde a confirmação `'ORACLE instance shut down'`.

    * Iniciar a nova instância e a montagem (mas não abrem) a base de dados para ativar ou desativar o arquivamento bu executando o seguinte comando:

      ```
      STARTUP MOUNT;
      ```

      A base de dados tem de ser montado; Aguarde pela confirmação 'À instância de Oracle'.

    * Altere base de dados do modo de arquivamento, executando o seguinte comando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Abra a base de dados para as operações normais, executando o seguinte comando:

      ```
      ALTER DATABASE OPEN;
      ```

      Terá de reiniciar para o ficheiro de arco a aparecer.

    * Para verificar, execute o seguinte comando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Deverá receber uma resposta `'ARCHIVELOG'`. Se a resposta é `'NOARCHIVELOG'`, em seguida, o requisito de não for cumprido.

  * Ative suplementar de registo para a replicação através de uma das seguintes opções.

    * **Opção 1**.
      Altere o registo a suplementares nível do banco de dados para cobrir todas as tabelas com PK e índice exclusivo. A consulta de deteção irá devolver `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Registo de alterações a nível de tabela suplementar. Execute apenas para as tabelas que têm a manipulação de dados e não tem PKs ou índices exclusivos.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opção 2**.
      Alterar o registo a suplementares nível do banco de dados para cobrir todas as tabelas e devolve a consulta de deteção `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Registo de alterações a nível de tabela suplementar. Siga a lógica abaixo para executar apenas uma instrução para cada tabela.

      Se a tabela tem uma chave primária:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Se a tabela tem um índice exclusivo:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Caso contrário, execute o seguinte comando:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Para verificar, execute o seguinte comando:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Deverá receber uma resposta `'YES'`.

> [!IMPORTANT]
> Para a versão de pré-visualização pública deste cenário, o serviço de migração de base de dados do Azure suporta versão Oracle 10g ou 11g. Os clientes que executam a versão do Oracle 12 c ou posterior deve ter em consideração que o protocolo de autenticação mínimo permitido para o controlador ODBC ligar a Oracle deve ser 8. Para uma origem de Oracle que é a versão 12C ou posterior, tem de configurar o protocolo de autenticação da seguinte forma:
>
> * Atualize SQLNET. ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Reinicie o computador para as novas definições surtam efeito.
> * Altere a palavra-passe para os utilizadores existentes:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Para obter mais informações, consulte a página [aqui](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Finalmente, lembre-se de que o protocolo de autenticação a alteração pode afetar a autenticação de cliente.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Avalie o esforço para Oracle para a base de dados do Azure para a migração de PostgreSQL

Recomendamos que utilize ora2pg para avaliar o esforço necessário para migrar do Oracle para o banco de dados do Azure para PostgreSQL. Utilize o `ora2pg -t SHOW_REPORT` diretiva para criar um relatório listando todos os objetos do Oracle, o custo estimado de migração (em dias de desenvolvedor) e determinados objetos de base de dados que podem exigir uma atenção especial como parte da conversão.

A maioria dos clientes serão gastar um tempo considerável, consultar o relatório de avaliação e considerar o esforço de conversão automática e manual.

Para configurar e executar ora2pg para criar um relatório de avaliação, consulte o **Premigration: Avaliação** secção do [Oracle para a base de dados do Azure para PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Um relatório de avaliação de ora2pg de exemplo está disponível para referência [aqui](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Exporte o esquema do Oracle

Recomendamos que utilize ora2pg para converter o esquema do Oracle e outros objetos de Oracle (tipos, procedimentos, funções, etc.) para um esquema que é compatível com a base de dados do Azure para PostgreSQL. ora2pg inclui muitas diretivas para o ajudar a definir previamente determinados tipos de dados. Por exemplo, pode usar o `DATA_TYPE` diretiva para substituir todos os NUMBER(*,0) com bigint, em vez de NUMERIC(38).

Pode executar ora2pg para exportar cada um dos objetos da base de dados em arquivos. SQL. Em seguida, pode rever os ficheiros. SQL antes de importá-los à base de dados do Azure para PostgreSQL com psql ou pode executar o. Script SQL em PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Por exemplo:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Para configurar e executar ora2pg para conversão de esquema, consulte o **migração: Esquema e dados** secção do [Oracle para a base de dados do Azure para PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Configurar o esquema na base de dados do Azure para PostgreSQL

Por predefinição, o Oracle mantém o schema.table.column em todos os casos de canto superior, enquanto o PostgreSQL mantém schema.table.column em letra minúscula. Para o serviço de migração de base de dados do Azure iniciar o movimento de dados do Oracle para a base de dados do Azure para PostgreSQL, o schema.table.column tem de ser o mesmo formato de maiúsculas, como a origem da Oracle.

Por exemplo, se a origem de Oracle tem como esquema de "RH"." FUNCIONÁRIOS"." EMPLOYEE_ID", em seguida, o esquema de PostgreSQL tem de utilizar o mesmo formato.

Para garantir que o formato de caso do schema.table.column é o mesmo para Oracle e base de dados do Azure para PostgreSQL, recomendamos que utilize os seguintes passos.

> [!NOTE]
> Pode usar uma abordagem diferente para derivar o esquema de maiúsculas. Estamos a trabalhar para melhorar e automatizar este passo.

1. Exporte esquemas com ora2pg com casos inferior. O script de sql de criação de tabela, crie um esquema com maiúsculas "SCHEMA" manualmente.
2. Importe o restante dos objetos de Oracle, como acionadores, seqüências, procedimentos, tipos e funções, para a base de dados do Azure para PostgreSQL.
3. Para tornar o caso de tabela e coluna superior, execute o seguinte script:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Remova a chave estrangeira do banco de dados de destino para a carga completa executar. Consulte a **migrar o esquema de exemplo** secção do artigo [aqui](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) para um script que pode utilizar para remover a chave estrangeira.
* Utilize o serviço de migração de base de dados do Azure para executar para carregamento completo e sincronização.
* Quando os dados no destino da base de dados do Azure para PostgreSQL a instância são processados com a origem, efetue a transição no serviço de migração de base de dados do Azure da base de dados.
* Para tornar o esquema, tabela e coluna minúsculas (se o esquema para a base de dados do Azure para PostgreSQL deve ser desta forma para consulta de aplicação), execute o seguinte script:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione uma VNet já existente ou crie um novo.

    A VNet fornece o Azure Database Migration Service com acesso à origem de Oracle e o destino da base de dados do Azure para PostgreSQL a instância.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

5. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

    ![Localizar a instância do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **Oracle**, no **tipo de servidor de destino**  caixa de texto, selecione **base de dados do Azure para PostgreSQL**.
5. Na **escolha o tipo de atividade** secção, selecione **migração de dados Online**.

   ![Criar Projeto do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Em alternativa, pode escolher **criar projeto apenas** para criar o projeto de migração e executar a migração mais tarde.

6. Selecione **salvar**, tenha em atenção os requisitos para utilizar com êxito o serviço de migração de base de dados do Azure para efetuar uma migração online e, em seguida, selecione **criar e a execução de atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* Sobre o **adicionar detalhes da origem** ecrã, especifique os detalhes de ligação para a instância de Oracle de origem.

  ![Ecrã Adicionar Detalhes da Origem](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Carregar o driver Oracle OCI

1. Selecione **salvar**e, em seguida, no **controlador instalar OCI** ecrã, inicie sessão na sua conta do Oracle e transferir o controlador **instantclient basiclite do windows.x64-12.2.0.1.0.zip**(37,128,586 Byte(s)) (SHA1 soma de verificação: 865082268) partir [aqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Transferir o controlador para uma pasta compartilhada.

   Certifique-se de que a pasta é partilhada com o nome de utilizador que especificou com acesso só de leitura mínimo. Serviço de migração de base de dados do Azure acede e lê a partir do compartilhamento de carregar o driver OCI para o Azure, representando o nome de utilizador que especificar.

   O nome de utilizador que especificar tem de ser uma conta de utilizador do Windows.

   ![Instalação de Driver OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **salvar**e, em seguida, no **detalhes de destino** ecrã, especifique os detalhes de ligação para a base de dados do Azure de destino para o servidor PostgreSQL, que é a instância já aprovisionada da base de dados do Azure para PostgreSQL para o qual o **RH** esquema foi implementado.

    ![Ecrã Detalhes do destino](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados de destino contém o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

  É apresentada a janela da atividade de migração e o **Estado** da atividade é **a inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

     ![Estado da atividade - em execução](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Sob **nome da base de dados**, selecione uma base de dados específica para obter o estado de migração para **carregamento de dados completo** e **sincronização de dados incrementais** operações.

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).

     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.

   ![Iniciar transferência](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.
4. Quando o estado de migração de base de dados é apresentado **concluído**, conectar seus aplicativos para o destino nova base de dados do Azure para PostgreSQL a instância.

 > [!NOTE]
 > Uma vez que o PostgreSQL por predefinição tem schema.table.column em letras minúsculas, pode reverter de maiúsculas em minúsculas, utilizando o script na **configurar o esquema na base de dados do Azure para PostgreSQL** secção anteriormente neste artigo.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o serviço de migração de base de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre a base de dados do Azure para PostgreSQL, consulte o artigo [o que é a base de dados do Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
