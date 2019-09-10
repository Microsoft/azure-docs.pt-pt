---
title: 'Tutorial: Usar o serviço de migração de banco de dados do Azure para executar uma migração online do Oracle para o banco de dados do Azure para PostgreSQL | Microsoft Docs'
description: Aprenda a executar uma migração online do Oracle local ou em máquinas virtuais para o banco de dados do Azure para PostgreSQL usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 8944a5adbe1b9e129b4a95c64aaa7a75fb96ac82
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845573"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Migrar o Oracle para o banco de dados do Azure para PostgreSQL online usando DMS (versão prévia)

Você pode usar o serviço de migração de banco de dados do Azure para migrar os bancos de dados do Oracle hospedados localmente ou em máquinas virtuais para o [Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/) com tempo de inatividade mínimo. Em outras palavras, você pode concluir a migração com o mínimo de tempo de inatividade para o aplicativo. Neste tutorial, você migra o banco de dados de exemplo de **RH** de uma instância de máquina virtual ou local do Oracle 11g para o banco de dados do Azure para PostgreSQL usando a atividade de migração online no serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> * Avalie o esforço de migração usando a ferramenta ora2pg.
> * Migre o esquema de exemplo usando a ferramenta ora2pg.
> * Crie uma instância do serviço de migração de banco de dados do Azure.
> * Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> * Executar a migração.
> * Monitorizar a migração.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium.

