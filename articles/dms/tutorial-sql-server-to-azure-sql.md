---
title: 'Tutorial: migrar SQL Server offline para um banco de dados individual SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar de SQL Server locais para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure offline usando o serviço de migração de banco de dados do Azure.
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
ms.openlocfilehash: 75dacf053de595ae25f3b58ef33577e2723319f0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750752"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Tutorial: migrar SQL Server para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure offline usando DMS

Você pode usar o serviço de migração de banco de dados do Azure para migrar os bancos de dados de uma instância de SQL Server local para o [banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/). Neste tutorial, você migra o banco de dados **Adventureworks2012** restaurado para uma instância local do SQL Server 2016 (ou posterior) para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
>
> - Utilizar o Assistente de Migração de Dados para avaliar a sua base de dados no local.
> - Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> - Crie uma instância do Azure Database Migration Service.
> - Crie um projeto de migração usando o serviço de migração de banco de dados do Azure.
> - Executar a migração.
> - Monitorizar a migração.
> - Transferir um relatório da migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline de SQL Server para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure. Para uma migração online, veja [Migrar o SQL Server para a Base de Dados SQL do Azure online com o DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

- Baixe e instale o [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads).
- Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).
- Crie um banco de dados único (ou em pool) no banco de dados SQL do Azure, que você faz seguindo os detalhes no artigo [criar um banco de dados individual no banco de dados SQL do Azure usando o portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se você usar SQL Server Integration Services (SSIS) e desejar migrar o banco de dados de catálogo para seus projetos/pacotes SSIS (SSISDB) do SQL Server para o banco de dados SQL do Azure, o destino SSISDB será criado e gerenciado automaticamente em seu nome quando você provisionar o SSIS no Azure Data Factory (ADF). Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Crie um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações sobre como criar uma rede virtual, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.

    > [!NOTE]
    > Durante a configuração de rede virtual, se você usar o ExpressRoute com emparelhamento de rede para a Microsoft, adicione os seguintes [pontos de extremidade](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) de serviço à sub-rede na qual o serviço será provisionado:
    >
    > - Ponto de extremidade do banco de dados de destino (por exemplo, ponto de extremidade SQL, ponto de extremidade Cosmos DB e assim por diante)
    > - Ponto de extremidade de armazenamento
    > - Ponto de extremidade do barramento de serviço
    >
    > Essa configuração é necessária porque o serviço de migração de banco de dados do Azure não tem conectividade com a Internet.
    >
    >Se você não tiver conectividade site a site entre a rede local e o Azure ou se houver uma largura de banda de conectividade site a site limitada, considere usar o serviço de migração de banco de dados do Azure no modo híbrido (versão prévia). O modo híbrido aproveita um trabalho de migração local junto com uma instância do serviço de migração de banco de dados do Azure em execução na nuvem. Para criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido, consulte o artigo [criar uma instância do serviço de migração de banco de dados do Azure no modo híbrido usando o portal do Azure](https://aka.ms/dms-hybrid-create).

- Verifique se as regras do grupo de segurança de rede de rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual do Azure, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abra o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o SQL Server de origem, que por padrão é a porta TCP 1433.
- Se você estiver executando várias instâncias de SQL Server nomeadas usando portas dinâmicas, talvez queira habilitar o serviço de SQL Browser e permitir o acesso à porta UDP 1434 por meio de firewalls para que o serviço de migração de banco de dados do Azure possa se conectar a uma instância nomeada em sua origem servidor.
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o serviço de migração de banco de dados do Azure acesse os bancos de dados de origem para migração.
- Crie uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) de IP no nível de servidor para o servidor do banco de dados SQL do Azure para permitir o acesso ao serviço de migração de banco de dados do Azure aos bancos de dados de destino Forneça o intervalo de sub-rede da rede virtual usada para o serviço de migração de banco de dados do Azure.
- Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.

## <a name="assess-your-on-premises-database"></a>Avaliar a base de dados no local

Antes de poder migrar dados de uma instância de SQL Server local para um banco de dado único ou banco de dados em pool no banco de dados SQL do Azure, você precisa avaliar o banco de dados SQL Server para qualquer problema de bloqueio que possa impedir a migração. Ao utilizar o Assistente de Migração de Dados v3.3 ou posterior, siga os passos descritos no artigo [Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) (Realizar uma avaliação da migração do SQL Server para concluir a avaliação da base de dados no local. Segue-se um resumo dos passos necessários:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e selecione o tipo de projeto **Assessment** (Avaliação).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server**, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure) e, em seguida, selecione **Create** (Criar) para criar o projeto.

    Ao avaliar o banco de dados de SQL Server de origem migrando para um banco de dados individual ou um banco de dados em pool no banco de dados SQL do Azure, você pode escolher um ou ambos os seguintes tipos de relatório de avaliação:

   - Verificar a compatibilidade da base de dados
   - Verificar a paridade de funcionalidades

    Ambos os tipos de relatórios estão selecionados por predefinição.

3. No Assistente de Migração de Dados, no ecrã **Options** (Opções), selecione **Next** (Seguinte).
4. No ecrã **Select sources** (Selecionar origens), na caixa de diálogo **Connect to a server** (Ligar a um servidor), indique os detalhes da ligação ao SQL Server e selecione **Connect** (Ligar).
5. Na caixa de diálogo **Add sources** (Adicionar origens), selecione **AdventureWorks2012**, selecione **Add** (Adicionar) e, em seguida, selecione **Start Assessment** (Iniciar Avaliação).

    > [!NOTE]
    > Se você usar o SSIS, o DMA não oferece atualmente suporte à avaliação da origem SSISDB. No entanto, os projetos/pacotes do SSIS serão avaliados/validados à medida que forem reimplantados no SSISDB de destino hospedado pelo banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Quando a avaliação estiver concluída, os resultados são apresentados tal como no gráfico abaixo:

    ![Avaliar migração de dados](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Para bancos de dados individuais ou bancos de dados em pool no banco de dados SQL do Azure, as avaliações identificam problemas de paridade de recursos e problemas de bloqueio de migração para a implantação em um banco de dados individual ou em um banco de dados em pool.

    - A categoria **paridade de funcionalidades do SQL Server** oferece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e passos de migração para o ajudar a planear o esforço necessário para os seus projetos de migração.
    - A categoria **Problemas de compatibilidade** identifica funcionalidades parcialmente suportadas ou não suportadas que refletem problemas de compatibilidade que poderão impedir a migração de bases de dados do SQL Server no local para a Base de Dados SQL do Azure. As recomendações também são disponibilizadas para o ajudar a resolver esses problemas.

6. Selecione as opções relevantes para rever os resultados da avaliação relativamente a problemas de bloqueio que impedem a migração e problemas de paridade de funcionalidades.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo

Depois que você estiver familiarizado com a avaliação e estiver convencido de que o banco de dados selecionado é um candidato viável para migração para um banco de dados individual ou banco de dados em pool no banco de dados SQL do Azure, use DMA para migrar o esquema para o banco de dados SQL do Azure.

> [!NOTE]
> Antes de poder criar um projeto de migração no Assistente de Migração de Dados, confirme que já aprovisionou uma base de dados SQL do Azure, conforme mencionado nos pré-requisitos. Para efeitos deste tutorial, pressupõe-se que o nome da Base de Dados SQL do Azure é **AdventureWorksAzure**, mas pode indicar um nome à sua escolha.

> [!IMPORTANT]
> Se você usar o SSIS, o DMA não oferece atualmente suporte à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes do SSIS no SSISDB de destino armazenado pelo banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Para migrar o esquema **AdventureWorks2012** para um banco de dados individual ou banco de dados SQL do Azure, execute as seguintes etapas:

1. No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2. Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3. Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do Assistente de Migração de Dados deverá ser apresentada, conforme mostrado na imagem abaixo:

    ![Criar Projeto do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selecione **Create** (Criar) para criar o projeto.
5. No Assistente de Migração de Dados, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selecione **Avançar**, em **conectar ao servidor de destino**, especifique os detalhes da conexão de destino para o banco de dados SQL do Azure, selecione **conectar**e, em seguida, selecione o banco de dados **AdventureWorksAzure** que você tinha previamente provisionado no banco de dados SQL do Azure.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selecione **Next** (Seguinte) para avançar para o ecrã **Select objects** (Selecionar objetos), no qual pode especificar os objetos do esquema na base de dados **AdventureWorks2012** que têm de ser implementados na Base de Dados SQL do Azure.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

    ![Implementar o esquema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no Portal do Azure. Procure e selecione **subscrições**.

   ![Mostrar subscrições no portal](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

    ![Mostrar fornecedores de recursos](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Procure migração e, em seguida, selecione **registrar** para **Microsoft. datamigration**.

    ![Registar o fornecedor de recursos](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância

1. No menu portal do Azure ou na **Home** Page do, selecione **criar um recurso**. Pesquise e selecione **serviço de migração de banco de dados do Azure**.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione o local no qual você deseja criar a instância do serviço de migração de banco de dados do Azure.

5. Selecione uma rede virtual existente ou crie uma nova.

    A rede virtual fornece ao serviço de migração de banco de dados do Azure acesso ao SQL Server de origem e à instância de destino do banco de dados SQL do Azure.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Configurar as definições da instância do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No menu portal do Azure, selecione **todos os serviços**. Pesquise e selecione **serviços de migração de banco de dados do Azure**.

     ![Localizar todas as instâncias do serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Na tela **serviços de migração de banco de dados do Azure** , selecione a instância do serviço de migração de banco de dados do Azure que você criou.

3. Selecione **novo projeto de migração**.

     ![Localize sua instância do serviço de migração de banco de dados do Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Base de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**.

    ![Criar Projeto do Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação da instância do SQL Server de origem.

    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem. Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Se você usar o SSIS, o DMS atualmente não oferece suporte à migração do SSISDB de origem, mas você pode reimplantar seus projetos/pacotes do SSIS no SSISDB de destino armazenado pelo banco de dados SQL do Azure. Para obter mais informações sobre como migrar pacotes SSIS, consulte o artigo [migrar pacotes de SQL Server Integration Services para o Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Selecione **Guardar** e, no ecrã, **Detalhes do destino da migração**, especifique os detalhes da ligação do servidor da Base de Dados SQL do Azure de destino, que é a Base de Dados SQL do Azure pré-aprovisionada na qual o esquema de **AdventureWorks2012** foi implementada com o Assistente de Migração de Dados.

    ![Selecionar o Destino](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o banco de dados de destino contém o mesmo nome do banco de dados de origem, o serviço de migração de banco de dados do Azure seleciona o banco de dados de destino por padrão

    ![Mapear para as bases de dados de destino](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selecione **Guardar**, no ecrã **Selecionar tabelas**, expanda a lista de tabelas e reveja a lista dos campos afetados.

    O serviço de migração de banco de dados do Azure seleciona automaticamente todas as tabelas de origem vazias existentes na instância de banco de dados SQL do Azure de destino. Se quiser voltar a emigrar tabelas que já incluem dados, tem de selecionar explicitamente as tabelas neste painel.

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

3. Verifique a base ou bases de dados de destino no servidor da Base de Dados SQL do Azure de destino.

### <a name="additional-resources"></a>Recursos adicionais

- [Migração de SQL usando o laboratório prático do serviço de migração de dados do Azure](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) .
- Para obter informações sobre problemas conhecidos e limitações ao executar migrações online para o banco de dados SQL do Azure, consulte o artigo [problemas conhecidos e soluções alternativas com migrações online do banco de dados SQL do Azure](known-issues-azure-sql-online.md).
- Para obter informações sobre o serviço de migração de banco de dados do Azure, consulte o artigo [o que é o Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
- Para obter informações sobre o banco de dados SQL do Azure, consulte o artigo o [que é o serviço de banco de dados SQL do Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
