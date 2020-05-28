---
title: 'Tutorial: Migrar o Servidor SQL offline para uma base de dados única SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server no local para uma única base de dados ou base de dados em conjunto na Base de Dados Azure SQL offline utilizando o Serviço de Migração de Bases de Dados Azure.
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
ms.openlocfilehash: 9eb5e5063a4aec69e1f21445cb5278caaea82ce2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020494"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Tutorial: Migrar o Servidor SQL para uma única base de dados ou base de dados agrupinda na Base de Dados Azure SQL offline usando DMS

Pode utilizar o Serviço de Migração de Bases de Dados Azure para migrar as bases de dados de uma instância do Servidor SQL no local para a Base de [Dados Azure SQL](https://docs.microsoft.com/azure/sql-database/). Neste tutorial, migra a base de dados **Adventureworks2012** restaurada para uma instância no local do SQL Server 2016 (ou mais tarde) para uma única base de dados ou base de dados pooled na Base de Dados Azure SQL utilizando o Serviço de Migração azure Database.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> - Utilizar o Assistente de Migração de Dados para avaliar a sua base de dados no local.
> - Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> - Crie uma instância do Azure Database Migration Service.
> - Crie um projeto de migração utilizando o Serviço de Migração de Bases de Dados Azure.
> - Executar a migração.
> - Monitorizar a migração.
> - Transferir um relatório da migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para uma única base de dados ou base de dados pooled na Base de Dados Azure SQL. Para uma migração online, veja [Migrar o SQL Server para a Base de Dados SQL do Azure online com o DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Descarregue e instale [o SQL Server 2016 ou mais tarde](https://www.microsoft.com/sql-server/sql-server-downloads).
- Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).
- Crie uma única base de dados (ou reunida) na Base de Dados Azure SQL, o que faz seguindo os detalhes do artigo Criar uma única base de dados na Base de [Dados Azure SQL utilizando o portal Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se utilizar os Serviços de Integração de Servidores SQL (SSIS) e pretender migrar a base de dados do catálogo para os seus projetos/pacotes SSIS (SSISDB) do SQL Server para a Base de Dados SQL Azure, o destino SSISDB será criado e gerido automaticamente em seu nome quando fornecer SSIS na Azure Data Factory (ADF). Para obter mais informações sobre pacotes SSIS migratórios, consulte o artigo Pacotes de Serviços de Integração de [Servidores SQL para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o peering da rede para a Microsoft, adicione os [seguintes pontos finais](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à subnet na qual o serviço será aprovisionado:
    >
    > - Ponto final da base de dados alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > - Ponto final de armazenamento
    > - Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração de Bases de Dados Azure carece de conectividade na Internet.
    >
    >Se não tiver conectividade site-a-site entre a rede no local e o Azure ou se houver uma largura de banda de conectividade de ligação local-local limitada, considere utilizar o Serviço de Migração de Bases de Dados Azure em modo híbrido (Pré-visualização). O modo híbrido aproveita um trabalhador migratório no local juntamente com uma instância do Serviço de Migração de Bases de Dados Azure que funciona na nuvem. Para criar uma instância do Serviço de Migração de Bases de Dados Azure em modo híbrido, consulte o artigo Criar uma instância do Serviço de Migração de Bases [de Dados Azure em modo híbrido utilizando o portal Azure](https://aka.ms/dms-hybrid-create).

- Certifique-se de que as regras do Grupo de Segurança da Rede Virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual Azure NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)de segurança da rede .
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra a sua firewall windows para permitir que o Serviço de Migração da Base de Dados Azure aceda à fonte Do Servidor SQL, que por padrão é a porta TCP 1433.
- Se estiver a executar várias instâncias de Servidor SQL com portas dinâmicas, poderá desejar ativar o Serviço de Navegador SQL e permitir o acesso à porta UDP 1434 através das suas firewalls para que o Serviço de Migração da Base de Dados Azure possa ligar-se a uma instância nomeada no seu servidor de origem.
- Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à base de dados de origem para migração.
- Crie uma regra de [firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) IP ao nível do servidor para a Base de Dados Azure SQL para permitir o acesso do Serviço de Migração de Bases de Dados Azure às bases de dados-alvo. Forneça a gama de subredes da rede virtual utilizada para o Serviço de Migração de Bases de Dados Azure.
- Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.

## <a name="assess-your-on-premises-database"></a>Avaliar a base de dados no local

Antes de poder migrar dados de uma instância do SQL Server no local para uma única base de dados ou base de dados pooled na Base de Dados Azure SQL, é necessário avaliar a base de dados do SQL Server para quaisquer problemas de bloqueio que possam impedir a migração. Ao utilizar o Assistente de Migração de Dados v3.3 ou posterior, siga os passos descritos no artigo [Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) (Realizar uma avaliação da migração do SQL Server para concluir a avaliação da base de dados no local. Segue-se um resumo dos passos necessários:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e selecione o tipo de projeto **Assessment** (Avaliação).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server**, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure) e, em seguida, selecione **Create** (Criar) para criar o projeto.

    Ao avaliar a base de dados do SQL Server de origem que migra para uma única base de dados ou base de dados pooled na Base de Dados Azure SQL, pode escolher um ou ambos os seguintes tipos de relatórios de avaliação:

   - Verificar a compatibilidade da base de dados
   - Verificar a paridade de funcionalidades

    Ambos os tipos de relatórios estão selecionados por predefinição.

3. No Assistente de Migração de Dados, no ecrã **Options** (Opções), selecione **Next** (Seguinte).
4. No ecrã **Select sources** (Selecionar origens), na caixa de diálogo **Connect to a server** (Ligar a um servidor), indique os detalhes da ligação ao SQL Server e selecione **Connect** (Ligar).
5. Na caixa de diálogo **Add sources** (Adicionar origens), selecione **AdventureWorks2012**, selecione **Add** (Adicionar) e, em seguida, selecione **Start Assessment** (Iniciar Avaliação).

    > [!NOTE]
    > Se utilizar o SSIS, o DMA não suporta atualmente a avaliação da fonte SSISDB. No entanto, os projetos/pacotes SSIS serão avaliados/validados à medida que forem redistribuídos para o destino SSISDB hospedado pela Base de Dados Azure SQL. Para obter mais informações sobre pacotes SSIS migratórios, consulte o artigo Pacotes de Serviços de Integração de [Servidores SQL para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Quando a avaliação estiver concluída, os resultados são apresentados tal como no gráfico abaixo:

    ![Avaliar migração de dados](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Para bases de dados únicas ou bases de dados agruparadas na Base de Dados Azure SQL, as avaliações identificam problemas de paridade de funcionalidades e problemas de bloqueio de migração para implantação numa única base de dados ou base de dados agrunada.

    - A categoria **paridade de funcionalidades do SQL Server** oferece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e passos de migração para o ajudar a planear o esforço necessário para os seus projetos de migração.
    - A categoria **Problemas de compatibilidade** identifica funcionalidades parcialmente suportadas ou não suportadas que refletem problemas de compatibilidade que poderão impedir a migração de bases de dados do SQL Server no local para a Base de Dados SQL do Azure. As recomendações também são disponibilizadas para o ajudar a resolver esses problemas.

6. Selecione as opções relevantes para rever os resultados da avaliação relativamente a problemas de bloqueio que impedem a migração e problemas de paridade de funcionalidades.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois de se sentir confortável com a avaliação e satisfeito por a base de dados selecionada ser um candidato viável para a migração para uma única base de dados ou base de dados pooled na Base de Dados Azure SQL, use DMA para migrar o esquema para a Base de Dados Azure SQL.

> [!NOTE]
> Antes de poder criar um projeto de migração no Assistente de Migração de Dados, confirme que já aprovisionou uma base de dados SQL do Azure, conforme mencionado nos pré-requisitos. Para efeitos deste tutorial, pressupõe-se que o nome da Base de Dados SQL do Azure é **AdventureWorksAzure**, mas pode indicar um nome à sua escolha.

> [!IMPORTANT]
> Se utilizar o SSIS, o DMA não suporta atualmente a migração de origem SSISDB, mas pode reimplantar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela Azure SQL Database. Para obter mais informações sobre pacotes SSIS migratórios, consulte o artigo Pacotes de Serviços de Integração de [Servidores SQL para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Para migrar o esquema **AdventureWorks2012** para uma única base de dados ou base de dados pooled Azure SQL Database, execute os seguintes passos:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3. Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do Assistente de Migração de Dados deverá ser apresentada, conforme mostrado na imagem abaixo:

    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecione **Create** (Criar) para criar o projeto.
5. No Assistente de Migração de Dados, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecione **Next**, em **Connect to target server**, especifique os detalhes de ligação do alvo para a Base de Dados Azure SQL, selecione **Connect**, e, em seguida, selecione a base de dados **AdventureWorksAzure** que tinha pré-provisionado na Base de Dados Azure SQL.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecione **Next** (Seguinte) para avançar para o ecrã **Select objects** (Selecionar objetos), no qual pode especificar os objetos do esquema na base de dados **AdventureWorks2012** que têm de ser implementados na Base de Dados SQL do Azure.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

    ![Implementar o esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no Portal do Azure. Procure e selecione **Subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Procure a migração e, em seguida, selecione **Register** for **Microsoft.DataMigration**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**. Procure e selecione O Serviço de Migração da Base de **Dados Azure**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do Serviço de Migração de Bases de Dados Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte SQL Server e à instância de base de dados Azure SQL.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure.](https://aka.ms/DMSVnet)

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No menu do portal Azure, selecione **Todos os serviços.** Procure e selecione Serviços de Migração de Bases de **Dados Azure**.

     ![Localize todas as instâncias do Serviço de Migração da Base de Dados Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. No ecrã dos Serviços de Migração da Base de **Dados Azure,** selecione a instância do Serviço de Migração da Base de Dados Azure que criou.

3. Selecione **Novo Projeto de Migração**.

     ![Localize a sua instância do Serviço de Migração de Bases de Dados Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Base de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação da instância do SQL Server de origem.

    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem. Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas utilizando um certificado auto-assinado não proporcionam uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Se utilizar o SSIS, o DMS não suporta atualmente a migração de origem SSISDB, mas pode reimplantar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela Azure SQL Database. Para obter mais informações sobre pacotes SSIS migratórios, consulte o artigo Pacotes de Serviços de Integração de [Servidores SQL para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Save**, e depois no ecrã de detalhes do **alvo da Migração,** especifique os detalhes de ligação para o target Azure SQL Database, que é a base de dados Azure SQL pré-aprovisionada para a qual o esquema **AdventureWorks2012** foi implementado através do Assistente de Migração de Dados.

    ![Selecionar o Destino](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Serviço de Migração da Base de Dados Azure seleciona a base de dados de destino por padrão.

    ![Mapear para as bases de dados de destino](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selecione **Guardar**, no ecrã **Selecionar tabelas**, expanda a lista de tabelas e reveja a lista dos campos afetados.

    O Serviço de Migração azure Database seleciona todas as tabelas de origem vazias que existem na instância de base de dados Azure SQL. Se quiser voltar a emigrar tabelas que já incluem dados, tem de selecionar explicitamente as tabelas neste painel.

    ![Selecionar tabelas](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

5. Expanda a secção **Opção de validação** para ver o ecrã **Escolher opção de validação** e especifique se as bases de dados migradas vão ser validadas relativamente a **Comparação de esquema**, **Consistência de dados** e **Exatidão de consultas**.

    ![Escolher a opção de validação](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Selecione **Guardar** e reveja o resumo para confirmar que os detalhes de origem e destino correspondem ao que especificou anteriormente.

    ![Resumo da migração](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Executar a migração

- Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **Pendente**.

    ![Estado da Atividade](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

    ![Estado da Atividade Concluída](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Após a conclusão da migração, selecione **Transferir relatório** para obter um relatório que liste os detalhes associados ao processo de migração.

3. Verifique a base de dados de destino na base de dados Azure SQL.

### <a name="additional-resources"></a>Recursos adicionais

- [Migração SQL usando o Serviço](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) de Migração de Dados Azure laboratório prático.
- Para obter informações sobre questões e limitações conhecidas ao realizar migrações online para a Base de Dados Azure SQL, consulte o artigo [Questões conhecidas e seleções com migrações online Azure SQL Database](known-issues-azure-sql-online.md).
- Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo O que é o Serviço de Migração da Base de [Dados Azure?](https://docs.microsoft.com/azure/dms/dms-overview)
- Para obter informações sobre a Base de Dados Azure SQL, consulte o artigo O que é o serviço de base de [dados Azure SQL?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)
