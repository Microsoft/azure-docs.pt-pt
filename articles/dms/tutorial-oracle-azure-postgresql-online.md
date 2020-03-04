---
title: 'Tutorial: Migrar o Oracle on-line para a Base de Dados Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line da Oracle no local ou em máquinas virtuais para a Base de Dados Azure para PostgreSQL utilizando o Serviço de Migração de Bases de Dados Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 14db95adccf5118321bc763cbe599e19febc7eac
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255569"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Migrar o Oráculo para a Base de Dados Azure para PostgreSQL online usando DMS (Pré-visualização)

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar as bases de dados das bases de dados da Oracle alojadas no local ou em máquinas virtuais para a Base de [Dados Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) com o mínimo de tempo de inatividade. Por outras palavras, pode completar a migração com o mínimo de tempo de paragem para a aplicação. Neste tutorial, migra a base de dados de amostras de **RH** de uma instância de máquina virtual ou no local do Oracle 11g para a Base de Dados Azure para PostgreSQL utilizando a atividade de migração on-line no Serviço de Migração de Bases de Dados Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Avalie o esforço de migração utilizando a ferramenta ora2pg.
> * Migrar o esquema da amostra utilizando a ferramenta ora2pg.
> * Crie uma instância do Serviço de Migração de Bases de Dados Azure.
> * Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Serviço de Migração de Bases de Dados Azure para realizar uma migração online requer a criação de uma instância baseada no nível de preços Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do Serviço de Migração de Bases de Dados Azure na mesma região do Azure que a base de dados alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração on-line de Oracle para Azure Database para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Descarregue e instale [o Oracle 11g Release 2 (Standard Edition, Standard Edition One ou Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Descarregue a base de dados de **RH** da amostra [daqui.](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)
* Descarregue e [instale ora2pg no Windows ou linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Criar uma instância na Base de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Ligue-se à instância e crie uma base de dados utilizando as instruções neste [documento](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

  > [!NOTE]
  > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o peering da rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
  >
  > * Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
  > * Ponto final de armazenamento
  > * Ponto final do ônibus de serviço
  >
  > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.

* Certifique-se de que as regras do Grupo de Segurança da Rede Virtual (NSG) não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)de segurança da rede .
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a sua firewall windows para permitir que o Serviço de Migração da Base de Dados Azure aceda ao servidor Oracle de origem, que por padrão é a porta TCP 1521.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração.
* Crie uma [regra](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de firewall ao nível do servidor para a Base de Dados Azure para postgreSQL para permitir o acesso do Serviço de Migração de Bases de Dados Azure às bases de dados-alvo. Forneça a gama de subredes da rede virtual utilizada para o Serviço de Migração de Bases de Dados Azure.
* Ative o acesso às bases de dados da Oráculo de origem.

  > [!NOTE]
  > A função DBA é necessária para que um utilizador se conectem à fonte oráculo.

  * O Archive Redo Logs é necessário para uma sincronização incremental no Serviço de Migração de Bases de Dados Azure para capturar a alteração de dados. Siga estes passos para configurar a fonte oráculo:
    * Inscreva-se utilizando o privilégio SYSDBA executando o seguinte comando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Desligue a instância da base de dados executando o seguinte comando.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Aguarde a confirmação `'ORACLE instance shut down'`.

    * Inicie a nova instância e monte (mas não abra) a base de dados para ativar ou desativar o bu de arquivamento com o seguinte comando:

      ```
      STARTUP MOUNT;
      ```

      A base de dados deve ser montada; esperar pela confirmação 'Oracle instância começou'.

    * Altere o modo de arquivamento da base de dados executando o seguinte comando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Abra a base de dados para operações normais executando o seguinte comando:

      ```
      ALTER DATABASE OPEN;
      ```

      Pode ter de reiniciar para que o ficheiro ARC apareça.

    * Para verificar, execute o seguinte comando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Deve receber uma resposta `'ARCHIVELOG'`. Se a resposta for `'NOARCHIVELOG'`, então o requisito não é cumprido.

  * Ativar o registo suplementar para a replicação utilizando uma das seguintes opções.

    * **Opção 1**.
      Altere o nível de registo suplementar do nível da base de dados para cobrir todas as tabelas com PK e índice único. A consulta de deteção devolverá `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Altere o nível de registo suplementar do nível da tabela. Correr apenas para tabelas que tenham manipulação de dados e não tenham PKs ou índices únicos.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opção 2**.
      Altere o nível de registo suplementar do nível da base de dados para cobrir todas as tabelas e a consulta de deteção retorna `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Altere o nível de registo suplementar do nível da tabela. Siga a lógica abaixo para executar apenas uma declaração para cada mesa.

      Se a mesa tiver uma chave primária:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Se a tabela tiver um índice único:

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

    Deve receber uma resposta `'YES'`.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Avaliar o esforço de uma Base de Dados Oráculo para Azure para migração postgresQL

Recomendamos a utilização do ora2pg para avaliar o esforço necessário para migrar da Oracle para a Base de Dados Azure para postgreSQL. Utilize a diretiva `ora2pg -t SHOW_REPORT` para criar um relatório que enumera todos os objetos oráculo, o custo estimado de migração (em dias de desenvolvimento) e certos objetos de base de dados que possam necessitar de uma atenção especial como parte da conversão.

A maioria dos clientes passará um tempo considerável a rever o relatório de avaliação e a considerar o esforço de conversão automática e manual.

Para configurar e executar o ora2pg para criar um relatório de avaliação, consulte a secção **de pré-migração: Secção** de avaliação do [Oráculo para a Base de Dados Azure para livro de receitas postgresQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Um relatório de avaliação ora2pg da amostra está disponível para referência [aqui](https://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Exportar o esquema oráculo

Recomendamos que utilize o ora2pg para converter o esquema Oracle e outros objetos Oracle (tipos, procedimentos, funções, etc.) para um esquema compatível com a Base de Dados Azure para PostgreSQL. o ora2pg inclui muitas diretivas para ajudá-lo a pré-definir certos tipos de dados. Por exemplo, pode utilizar a diretiva `DATA_TYPE` para substituir todos os NÚMERO (*,0) por mais grandes do que numeric(38).

Pode executar o ora2pg para exportar cada um dos objetos da base de dados em ficheiros .sql. Em seguida, pode rever os ficheiros .sql antes de os importar para a Base de Dados Azure para postgreSQL utilizando o psql ou pode executar o . Script SQL em PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Por exemplo:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Para configurar e executar ora2pg para conversão de esquemas, consulte a **Migração: Schema e** secção de dados do Oracle para azure Database para Livro de [Receitas PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Configurar o esquema na Base de Dados Azure para PostgreSQL

Pode optar por converter esquemas de mesa Oracle, procedimentos armazenados, pacotes e outros objetos de base de dados para torná-los Postgres compatíveis usando ora2pg antes de iniciar um pipeline de migração no Serviço de Migração de Bases de Dados Azure. Consulte os links abaixo para saber como trabalhar com o ora2pg:

* [Instale ora2pg no Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Livro de receitas de migração Da Oracle para Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

O Serviço de Migração de Bases de Dados Azure também pode criar o esquema de mesa PostgreSQL. O serviço acede ao esquema de mesa na fonte oráculo conectada e cria um esquema de mesa compatível na Base de Dados Azure para PostgreSQL. Certifique-se de validar e verificar o formato de esquema na Base de Dados Azure para PostgreSQL após o Serviço de Migração da Base de Dados Azure terminar de criar o esquema e mover os dados.

> [!IMPORTANT]
> O Serviço de Migração da Base de Dados Azure só cria o esquema de mesa; não são criados outros objetos de base de dados, tais como procedimentos armazenados, pacotes, índices, etc.

Certifique-se também de que deixa cair a chave estrangeira na base de dados do alvo para que a carga completa seja executada. Consulte a secção de **esquemas** de amostra da amostra do artigo [aqui](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) para obter um script que pode usar para deixar cair a chave estrangeira. Utilize o Serviço de Migração de Bases de Dados Azure para funcionar para a carga completa e sincronização.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Quando o esquema da mesa PostgreSQL já existe

Se criar um esquema PostgreSQL utilizando ferramentas como o ora2pg antes de iniciar o movimento de dados com o Serviço de Migração de Bases de Dados Azure, mapeie as tabelas de origem para as tabelas-alvo no Serviço de Migração da Base de Dados Azure.

1. Quando cria um novo Oracle para a Base de Dados Azure para o projeto de migração PostgreSQL, é-lhe solicitado que selecione a base de dados de destino e o esquema de alvo em select schemas step. Preencha a base de dados do alvo e o esquema de destino.

   ![Mostrar subscrições no portal](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. O ecrã de definições de **migração** apresenta uma lista de tabelas na fonte do Oráculo. O Serviço de Migração da Base de Dados Azure tenta combinar tabelas na fonte e nas tabelas-alvo com base no nome da mesa. Se existirem várias tabelas-alvo correspondentes com invólucros diferentes, pode selecionar a tabela de destino para o mapa.

    ![Mostrar subscrições no portal](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Se precisar de mapear nomes de tabelas de origem para tabelas com nomes diferentes, [envie um](mailto:dmsfeedbac@microsoft.com) e-maildmsfeedback@microsoft.come podemos fornecer um script para automatizar o processo.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Quando o esquema da mesa PostgreSQL não existe

Se a base de dados PostgreSQL não contiver qualquer informação de esquema de mesa, o Serviço de Migração da Base de Dados Azure converte o esquema de origem e recria-o na base de dados do alvo. Lembre-se, o Azure Database Migration Service cria apenas o esquema de mesa, não outros objetos de base de dados, tais como procedimentos armazenados, pacotes e índices.
Para que o Serviço de Migração de Bases de Dados Azure crie o esquema para si, certifique-se de que o seu ambiente alvo inclui um esquema sem tabelas existentes. Se o Serviço de Migração da Base de Dados Azure descobrir qualquer tabela, o serviço assume que o esquema foi criado por uma ferramenta externa como o ora2pg.

> [!IMPORTANT]
> O Serviço de Migração da Base de Dados Azure exige que todas as tabelas sejam criadas da mesma forma, utilizando o Serviço de Migração de Bases de Dados Azure ou uma ferramenta como o ora2pg, mas não ambas.

Para começar:

1. Crie um esquema na base de dados do alvo com base nos requisitos da sua aplicação. Por predefinição, o esquema da tabela PostgreSQL e os nomes das colunas são minúsculos. O esquema e as colunas da mesa oracle, por outro lado, são por defeito em todos os casos maiúsculos.
2. Em selecionar esquemia, especifique a base de dados do alvo e o esquema de destino.
3. Com base no esquema que cria na Base de Dados Azure para PostgreSQL, o Serviço de Migração da Base de Dados Azure utiliza as seguintes regras de transformação:

    Se o nome do esquema na fonte do Oráculo e corresponder ao da Base de Dados Azure para postgreSQL, então o Serviço de Migração da Base de Dados Azure *cria o esquema de mesa utilizando o mesmo caso que no alvo*.

    Por exemplo:

    | Esquema de Oráculo fonte | Target PostgreSQL Database.Schema | DMS criou schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR". PAÍSES". COUNTRY_ID" |
    | HR | targetHR.HR | "HR". PAÍSES". COUNTRY_ID" |
    | HR | targetHR.Hr | *Incapaz de mapear casos mistos |

    *Para criar esquemas mistos e nomes de mesa no target PostgreSQL, contacte [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com). Podemos fornecer um script para configurar esquemas de mesa mistos na base de dados postgresQL alvo.

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

4. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte Oracle e à base de dados azure-alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

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
4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo servidor Fonte,** selecione **Oracle**, na caixa de texto do tipo **servidor Target,** selecione Base de **Dados Azure para PostgreSQL**.
5. Na secção **Escolha tipo de atividade,** selecione **migração de dados online**.

   ![Criar Projeto do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternadamente, pode escolher o **projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar,** note os requisitos para utilizar com sucesso o Serviço de Migração de Bases de Dados Azure para realizar uma migração online e, em seguida, selecione **Criar e executar atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* No ecrã **Adicionar Detalhes de Origem,** especifique os detalhes de ligação para a instância de origem Oracle.

  ![Ecrã Adicionar Detalhes da Origem](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Carregar o controlador OCI da Oracle

1. Selecione **Guardar**, e depois no ecrã de **controlador OCI instalar,** inste na sua conta Oracle e baixe o **cliente instantâneo do condutor-basiclite-windows.x64-12.2.0.0.zip** (37.128.586 Byte(s)) (SHA1 Checksum: 865082268) a partir [daqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Descarregue o condutor para uma pasta partilhada.

   Certifique-se de que a pasta é partilhada com o nome de utilizador que especificou com o mínimo de acesso de leitura. O Azure Database Migration Service acede e lê a partir da parte para enviar o controlador OCI para o Azure, personificando o nome de utilizador que especifica.

   O nome de utilizador que especifica deve ser uma conta de utilizador do Windows.

   ![Instalação do controlador OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar**, e depois no ecrã de dados do **Target,** especificar os detalhes de ligação para o target Azure Database para o servidor PostgreSQL, que é a instância pré-disponibilizada de Base de Dados Azure para PostgreSQL a que o esquema **hr** foi implementado.

    ![Ecrã Detalhes do destino](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    ![Mapear para as bases de dados de destino](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

  É apresentada a janela da atividade de migração e o **Estado** da atividade é **a inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

     ![Estado de Atividade - execução](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Em **nome da base de dados,** selecione uma base de dados específica para chegar ao estado de migração para **operações completas** de carga de dados e **sincronização de dados incrementais.**

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).

     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.

   ![Iniciar transferência](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.
4. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados-alvo Azure para a instância PostgreSQL.

 > [!NOTE]
 > Uma vez que postgreSQL por padrão tem schema.table.column em maiúsculas minúsculas, pode reverter da caixa superior para a minúscula, utilizando o script na Configuração do esquema na base de dados Azure para a secção **PostgreSQL** no início deste artigo.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo O que é o Serviço de Migração da Base de [Dados Azure?](https://docs.microsoft.com/azure/dms/dms-overview)
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo O que é a Base de [Dados Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview)
