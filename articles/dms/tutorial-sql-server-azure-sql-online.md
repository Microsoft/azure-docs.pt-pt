---
title: 'Tutorial: Usar o serviço de migração de banco de dados do Azure para migrar online de SQL Server para um banco de dados único/em pool no banco de dados SQL Microsoft Docs'
description: Aprenda a executar uma migração online de SQL Server local para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/22/2019
ms.openlocfilehash: 619c36257f9166492e98d88335d767f358e3feca
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179118"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Tutorial: Migrar SQL Server para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure online usando DMS

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância do SQL Server no local para a [Base de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/) com um período de indisponibilidade reduzido. Neste tutorial, você migra o banco de dados **Adventureworks2012** restaurado para uma instância local do SQL Server 2016 (ou posterior) para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> - Utilizar o Assistente de Migração de Dados para avaliar a sua base de dados no local.
> - Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> - Criar uma instância do Azure Database Migration Service.
> - Utilizar o Azure Database Migration Service para criar um projeto de migração.
> - Executar a migração.
> - Monitorizar a migração.
> - Transferir um relatório da migração.

> [!NOTE]
> Usar o serviço de migração de banco de dados do Azure para executar uma migração online requer a criação de uma instância com base no tipo de preço premium. Para obter mais informações, consulte a página [preços](https://azure.microsoft.com/pricing/details/database-migration/) do serviço de migração de banco de dados do Azure.

> [!IMPORTANT]
> Para uma experiência de migração otimizada, a Microsoft recomenda a criação de uma instância do Azure Database Migration Service na mesma região do Azure que a base de dados de destino. Mover dados entre regiões ou geografias pode retardar o processo de migração e introduzir erros.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração online do SQL Server para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure. Para uma migração offline, veja [Migrar o SQL Server para a Base de Dados SQL do Azure offline com o DMS](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Baixe e instale o [SQL Server 2012 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads).
- Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).
- Crie um banco de dados único (ou em pool) no banco de dados SQL do Azure, que você faz seguindo os detalhes no artigo [criar um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se você usar SQL Server Integration Services (SSIS) e desejar migrar o banco de dados de catálogo para seus projetos/pacotes SSIS (SSISDB) de SQL Server para o banco de dados SQL do Azure, o destino SSISDB será criado e gerenciado automaticamente em seu nome quando você Provisione o SSIS no Azure Data Factory (ADF). Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

- Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 ou posterior.
- Crie uma VNet (rede virtual) do Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou a [VPN ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma VNet, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da VNet, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.

- Confirme que as regras de Grupos de Segurança de Rede da VNet não bloqueiam as seguintes portas de comunicação de entrada para o Azure Database Migration Service: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG VNet do Azure, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abrir a firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
- Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
- Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Forneça o intervalo de sub-rede da VNet usada para o serviço de migração de banco de dados do Azure.
- Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.
- A versão do SQL Server de origem tem de ser o SQL Server 2005 e superior. Para determinar a versão que a instância do SQL Server está a executar, leia o artigo [Como determinar a versão, edição e nível de atualização do SQL Server e respetivos componentes](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- As bases de dados têm de estar em modo de recuperação Completa ou Registada em massa. Para determinar o modelo de recuperação configurado na sua instância do SQL Server, leia o artigo [Ver ou Alterar o Modelo de Recuperação de uma Base de Dados (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Certifique-se de que faz Cópias de segurança de base de dados completas para as bases de dados. Para criar um backup de banco de dados completo, [consulte o artigo como: Criar um backup de banco de dados completo (Transact](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105))-SQL).
- Se alguma uma das tabelas não tiver uma chave primária, ative a Captura de Dados Alterados (CDC) na(s) base(s) de dados e tabela(s) específica(s).
    > [!NOTE]
    > Pode utilizar o script abaixo para encontrar as tabelas que não têm chaves primárias.
    
    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
    ```

    Se os resultados mostrarem uma ou mais tabelas com "is_tracked_by_cdc", como "0", ative Alterar captura para a base de dados e para as tabelas específicas, através do processo descrito no artigo [Ativar e Desativar a Captura de Dados Alterados (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017).

- Configure a função de distribuidor para o SQL Server de origem.

    >[!NOTE]
    > Pode determinar se os componentes de replicação estão instalados com a consulta abaixo.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Se o resultado devolver uma mensagem de erro a sugerir a instalação dos componentes de replicação, instale os componentes de replicação do SQL Server com o processo no artigo [Instalar replicação do SQL Server](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Se a replicação já estiver instalada, verifique se a função de distribuição está configurada no SQL Server de origem com o comando T-SQL abaixo.

    ```sql
    EXEC sp_get_distributor;
    ```

    Se a distribuição não estiver configurada, onde o servidor de distribuição mostrar NULO para a saída do comando acima, configure a distribuição com a documentação de orientação indicada no artigo [Configurar Distribuição](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Desative os acionadores de base de dados na Base de Dados SQL do Azure de destino.
    >[!NOTE]
    > Pode encontrar os acionadores de base de dados na Base de Dados SQL do Azure com a seguinte consulta:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Para obter mais informações, leia o artigo [DESATIVAR ACIONADOR (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Avaliar a base de dados no local

Antes de poder migrar dados de uma instância de SQL Server local para um banco de dado único ou banco de dados em pool no banco de dados SQL do Azure, você precisa avaliar o banco de dados SQL Server para qualquer problema de bloqueio que possa impedir a migração. Ao utilizar o Assistente de Migração de Dados v3.3 ou posterior, siga os passos descritos no artigo [Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) (Realizar uma avaliação da migração do SQL Server para concluir a avaliação da base de dados no local.

Para avaliar um banco de dados local, execute as seguintes etapas:

1. No DMA, selecione o ícone Novo (+) e selecione o tipo de projeto **Avaliação**.
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server**, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure) e, em seguida, selecione **Create** (Criar) para criar o projeto.

   Ao avaliar o banco de dados de SQL Server de origem migrando para um banco de dados individual ou um banco de dados em pool no banco de dados SQL do Azure, você pode escolher um ou ambos os seguintes tipos de relatório de avaliação:

   - Verificar a compatibilidade de base de dados
   - Verificar a paridade de funcionalidades

   Ambos os tipos de relatórios estão selecionados por predefinição.

3. No DMA, no ecrã **Opções**, selecione **Seguinte**.
4. No ecrã **Select sources** (Selecionar origens), na caixa de diálogo **Connect to a server** (Ligar a um servidor), indique os detalhes da ligação ao SQL Server e selecione **Connect** (Ligar).
5. Na caixa de diálogo **Add sources** (Adicionar origens), selecione **AdventureWorks2012**, selecione **Add** (Adicionar) e, em seguida, selecione **Start Assessment** (Iniciar Avaliação).

    > [!NOTE]
    > Se você usar o SSIS, o DMA não oferece atualmente suporte à avaliação da origem SSISDB. No entanto, os projetos/pacotes do SSIS serão avaliados/validados à medida que forem reimplantados no SSISDB de destino hospedado pelo banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Quando a avaliação estiver concluída, os resultados são apresentados tal como no gráfico abaixo:

    ![Avaliar migração de dados](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Para bancos de dados individuais ou bancos de dados em pool no banco de dados SQL do Azure, as avaliações identificam problemas de paridade de recursos e problemas de bloqueio de migração para a implantação em um banco de dados individual ou em um banco de dados em pool.

    - A categoria **paridade de funcionalidades do SQL Server** oferece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e passos de migração para o ajudar a planear o esforço necessário para os seus projetos de migração.
    - A categoria **Problemas de compatibilidade** identifica funcionalidades parcialmente suportadas ou não suportadas que refletem problemas de compatibilidade que poderão impedir a migração de bases de dados do SQL Server no local para a Base de Dados SQL do Azure. As recomendações também são disponibilizadas para o ajudar a resolver esses problemas.

6. Selecione as opções relevantes para rever os resultados da avaliação relativamente a problemas de bloqueio que impedem a migração e problemas de paridade de funcionalidades.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois que você estiver familiarizado com a avaliação e estiver convencido de que o banco de dados selecionado é um candidato viável para migração para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure, use DMA para migrar o esquema para o banco de dados SQL do Azure.

> [!NOTE]
> Antes de poder criar um projeto de migração no DMA, confirme que já aprovisionou uma base de dados SQL do Azure, conforme mencionado nos pré-requisitos. Para efeitos deste tutorial, pressupõe-se que o nome da Base de Dados SQL do Azure é **AdventureWorksAzure**, mas pode indicar um nome à sua escolha.

> [!IMPORTANT]
> Se você usar o SSIS, o DMA não oferece atualmente suporte à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes do SSIS no SSISDB de destino armazenado pelo banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Para migrar o esquema **AdventureWorks2012** para um banco de dados individual ou banco de dados SQL do Azure, execute as seguintes etapas:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3. Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do DMA deverá ser apresentada, conforme mostrado na imagem abaixo:

    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Selecione **Create** (Criar) para criar o projeto.
5. No DMA, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Selecione **Next** (Seguinte), em **Connect to target server** (Ligar a servidor de destino), especifique os detalhes da ligação de destino da base de dados SQL do Azure, selecione **Connect** (Ligar) e, em seguida, selecione a base de dados **AdventureWorksAzure** que pré-aprovisionou na Base de Dados SQL do Azure.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Selecione **Next** (Seguinte) para avançar para o ecrã **Select objects** (Selecionar objetos), no qual pode especificar os objetos do esquema na base de dados **AdventureWorks2012** que têm de ser implementados na Base de Dados SQL do Azure.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

    ![Implementar o esquema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar fornecedor de recursos](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Criar uma instância

1. No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do Azure Database Migration Service. 

5. Selecione uma VNet existente ou crie uma nova.

    A VNet fornece ao serviço de migração de banco de dados do Azure acesso ao SQL Server de origem e à instância de destino do banco de dados SQL do Azure.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Selecione **criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.

    ![Localizar a instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Selecione + **Novo Projeto de Migração**.
4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Base de Dados SQL do Azure**.
5. Na seção **escolher tipo de atividade** , selecione **migração de dados online**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Como alternativa, você pode escolher **criar projeto somente** para criar o projeto de migração agora e executar a migração mais tarde.

6. Selecione **Guardar**.

7. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

    ![Criar e Executar uma Atividade do Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação da instância do SQL Server de origem.

    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem. Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Se você usar o SSIS, o DMS atualmente não oferece suporte à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes do SSIS no SSISDB de destino armazenado pelo banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar** e, no ecrã, **Detalhes do destino da migração**, especifique os detalhes da ligação do servidor da Base de Dados SQL do Azure de destino, que é a Base de Dados SQL do Azure pré-aprovisionada na qual o esquema de **AdventureWorks2012** foi implementada com o DMA.

    ![Selecionar o Destino](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Selecione **Guardar**, no ecrã **Selecionar tabelas**, expanda a lista de tabelas e reveja a lista dos campos afetados.

    O Azure Database Migration Service seleciona automaticamente todas as tabelas de origem vazias que existem na instância da Base de Dados SQL do Azure de destino. Se quiser voltar a emigrar tabelas que já incluem dados, tem de selecionar explicitamente as tabelas neste painel.

    ![Selecionar tabelas](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, especifique um nome para a atividade de migração e, em seguida, reveja o resumo para garantir que os detalhes de origem e de destino correspondem aos que foram anteriormente especificados.

    ![Resumo da migração](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Executar a migração

- Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **A inicializar**.

    ![Estado da Atividade - a inicializar](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Em execução**.

2. Clique numa base de dados específica para obter o estado da migração para as operações **Carregamento de dados completo** e **Sincronização de dados incrementais**.

    ![Estado da Atividade - em progresso](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Realizar transferência de migração

Depois de concluída a Carga completa inicial, as bases de dados são marcadas como **Preparadas para transferência**.

1. Quando estiver pronto para concluir a migração da base de dados, selecione **Iniciar Transferência**.

    ![Iniciar transferência](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Certifique-se de que para todas as transações de entrada para a base de dados de origem; aguarde até que o contador **Alterações pendentes** mostre **0**.
3. Selecione **Confirmar** e depois **Aplicar**.
4. Quando o estado da migração de base de dados apresentar **Concluído**, ligue as suas aplicações à nova Base de Dados SQL do Azure de destino.

    ![Estado da Atividade - concluído](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Passos seguintes

- Laboratório prático [SQL migration using Azure Data Migration Service (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) hands-on lab (Utilizar o Azure Database Migration Service (DMS) para a migração de SQL).
- Para obter informações sobre problemas conhecidos e limitações ao executar migrações online para o banco de dados SQL do Azure, consulte o artigo [problemas conhecidos e soluções alternativas com migrações online do banco de dados SQL do Azure](known-issues-azure-sql-online.md).
- Para obter informações sobre o Azure Database Migration Service, leia o artigo [O que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Para obter informações sobre o banco de dados SQL do Azure, consulte o artigo o [que é o serviço de banco de dados SQL do Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
