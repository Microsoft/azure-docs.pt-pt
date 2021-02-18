---
title: 'Tutorial: Migrar o SQL Server offline para uma base de dados única SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server para o Azure SQL Database offline utilizando o Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/03/2021
ms.openlocfilehash: 6c5d758f94a4ad4e70a8b02a02c7c61097725f63
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094835"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Tutorial: Migrar o SQL Server para a Base de Dados SQL do Azure offline com o DMS

Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância do SQL Server para a Base de [Dados Azure SQL](/azure/sql-database/). Neste tutorial, migra a base de dados [Adventureworks2016](/sql/samples/adventureworks-install-configure?tabs=ssms&view=sql-server-ver15#download-backup-files) restaurada para uma instância no local do SQL Server 2016 (ou mais tarde) para uma única base de dados ou base de dados em Azure SQL Database, utilizando o Azure Database Migration Service.

Vai aprender a:
> [!div class="checklist"]
>
> - Avalie e avalie a sua base de dados no local para quaisquer problemas de bloqueio utilizando o Assistente de Migração de Dados.
> - Utilize o Assistente de Migração de Dados para migrar o esquema de amostra de base de dados. 
> - Registe o fornecedor de recursos Azure DataMigration.
> - Crie uma instância do Azure Database Migration Service.
> - Crie um projeto de migração utilizando o Serviço de Migração da Base de Dados Azure.
> - Executar a migração.
> - Monitorizar a migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para uma base de dados na Base de Dados Azure SQL. Para uma migração online, veja [Migrar o SQL Server para a Base de Dados SQL do Azure online com o DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Descarregue e instale [o SQL Server 2016 ou mais tarde](https://www.microsoft.com/sql-server/sql-server-downloads).
- Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).
- Crie uma base de dados na Base de Dados Azure SQL, o que faz seguindo os detalhes do artigo [Criar uma base de dados na Base de Dados Azure SQL utilizando o portal Azure](../azure-sql/database/single-database-create-quickstart.md). Para efeitos deste tutorial, pressupõe-se que o nome da Base de Dados SQL do Azure é **AdventureWorksAzure**, mas pode indicar um nome à sua escolha.

    > [!NOTE]
    > Se utilizar os Serviços de Integração de Servidores SQL (SSIS) e quiser migrar a base de dados do catálogo para os seus projetos/pacotes SSIS (SSISDB) do SQL Server para a Azure SQL Database, o destino SSISDB será criado e gerido automaticamente em seu nome quando forrese SSIS na Azure Data Factory (ADF). Para obter mais informações sobre os pacotes SSIS migratórios, consulte o artigo [Pacotes de Serviços de Integração de Servidores SQL migratórios para o Azure](./how-to-migrate-ssis-packages.md).
  
- Descarregue e instale a versão mais recente do [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595).
- Crie uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando [expressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração da rede virtual, se utilizar o ExpressRoute com o olhar de rede para a Microsoft, adicione os [seguintes pontos finais](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço à sub-rede em que o serviço será abastecado:
    >
    > - Ponto final da base de dados-alvo (por exemplo, ponto final SQL, ponto final cosmos DB, e assim por diante)
    > - Ponto final de armazenamento
    > - Ponto final do ônibus de serviço
    >
    > Esta configuração é necessária porque o Serviço de Migração da Base de Dados Azure carece de conectividade com a Internet.
    >
    >Se não tiver conectividade site-to-site entre a rede no local e a Azure ou se houver uma largura de banda de conectividade local-local limitada, considere a utilização do Serviço de Migração da Base de Dados Azure em modo híbrido (Preview). O modo híbrido aproveita um trabalhador de migração no local, juntamente com um exemplo do Serviço de Migração da Base de Dados Azure a funcionar na nuvem. Para criar um exemplo do Serviço de Migração da Base de Dados Azure em modo híbrido, consulte o artigo [Criar uma instância do Serviço de Migração da Base de Dados Azure em modo híbrido utilizando o portal Azure](./quickstart-create-data-migration-service-hybrid-portal.md).

- Certifique-se de que as regras de segurança de saída do Grupo de Segurança da Rede Virtual não bloqueiam a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual Azure, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra a firewall do Windows para permitir que o Serviço de Migração da Base de Dados de Azure aceda ao servidor SQL de origem, que por padrão é a porta TCP 1433. Se o seu caso padrão estiver a ouvir noutra porta, adicione-o à firewall.
- Se estiver a executar várias instâncias chamadas SQL Server utilizando portas dinâmicas, pode desejar ativar o Serviço de Navegador SQL e permitir o acesso à porta UDP 1434 através das suas firewalls para que o Serviço de Migração da Base de Dados Azure possa ligar-se a uma instância nomeada no seu servidor de origem.
- Ao utilizar um aparelho de firewall em frente à sua base de dados de origem, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base de dados de origem para migração.
- Crie uma regra de [firewall](../azure-sql/database/firewall-configure.md) IP de nível de servidor para Azure SQL Database para permitir o acesso do Serviço de Migração da Base de Dados Azure às bases de dados-alvo. Forneça a gama de sub-redes da rede virtual utilizada para o Serviço de Migração da Base de Dados Azure.
- Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Certifique-se de que as credenciais utilizadas para se ligar ao target Azure SQL Database instance têm permissão [de BASE DE DADOS DE CONTROLO](/sql/t-sql/statements/grant-database-permissions-transact-sql) nas bases de dados-alvo.

## <a name="assess-your-on-premises-database"></a>Avaliar a base de dados no local

Antes de poder migrar dados de uma instância do SQL Server para uma única base de dados ou base de dados agram na Base de Dados Azure SQL, é necessário avaliar a base de dados do SQL Server para quaisquer problemas de bloqueio que possam impedir a migração. Utilizando o Assistente de Migração de Dados, siga os passos descritos no artigo [Realizando uma avaliação de migração do SQL Server](/sql/dma/dma-assesssqlonprem) para completar a avaliação da base de dados no local. Segue-se um resumo dos passos necessários:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e selecione o tipo de projeto **Assessment** (Avaliação).
2. Especifique um nome de projeto. A partir da lista de down down **do tipo de avaliação,** selecione o **Motor de Base de Dados**, na caixa de texto **do tipo de servidor Fonte,** selecione **SQL Server**, na caixa de texto do tipo **alvo,** selecione **Azure SQL Database** e, em seguida, selecione **Criar** para criar o projeto.

    Quando estiver a avaliar a base de dados do SQL Server de origem migrando para uma única base de dados ou base de dados agrida na Base de Dados Azure SQL, pode escolher um ou ambos os seguintes tipos de relatórios de avaliação:

   - Verificar a compatibilidade da base de dados
   - Verificar a paridade de funcionalidades

    Ambos os tipos de relatórios estão selecionados por predefinição.

3. No Assistente de Migração de Dados, no ecrã **Options** (Opções), selecione **Next** (Seguinte).
4. No ecrã **Select sources** (Selecionar origens), na caixa de diálogo **Connect to a server** (Ligar a um servidor), indique os detalhes da ligação ao SQL Server e selecione **Connect** (Ligar).
5. Na caixa de diálogo **'Adicionar fontes',** selecione **Adventureworks2016,** selecione **Add**, e, em seguida, selecione **Start Assessment**.

    > [!NOTE]
    > Se utilizar o SSIS, o DMA não suporta atualmente a avaliação da fonte SSISDB. No entanto, os projetos/pacotes SSIS serão avaliados/validados à medida que forem redistribuídos para o destino SSISDB hospedado pela Base de Dados Azure SQL. Para obter mais informações sobre os pacotes SSIS migratórios, consulte o artigo [Pacotes de Serviços de Integração de Servidores SQL migratórios para o Azure](./how-to-migrate-ssis-packages.md).

    Quando a avaliação estiver concluída, os resultados são apresentados tal como no gráfico abaixo:

    ![Avaliar migração de dados](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Para bases de dados na Base de Dados Azure SQL, as avaliações identificam problemas de paridade de recursos e problemas de bloqueio de migração para implantação numa única base de dados ou base de dados agrária.

    - A categoria **paridade de funcionalidades do SQL Server** oferece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e passos de migração para o ajudar a planear o esforço necessário para os seus projetos de migração.
    - A categoria **de problemas de compatibilidade** identifica funcionalidades parcialmente suportadas ou não suportadas que refletem problemas de compatibilidade que podem bloquear a migração da base de dados sql server(s) para a Base de Dados Azure SQL. As recomendações também são disponibilizadas para o ajudar a resolver esses problemas.

6. Selecione as opções relevantes para rever os resultados da avaliação relativamente a problemas de bloqueio que impedem a migração e problemas de paridade de funcionalidades.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois de se sentir confortável com a avaliação e satisfeito que a base de dados selecionada é um candidato viável para a migração para uma única base de dados ou base de dados agram na Base de Dados Azure SQL, utilize o DMA para migrar o esquema para a Base de Dados Azure SQL.

> [!NOTE]
> Antes de criar um projeto de migração no Data Migration Assistant, certifique-se de que já aprovisionou uma base de dados em Azure, conforme mencionado nos pré-requisitos. 

> [!IMPORTANT]
> Se utilizar o SSIS, o DMA não suporta atualmente a migração da fonte SSISDB, mas pode recolocar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela Base de Dados Azure SQL. Para obter mais informações sobre os pacotes SSIS migratórios, consulte o artigo [Pacotes de Serviços de Integração de Servidores SQL migratórios para o Azure](./how-to-migrate-ssis-packages.md).

Para migrar o esquema **Adventureworks2016** para uma única base de dados ou base de dados agram Azure SQL Database, execute os seguintes passos:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3. Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do Assistente de Migração de Dados deverá ser apresentada, conforme mostrado na imagem abaixo:

    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecione **Create** (Criar) para criar o projeto.
5. No Assistente de Migração de Dados, especifique os dados de ligação de origem para o seu Servidor SQL, selecione **Connect** e, em seguida, selecione a base **de dados Adventureworks2016.**

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecione **Seguinte**, em **Connect to target server**, especifique os detalhes da ligação alvo para a Base de Dados Azure SQL, selecione **Connect** e, em seguida, selecione a base de dados **AdventureWorksAzure** que tinha pré-a ser provisionada na Base de Dados Azure SQL.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecione **Seguinte** para avançar para o ecrã **de objetos Select,** no qual pode especificar os objetos de esquema na base de dados **Adventureworks2016** que precisam de ser implantados na Base de Dados Azure SQL.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

    ![Implementar o esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no Portal do Azure. Procure e **selecione Subscrições.**

   ![Mostrar subscrições no portal](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Procure por migração e, em seguida, **selecione Register** for **Microsoft.DataMigration**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**. Procure e selecione **O Serviço de Migração da Base de Dados Azure.**

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. No ecrã básico do **Serviço de Migração Create:**

     - Selecione uma subscrição.
     - Crie um novo grupo de recursos ou escolha um existente.
     - Especifique um nome para o exemplo do Serviço de Migração da Base de Dados Azure.
     - Selecione a localização em que pretende criar a instância do Serviço de Migração da Base de Dados Azure.
     - Escolha **Azure** como o modo de serviço.
     - Selecione um escalão de preço. Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar a azure Database Migration Service definições básicas](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

     - Selecione **Seguinte: Rede**.

4. No ecrã de rede **do Serviço de Migração Create:**

    - Selecione uma rede virtual existente ou crie uma nova. A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e à instância de base de dados Azure SQL. Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

    ![Configurar configurações de rede do Serviço de Migração de Bases de Dados Azure](media/tutorial-sql-server-to-azure-sql/dms-settings3.png)

    - Selecione **Review + Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No menu do portal Azure, selecione **Todos os serviços**. Procure e selecione **Azure Database Migration Services**.

     ![Localizar todas as instâncias do Serviço de Migração da Base de Dados de Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. No ecrã **Azure Database Migration Services,** selecione a instância do Serviço de Migração da Base de Dados Azure que criou.

3. Selecione **Novo Projeto de Migração**.

     ![Localize o seu caso de Serviço de Migração de Bases de Dados de Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Base de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **de origem Select,** especifique os detalhes da ligação para a origem SQL Server.

    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem. Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas usando um certificado auto-assinado não fornecem uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

    > [!IMPORTANT]
    > Se utilizar o SSIS, o DMS não suporta atualmente a migração da fonte SSISDB, mas pode recolocar os seus projetos/pacotes SSIS para o destino SSISDB hospedado pela Base de Dados Azure SQL. Para obter mais informações sobre os pacotes SSIS migratórios, consulte o artigo [Pacotes de Serviços de Integração de Servidores SQL migratórios para o Azure](./how-to-migrate-ssis-packages.md).

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

3. Selecione **Seguinte: Selecione o alvo**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã **alvo Select,** especifique os detalhes de ligação para a Base de Dados Azure SQL alvo, que é a Base de Dados Azure SQL pré-a provisionada para a qual o esquema **Adventureworks2016** foi implementado utilizando o Assistente de Migração de Dados.

    ![Selecionar o Destino](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)
    
    > [!NOTE]
    > As ligações privadas de ponto final à base de dados Azure SQL são suportadas pelo Serviço de Migração de Bases de Dados Azure, exceto quando se utiliza um nome DNS personalizado. 

2. Selecione **Seguinte: Mapear para o ecrã das bases de dados alvo,** mapear a fonte e a base de dados-alvo para a migração.

    Se a base de dados-alvo contiver o mesmo nome de base de dados que a base de dados de origem, o Azure Database Migration Service seleciona a base de dados-alvo por predefinição.

    ![Mapear para as bases de dados de destino](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selecione **Seguinte: Configurar as definições de migração,** expandir a listagem de tabelas e, em seguida, rever a lista de campos afetados.

    O Azure Database Migration Service seleciona todas as tabelas de origem vazias existentes na instância target Azure SQL Database. Se quiser voltar a emigrar tabelas que já incluem dados, tem de selecionar explicitamente as tabelas neste painel.

    ![Selecionar tabelas](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Selecione **Seguinte: Resumo,** reveja a configuração de migração e na caixa de texto **do nome de Atividade,** especifique um nome para a atividade de migração.

    ![Escolher a opção de validação](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

## <a name="run-the-migration"></a>Executar a migração

- Selecione **Migração iniciar**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **Pendente**.

    ![Estado da Atividade](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração

1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

    ![Estado da Atividade Concluída](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Verifique a base de dados-alvo na base de **dados Azure SQL target**.

### <a name="additional-resources"></a>Recursos adicionais

- [Migração SQL usando O Serviço de Migração de Dados Azure.](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587)
- Para obter informações sobre questões e limitações conhecidas ao realizar migrações on-line para Azure SQL Database, consulte o artigo [Questões conhecidas e soluções alternativas com migrações on-line Azure SQL Database](known-issues-azure-sql-online.md).
- Para obter informações sobre o Serviço de Migração da Base de Dados Azure, consulte o artigo [O que é o Serviço de Migração da Base de Dados Azure?](./dms-overview.md)
- Para obter informações sobre a Base de Dados Azure SQL, consulte o artigo [O que é o serviço de Base de Dados Azure SQL?](../azure-sql/database/sql-database-paas-overview.md)
