---
title: 'Tutorial: Migrar o Oracle on-line para a Base de Dados de Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar uma migração on-line a partir de Oracle no local ou em máquinas virtuais para Azure Database for PostgreSQL usando o Azure Database Migration Service.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: df789161bb9db8d49f069992600b5fcb4f78dd03
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006547"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Migrar o Oráculo para Azure Database para postgreSQL on-line usando DMS (Preview)

Pode utilizar o Azure Database Migration Service para migrar as bases de dados das bases de dados da Oracle hospedadas no local ou em máquinas virtuais [para a Base de Dados Azure para PostgreSQL](../postgresql/index.yml) com tempo de inatividade mínimo. Por outras palavras, pode completar a migração com o mínimo de tempo de inatividade para a aplicação. Neste tutorial, migra a base de dados de amostras de RH de uma caixa de amostras de **AR** de uma placa de máquina virtual do Oracle 11g para a Base de Dados de Azure para PostgreSQL utilizando a atividade de migração online no Azure Database Migration Service.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
>
> * Avalie o esforço de migração utilizando a ferramenta ora2pg.
> * Migrar o esquema da amostra utilizando a ferramenta ora2pg.
> * Crie uma instância do Azure Database Migration Service.
> * Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> A utilização do Azure Database Migration Service para realizar uma migração online requer a criação de um caso baseado no nível de preços Premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de um exemplo do Azure Database Migration Service na mesma região Azure que a base de dados-alvo. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como realizar uma migração on-line da Oracle para a Azure Database para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Descarregue e instale [o Oracle 11g Release 2 (Standard Edition, Standard Edition One ou Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Descarregue a base de **dados de RH** da amostra [daqui.](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)
* Faça o download e [instale o ora2pg no Windows ou linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Criar uma instância na Base de Dados do Azure para PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Ligue-se ao caso e crie uma base de dados utilizando as instruções deste [documento.](../postgresql/tutorial-design-database-using-azure-portal.md)
* Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

  > [!NOTE]
  > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
  >
  > * Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
  > * Ponto final de armazenamento
  > * Ponto final do ônibus de serviço
  >
  > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.

* Certifique-se de que as suas regras do Grupo de Segurança da Rede de Rede Virtual (NSG) não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração da Base de Dados Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor Oracle de origem, que por padrão é a porta TCP 1521.
* Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.
* Crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) ao nível do servidor para a Base de Dados Azure para PostgreSQL para permitir o acesso do Serviço de Migração da Base de Dados Azure às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
* Permitir o acesso às bases de dados da Oráculo de origem.

  > [!NOTE]
  > A função DBA é necessária para que um utilizador se conecte à fonte Oráculo.

  * Os Registos de Redo de Arquivo são necessários para sincronização incremental no Serviço de Migração da Base de Dados Azure para capturar a mudança de dados. Siga estes passos para configurar a fonte do Oráculo:
    * Inscreva-se utilizando o privilégio SYSDBA executando o seguinte comando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Desligue a instância da base de dados executando o seguinte comando.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Aguarde a `'ORACLE instance shut down'` confirmação.

    * Inicie a nova instância e monte (mas não abra) a base de dados para ativar ou desativar o arquivo bu executando o seguinte comando:

      ```
      STARTUP MOUNT;
      ```

      A base de dados deve ser montada; aguardar a confirmação "Oráculo exemplo começou".

    * Alterar o modo de arquivamento da base de dados executando o seguinte comando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Abra a base de dados para operações normais executando o seguinte comando:

      ```
      ALTER DATABASE OPEN;
      ```

      Pode ser necessário reiniciar para que o ficheiro ARC apareça.

    * Para verificar, executar o seguinte comando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Deve receber uma `'ARCHIVELOG'` resposta. Se a resposta `'NOARCHIVELOG'` for, então a exigência não está satisfeita.

  * Ativar a marcação suplementar para replicação utilizando uma das seguintes opções.

    * **Opção 1**.
      Altere o registo suplementar de nível de base de dados para cobrir todas as tabelas com PK e índice único. A consulta de deteção `'IMPLICIT'` regressará.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Altere o registo suplementar do nível da tabela. Corra apenas para tabelas que tenham manipulação de dados e não tenham PKs ou índices únicos.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opção 2**.
      Altere o registo suplementar de nível de base de dados para cobrir todas as tabelas e a consulta de deteção `'YES'` retorna .

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Altere o registo suplementar do nível da tabela. Siga a lógica abaixo para executar apenas uma declaração para cada mesa.

      Se a tabela tiver uma chave primária:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Se a tabela tiver um índice único:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Caso contrário, executar o seguinte comando:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Para verificar, executar o seguinte comando:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Deve receber uma `'YES'` resposta.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Avaliar o esforço de uma Base de Dados de Azure para a migração pós-SQL

Recomendamos a utilização de ora2pg para avaliar o esforço necessário para migrar da Oracle para a Base de Dados de Azure para postgreSQL. Utilize a `ora2pg -t SHOW_REPORT` diretiva para criar um relatório que enumera todos os objetos Oráculos, o custo estimado de migração (em dias de programação) e certos objetos de base de dados que podem necessitar de uma atenção especial como parte da conversão.

A maioria dos clientes gastará um tempo considerável a rever o relatório de avaliação e a considerar o esforço de conversão automática e manual.

Para configurar e executar o ora2pg para criar um relatório de avaliação, consulte a secção de **Pré-Gração: Secção** de Avaliação da Base de [Dados de Azure para o Livro de Receitas pós-SQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Aqui está disponível [um](https://ora2pg.darold.net/report.html)relatório de avaliação de ora2pg para referência.

## <a name="export-the-oracle-schema"></a>Exportar o esquema do Oráculo

Recomendamos que utilize o ora2pg para converter o esquema Oráculo e outros objetos Oráculos (tipos, procedimentos, funções, etc.) a um esquema compatível com a Base de Dados Azure para PostgreSQL. O ora2pg inclui muitas diretivas para ajudá-lo a pré-definir certos tipos de dados. Por exemplo, pode utilizar a `DATA_TYPE` diretiva para substituir todos os NÚMEROS (*,0) por bigint em vez de NUMMÉRICO(38).

Pode executar ora2pg para exportar cada um dos objetos de base de dados em .sql ficheiros. Em seguida, pode rever os ficheiros .sql antes de os importar para a Base de Dados Azure para PostgreSQL utilizando o psql ou pode executar o . Roteiro SQL em PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Por exemplo:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Para configurar e executar ora2pg para conversão de esquemas, consulte a secção **de Migração: Schema e dados** da Base de [Dados do Oráculo para Azure Para OrasQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Configurar o esquema na Base de Dados Azure para PostgreSQL

Pode optar por converter esquemas de mesa Oracle, procedimentos armazenados, pacotes e outros objetos de base de dados para torná-los compatíveis com postgres utilizando ora2pg antes de iniciar um oleoduto de migração no Azure Database Migration Service. Veja os links abaixo para saber como trabalhar com ora2pg:

* [Instale o ora2pg no Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oráculo para Azure PostgreSQL Livro de Receitas migratórias](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

O Serviço de Migração da Base de Dados Azure também pode criar o esquema de tabela PostgreSQL. O serviço acede ao esquema de tabela na fonte oráculo conectada e cria um esquema de tabela compatível na Base de Dados Azure para PostgreSQL. Certifique-se de validar e verificar o formato de esquema na Base de Dados Azure para PostgreSQL após o Azure Database Migration Service terminar de criar o esquema e mover os dados.

> [!IMPORTANT]
> O Serviço de Migração da Base de Dados Azure apenas cria o esquema de tabela; não são criados outros objetos de base de dados, tais como procedimentos armazenados, pacotes, índices, etc..

Certifique-se também de deixar cair a chave estrangeira na base de dados-alvo para que a carga completa seja executada. Consulte a secção de esquema de **amostras** do artigo [aqui](./tutorial-postgresql-azure-postgresql-online.md) para obter um script que pode usar para deixar cair a chave estrangeira. Utilize o Serviço de Migração da Base de Dados Azure para executar a carga completa e sincronização.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Quando o esquema de mesa PostgreSQL já existe

Se criar um esquema PostgreSQL utilizando ferramentas como o ora2pg antes de iniciar o movimento de dados com o Azure Database Migration Service, mapeeia as tabelas de origem para as tabelas-alvo no Serviço de Migração da Base de Dados Azure.

1. Quando cria um novo oracle para a base de dados de Azure para o projeto de migração PostgreSQL, é solicitado que selecione a base de dados-alvo e o esquema de destino na etapa selete schemas. Preencha a base de dados-alvo e o esquema do alvo.

   ![A imagem mostra o Mapa para as bases de dados do alvo.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. O ecrã **de definições de migração** apresenta uma lista de tabelas na fonte Oráculo. O Serviço de Migração da Base de Dados Azure tenta combinar tabelas na fonte e nas tabelas-alvo com base no nome da mesa. Se existirem várias tabelas-alvo correspondentes com invólucros diferentes, pode selecionar qual a tabela-alvo a mapear.

    ![A screenshot mostra as definições de migração.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Se precisar de mapear nomes de tabelas de origem para tabelas com nomes diferentes, o email [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) e podemos fornecer um script para automatizar o processo.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Quando o esquema de mesa PostgreSQL não existe

Se a base de dados postgreSQL alvo não contiver qualquer informação sobre esquemas de tabela, o Azure Database Migration Service converte o esquema de origem e recria-o na base de dados-alvo. Lembre-se, o Azure Database Migration Service cria apenas o esquema de tabela, não outros objetos de base de dados, tais como procedimentos armazenados, pacotes e índices.
Para que o Serviço de Migração da Base de Dados Azure crie o esquema para si, certifique-se de que o seu ambiente-alvo inclui um esquema sem tabelas existentes. Se o Azure Database Migration Service descobrir qualquer tabela, o serviço assume que o esquema foi criado por uma ferramenta externa como o ora2pg.

> [!IMPORTANT]
> O Azure Database Migration Service exige que todas as tabelas sejam criadas da mesma forma, utilizando o Azure Database Migration Service ou uma ferramenta como o ora2pg, mas não ambas.

Para começar:

1. Crie um esquema na base de dados-alvo com base nos requisitos da sua aplicação. Por predefinição, os nomes de esquema de tabela PostgreSQL e colunas são mais baixos. O esquema de mesa oráculo e colunas, por outro lado, estão por defeito em todo o caso de capital.
2. Na etapa de esquemas Select, especifique a base de dados-alvo e o esquema de destino.
3. Com base no esquema que cria na Base de Dados Azure para PostgreSQL, o Azure Database Migration Service utiliza as seguintes regras de transformação:

    Se o nome do esquema na fonte Oráculo e corresponder à base de dados Azure para PostgreSQL, então o Serviço de Migração da Base de Dados Azure *cria o esquema de tabela utilizando o mesmo caso que no alvo*.

    Por exemplo:

    | Fonte esquema Oráculo | Base de Dados PostgresQL alvo.Schema | DMS criou schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR". PAÍSES". COUNTRY_ID" |
    | HR | targetHR.HR | "RH". PAÍSES". COUNTRY_ID" |
    | HR | targetHR.Hr | *Incapaz de mapear casos mistos |

    *Para criar esquemas de casos mistos e nomes de tabelas no Target PostgreSQL, contacte [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) . Podemos fornecer um script para configurar esquemas de tabela de casos mistos na base de dados postgreSQL alvo.

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

    A rede virtual fornece ao Serviço de Migração da Base de Dados Azure o acesso à fonte Oracle e à base de dados Azure alvo para a instância PostgreSQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

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
4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo servidor Fonte,** selecione **o Oracle**, na caixa de texto do tipo do **servidor Target,** selecione **Azure Database for PostgreSQL**.
5. Na secção Escolha o **tipo de atividade,** selecione Migração de **dados Online.**

   ![Criar Projeto do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Em alternativa, pode escolher **o projeto Criar apenas** para criar o projeto de migração agora e executar a migração mais tarde.

6. **Selecione Save**, note os requisitos para utilizar com sucesso o Serviço de Migração da Base de Dados Azure para realizar uma migração on-line e, em seguida, selecione **Criar e executar atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* No ecrã **'Adicionar Detalhes fonte',** especifique os detalhes da ligação para a origem da ocorrência Oráculo.

  ![Ecrã Adicionar Detalhes da Origem](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Upload Oracle OCI driver

1. **Selecione Guardar**, e, em seguida, no ecrã **do controlador OCI instalar,** inscreva-se na sua conta Oracle e descarregue o **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** do controlador (37.128.586 Byte(s)) (SHA1 Checksum: 865082268) a partir [daqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Descarregue o controlador para uma pasta partilhada.

   Certifique-se de que a pasta é partilhada com o nome de utilizador especificado com acesso mínimo apenas para Leitura. O Serviço de Migração da Base de Dados Azure acede e lê a partir da partilha para fazer o upload do controlador OCI para Azure, personificando o nome de utilizador especificado.

   O nome de utilizador que especifica deve ser uma conta de utilizador do Windows.

   ![Instalação do controlador OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar**, e, em seguida, no ecrã **de detalhes do Destino,** especificar os detalhes de conexão para o target Azure Database para o servidor PostgreSQL, que é o exemplo pré-a provisionado da Base de Dados Azure para PostgreSQL para o qual o esquema **de RH** foi implementado.

    ![Ecrã Detalhes do destino](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

  A janela de atividade de migração aparece, e o **Estado** da atividade está **a ser inicializado.**

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

     ![Estado de Atividade - execução](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. No **Nome da Base de Dados,** selecione uma base de dados específica para chegar ao estado de migração para **operações de sincronização de dados completos** e **de sincronização de dados incrementais.**

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).

     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.

   ![Iniciar transferência](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.
4. Quando o estado de migração da base de dados mostrar **concluído,** ligue as suas aplicações à nova base de dados Azure alvo para a instância PostgreSQL.

 > [!NOTE]
 > Uma vez que o PostgreSQL por predefinição tem schema.table.column em minúsculas, pode reverter de maiúsito para minúscula, utilizando o script na **Configuração do esquema na Base de Dados Azure para a secção PostgreSQL** no início deste artigo.

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o Serviço de Migração de Bases de Dados Azure, consulte o artigo [O que é o Serviço de Migração da Base de Dados Azure?](./dms-overview.md)
* Para obter informações sobre a Base de Dados Azure para PostgreSQL, consulte o artigo [O que é a Base de Dados Azure para PostgreSQL?](../postgresql/overview.md)