> [!IMPORTANT]
> Para uma experiência de migração ideal, a Microsoft recomenda a criação de uma instância do serviço de migração de banco de dados do Azure na mesma região do Azure que o banco de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve como executar uma migração online do Oracle para o banco de dados do Azure para PostgreSQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Baixe e instale o [Oracle 11g versão 2 (Standard Edition, Standard Edition One ou Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Baixe o exemplo de banco de dados de **RH** [aqui](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Baixe e instale o ora2pg no [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) ou no [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Criar uma instância na Base de Dados do Azure para PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Conecte-se à instância do e crie um banco de dados usando a instrução neste [documento](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Criar uma VNet (rede virtual) do Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNet, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

  > [!NOTE]
  > Durante a configuração da VNet, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
  > * Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
  > * Ponto de extremidade de armazenamento
  > * Ponto de extremidade do barramento de serviço
  >
  > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.

* Verifique se suas regras de NSG (grupo de segurança de rede) VNet não bloqueiam as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG VNet do Azure, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o servidor Oracle de origem, que por padrão é a porta TCP 1521.
* Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração.
* Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o banco de dados do Azure para PostgreSQL a fim de permitir o acesso ao serviço de migração de banco de dados do Azure aos dados de destino Forneça o intervalo de sub-rede da VNet usada para o serviço de migração de banco de dados do Azure.
* Habilite o acesso aos bancos de dados Oracle de origem.

  > [!NOTE]
  > A função DBA é necessária para que um usuário se conecte à origem do Oracle.

  * Os logs de refazer de arquivamento são necessários para a sincronização incremental no serviço de migração de banco de dados do Azure para capturar a alteração Siga estas etapas para configurar a origem do Oracle:
    * Entre usando o privilégio SYSDBA executando o seguinte comando:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Desligue a instância do banco de dados executando o comando a seguir.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Aguarde a confirmação `'ORACLE instance shut down'`.

    * Inicie a nova instância e monte (mas não abra) o banco de dados para habilitar ou desabilitar o arquivamento Bu executando o seguinte comando:

      ```
      STARTUP MOUNT;
      ```

      O banco de dados deve ser montado; Aguarde a confirmação ' instância Oracle iniciada '.

    * Altere o modo de arquivamento do banco de dados executando o seguinte comando:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Abra o banco de dados para operações normais executando o seguinte comando:

      ```
      ALTER DATABASE OPEN;
      ```

      Talvez seja necessário reiniciar para que o arquivo ARC apareça.

    * Para verificar, execute o seguinte comando:

      ```
      SELECT log_mode FROM v$database;
      ```

      Você deve receber uma resposta `'ARCHIVELOG'`. Se a resposta for `'NOARCHIVELOG'`, o requisito não será atendido.

  * Habilite o registro em log suplementar para replicação usando uma das opções a seguir.

    * **Opção 1**.
      Altere o log suplementar de nível de banco de dados para abranger todas as tabelas com CP e índice exclusivo. A consulta de detecção será `'IMPLICIT'`retornada.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Altere o log suplementar do nível de tabela. Executar somente para tabelas que têm manipulação de dados e que não têm índices PKs ou exclusivos.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opção 2**.
      Altere o log suplementar de nível de banco de dados para abranger todas as tabelas e `'YES'`a consulta de detecção retorna.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Altere o log suplementar do nível de tabela. Siga a lógica abaixo para executar apenas uma instrução para cada tabela.

      Se a tabela tiver uma chave primária:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Se a tabela tiver um índice exclusivo:

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

    Você deve receber uma resposta `'YES'`.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Avaliar o esforço de uma migração do Oracle para o banco de dados do Azure para PostgreSQL

É recomendável usar ora2pg para avaliar o esforço necessário para migrar do Oracle para o banco de dados do Azure para PostgreSQL. Use a `ora2pg -t SHOW_REPORT` diretiva para criar um relatório listando todos os objetos Oracle, o custo estimado de migração (em dias do desenvolvedor) e determinados objetos de banco de dados que podem exigir atenção especial como parte da conversão.

A maioria dos clientes gastará uma quantidade considerável de tempo revisando o relatório de avaliação e considerando o esforço de conversão automática e manual.

Para configurar e executar o ora2pg para criar um relatório de avaliação, **consulte a pré-migração: Seção** de avaliação do [banco de dados Oracle para o Azure para PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf). Um exemplo de relatório de avaliação ora2pg está disponível para referência [aqui](http://ora2pg.darold.net/report.html).

## <a name="export-the-oracle-schema"></a>Exportar o esquema Oracle

Recomendamos que você use ora2pg para converter o esquema Oracle e outros objetos Oracle (tipos, procedimentos, funções, etc.) em um esquema compatível com o banco de dados do Azure para PostgreSQL. o ora2pg inclui muitas diretivas para ajudá-lo a definir previamente determinados tipos de dados. Por exemplo, você pode usar a `DATA_TYPE` diretiva para substituir todo o número (*, 0) por bigint em vez de numeric (38).

Você pode executar o ora2pg para exportar cada um dos objetos de banco de dados em arquivos. Sql. Em seguida, você pode examinar os arquivos. SQL antes de importá-los para o banco de dados do Azure para PostgreSQL usando psql ou pode executar o. Script SQL em PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Por exemplo:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Para configurar e executar o ora2pg para conversão de esquema, **consulte a migração: Seção esquema e** dados do [manual do Oracle para o Azure database for PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Configurar o esquema no banco de dados do Azure para PostgreSQL

Você pode optar por converter esquemas de tabela do Oracle, procedimentos armazenados, pacotes e outros objetos de banco de dados para torná-los postgres compatíveis usando ora2pg antes de iniciar um pipeline de migração no serviço de migração de banco de dados do Azure. Consulte os links abaixo para saber como trabalhar com o ora2pg:

* [Instalar o ora2pg no Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf)
* [Manual de migração do Oracle para o Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

O serviço de migração de banco de dados do Azure também pode criar o esquema de tabela PostgreSQL. O serviço acessa o esquema de tabela na fonte Oracle conectada e cria um esquema de tabela compatível no banco de dados do Azure para PostgreSQL. Certifique-se de validar e verificar o formato de esquema no banco de dados do Azure para PostgreSQL depois que o serviço de migração de banco de dados do Azure terminar de criar o esquema e mover os dados.

> [!IMPORTANT]
> O serviço de migração de banco de dados do Azure cria apenas o esquema de tabela; outros objetos de banco de dados, como procedimentos armazenados, pacotes, índices, etc., não são criados.

Além disso, não deixe de descartar a chave estrangeira no banco de dados de destino para que a carga completa seja executada. Consulte a seção **migrar o esquema de exemplo** do artigo [aqui](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) para obter um script que você pode usar para descartar a chave estrangeira. Use o serviço de migração de banco de dados do Azure para executar a carga e a sincronização completas.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Quando o esquema de tabela PostgreSQL já existe

Se você criar um esquema PostgreSQL usando ferramentas como ora2pg antes de iniciar a movimentação de dados com o serviço de migração de banco de dado do Azure, mapeie as tabelas de origem para as tabelas de destino no serviço de migração de banco de dados do Azure.

1. Ao criar um novo projeto de migração Oracle para banco de dados do Azure para PostgreSQL, você será solicitado a selecionar o banco de dados de destino e o esquema de destino na etapa selecionar esquemas. Preencha o banco de dados de destino e o esquema de destino.

   ![Mostrar subscrições no portal](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. A tela de **configurações de migração** apresenta uma lista de tabelas na origem do Oracle. O serviço de migração de banco de dados do Azure tenta corresponder tabelas nas tabelas de origem e de destino com base no nome da tabela. Se houver várias tabelas de destino correspondentes com maiúsculas e minúsculas diferentes, você poderá selecionar a tabela de destino para a qual mapear.

    ![Mostrar subscrições no portal](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Se você precisar mapear nomes de tabela de origem para tabelas com nomes diferentes [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) , email e poderemos fornecer um script para automatizar o processo.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Quando o esquema de tabela PostgreSQL não existe

Se o banco de dados PostgreSQL de destino não contiver nenhuma informação de esquema de tabela, o serviço de migração de banco de dados do Azure converte o esquema de origem e o recria no banco de dados de destino. Lembre-se de que o serviço de migração de banco de dados do Azure cria apenas o esquema de tabela, não outros objetos de banco de dados, como procedimentos armazenados, pacotes e índices.
Para que o serviço de migração de banco de dados do Azure crie o esquema para você, verifique se o ambiente de destino inclui um esquema sem tabelas existentes. Se o serviço de migração de banco de dados do Azure descobrir qualquer tabela, o serviço pressupõe que o esquema foi criado por uma ferramenta externa, como ora2pg.

> [!IMPORTANT]
> O serviço de migração de banco de dados do Azure requer que todas as tabelas sejam criadas da mesma maneira, usando o serviço de migração de banco de dados do Azure ou uma ferramenta como ora2pg, mas não ambos.

Para começar:

1. Crie um esquema no banco de dados de destino com base nos requisitos do aplicativo. Por padrão, o esquema de tabela do PostgreSQL e os nomes de colunas ficam menos em maiúsculas. As colunas e o esquema de tabela do Oracle, por outro lado, são, por padrão, em maiúsculas.
2. Na etapa selecionar esquemas, especifique o banco de dados de destino e o esquema de destino.
3. Com base no esquema que você cria no banco de dados do Azure para PostgreSQL, o serviço de migração de banco de dados do Azure usa as seguintes regras de transformação:

    Se o nome do esquema na origem do Oracle e corresponder ao que está no banco de dados do Azure para PostgreSQL, o serviço de migração de banco de dados do Azure *criará o esquema de tabela usando o mesmo caso do destino*.

    Por exemplo:

    | Esquema Oracle de origem | Banco de dados PostgreSQL de destino. esquema | DMS criou esquema. tabela. coluna |
    | ------------- | ------------- | ------------- |
    | HR | targetHR. público | Public. países. country_id |
    | HR | targetHR.trgthr | trgthr. países. country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"." PAÍSES "." COUNTRY_ID" |
    | HR | targetHR.HR | "HR". PAÍSES "." COUNTRY_ID" |
    | HR | targetHR.Hr | \* Não é possível mapear casos mistos |

    \* Para criar um esquema de caso misto e nomes de tabela no PostgreSQL [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com)de destino, contate. Podemos fornecer um script para configurar o esquema de tabela de casos misto no banco de dados PostgreSQL de destino.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar fornecedor de recursos](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Criar uma instância do DMS

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione uma VNet existente ou crie uma nova.

    A VNet fornece ao serviço de migração de banco de dados do Azure acesso ao Oracle de origem e à instância de destino do banco de dados do Azure para PostgreSQL.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

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
4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **Oracle**, na caixa de texto tipo de **servidor de destino** , selecione **banco de dados do Azure para PostgreSQL**.
5. Na seção **escolher tipo de atividade** , selecione **migração de dados online**.

   ![Criar Projeto do Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Como alternativa, você pode escolher **criar projeto somente** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **salvar**, observe os requisitos para usar com êxito o serviço de migração de banco de dados do Azure para executar uma migração online e, em seguida, selecione **criar e executar atividade**.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

* Na tela **adicionar detalhes de origem** , especifique os detalhes de conexão para a instância Oracle de origem.

  ![Ecrã Adicionar Detalhes da Origem](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Carregar o Driver Oracle OCI

1. Selecione **salvar**e, em seguida, na tela **Instalar driver de OCI** , entre na sua conta Oracle e baixe o driver **instantclient-basiclite-Windows. x64-12.2.0.1.0. zip** (37.128.586 byte (s)) (soma de verificação SHA1: 865082268) [aqui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Baixe o driver em uma pasta compartilhada.

   Verifique se a pasta é compartilhada com o nome de usuário que você especificou com o acesso de somente leitura mínimo. O serviço de migração de banco de dados do Azure acessa e lê do compartilhamento para carregar o driver de OCI no Azure, representando o nome de usuário que você especificar.

   O nome de usuário que você especificar deve ser uma conta do Windows.

   ![Instalação do driver de OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **salvar**e, em seguida, na tela **detalhes de destino** , especifique os detalhes de conexão do banco de dados do Azure de destino para o servidor PostgreSQL, que é a instância pré-configurada do banco de dados do Azure para PostgreSQL ao qual o esquema de **RH** foi Plant.

    ![Ecrã Detalhes do destino](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o banco de dados de destino contém o mesmo nome do banco de dados de origem, o serviço de migração de banco de dados do Azure seleciona o banco de dados de destino por padrão

    ![Mapa para as bases de dados de destino](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

  É apresentada a janela da atividade de migração e o **Estado** da atividade é **a inicializar**.

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

     ![Status da atividade-em execução](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Em **nome do banco**de dados, selecione um banco de dado específico para obter o status de migração para operações de sincronização de dados **incrementais** e **carregamento completo** .

    O Carregamento de dados completo irá mostrar o estado de migração de carregamento inicial, enquanto a Sincronização de dados incremental irá mostrar o estado de captura de dados alterados (CDC).

     ![Estado da Atividade - Carregamento completo concluído](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Estado da Atividade - Sincronização de dados incremental](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.

   ![Iniciar transferência](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selecione **Confirmar** e, em seguida, selecione **Aplicar**.
4. Quando o status de migração do banco de dados mostrar **concluído**, conecte seus aplicativos à nova instância de destino do banco de dados do Azure para PostgreSQL.

 > [!NOTE]
 > Como o PostgreSQL, por padrão, tem Schema. Table. Column em letras minúsculas, você pode reverter de maiúsculas para minúsculas usando o script na seção **Configurar o esquema no banco de dados do Azure para PostgreSQL** anteriormente neste artigo.

## <a name="next-steps"></a>Passos Seguintes

* Para obter informações sobre problemas conhecidos e limitações ao realizar migrações online para a Base de Dados do Azure para PostgreSQL, veja o artigo [Problemas conhecidos e soluções alternativas das migrações online da Base de Dados do Azure para PostgreSQL](known-issues-azure-postgresql-online.md).
* Para obter informações sobre o serviço de migração de banco de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Para obter informações sobre o banco de dados do Azure para PostgreSQL, consulte o artigo [o que é o banco de dados do Azure para PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
