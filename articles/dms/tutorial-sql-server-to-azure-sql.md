---
title: 'Tutorial: Utilizar o Azure Database Migration Service para migrar o SQL Server para a Base de Dados SQL do Azure offline | Microsoft Docs'
description: Saiba como migrar do SQL Server no local para a Base de Dados SQL do Azure offline com o Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 783785a73b3a6a99126b07f3759be540fe3098d8
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959244"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Tutorial: Migrar o SQL Server para a Base de Dados SQL do Azure offline com o DMS
Pode utilizar o Azure Database Migration Service para migrar as bases de dados de uma instância do SQL Server no local para a [Base de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/). Neste tutorial, vai migrar a base de dados **Adventureworks2012** restaurada para uma instância do SQL Server 2016 (ou posterior) no local para a Base de Dados SQL do Azure com o Azure Database Migration Service.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilizar o Assistente de Migração de Dados para avaliar a sua base de dados no local.
> * Utilizar o Assistente de Migração de Dados para migrar o esquema de exemplo.
> * Criar uma instância do Azure Database Migration Service.
> * Utilizar o Azure Database Migration Service para criar um projeto de migração.
> * Executar a migração.
> * Monitorizar a migração.
> * Transferir um relatório da migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo descreve uma migração offline do SQL Server para a Base de Dados SQL do Azure. Para uma migração online, veja [Migrar o SQL Server para a Base de Dados SQL do Azure online com o DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

- Transferir e instalar o [SQL Server 2016 ou posterior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
- Ativar o protocolo TCP/IP, que está desativado por predefinição durante a instalação do SQL Server Express, através das instruções no artigo [Enable or Disable a Server Network Protocol](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) (Ativar ou desativar um Protocolo de Rede de Servidor).
- Criar uma instância da Base de Dados SQL do Azure; para tal, siga o detalhe descrito no artigo [Criar uma base de Dados SQL do Azure no portal do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Transferir e instalar o [Assistente de Migração de Dados](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
- Utilizar o modelo de implementação Azure Resource Manager para criar uma VNET para o Azure Database Migration Service, que proporciona conectividade site a site aos seus servidores de origens no local mediante a utilização do [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou de [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Confirmar que as regras de Grupos de Segurança de Rede da Rede Virtual do Azure (VNET) não bloqueia as portas de comunicação 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego dos NSGs das VNETs do Azure, veja o artigo [Filter network traffic with network security groups](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Filtrar tráfego de rede com grupos de segurança de rede).
- Configurar a sua [Firewall do Windows para acesso ao motor de bases de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Abrir a firewall do Windows para permitir ao Azure Database Migration Service aceder ao SQL Server de origem, que, por predefinição, é a porta TCP 1433.
- Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
- Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração.
- Criar uma [regra de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) ao nível do servidor para o servidor da Base de Dados SQL do Azure para permitir que o Azure Database Migration Service aceda às bases de dados de destino. Fornecer o intervalo de sub-redes da VNET utilizada no Azure Database Migration Service.
- Confirmar que as credenciais utilizadas para ligar à instância de origem do SQL Server têm permissões [CONTROLAR SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Confirmar que as credenciais utilizadas para ligar à instância de destino da Base de Dados SQL do Azure têm permissões CONTROLAR BASE DE DADOS nas bases de dados SQL do Azure de destino.

## <a name="assess-your-on-premises-database"></a>Avaliar a base de dados no local
Antes de poder migrar dados de uma instância do SQL Server no local para a Base de Dados SQL do Azure, tem de avaliar a base de Dados SQL do Azure relativamente a problemas que possam impedir a migração. Ao utilizar o Assistente de Migração de Dados v3.3 ou posterior, siga os passos descritos no artigo [Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) (Realizar uma avaliação da migração do SQL Server para concluir a avaliação da base de dados no local. Segue-se um resumo dos passos necessários:
1.  No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e selecione o tipo de projeto **Assessment** (Avaliação).
2.  Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server**, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure) e, em seguida, selecione **Create** (Criar) para criar o projeto.

    Quando avaliar a base de Dados do SQL Server de origem que está a ser migrada para a Base de Dados SQL do Azure, pode escolher um ou ambos os tipos de relatórios de avaliação abaixo:
    - Verificar a compatibilidade da base de dados
    - Verificar a paridade de funcionalidades

    Ambos os tipos de relatórios estão selecionados por predefinição.

3.  No Assistente de Migração de Dados, no ecrã **Options** (Opções), selecione **Next** (Seguinte).
4.  No ecrã **Select sources** (Selecionar origens), na caixa de diálogo **Connect to a server** (Ligar a um servidor), indique os detalhes da ligação ao SQL Server e selecione **Connect** (Ligar).
5.  Na caixa de diálogo **Add sources** (Adicionar origens), selecione **AdventureWorks2012**, selecione **Add** (Adicionar) e, em seguida, selecione **Start Assessment** (Iniciar Avaliação).

    Quando a avaliação estiver concluída, os resultados são apresentados tal como no gráfico abaixo:

    ![Avaliar migração de dados](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    Na Base de Dados SQL do Azure, as avaliações identificam problemas de paridade de funcionalidades e problemas que impedem a migração.

    - A categoria **paridade de funcionalidades do SQL Server** oferece um conjunto abrangente de recomendações, abordagens alternativas disponíveis no Azure e passos de migração para o ajudar a planear o esforço necessário para os seus projetos de migração.
    - A categoria **Problemas de compatibilidade** identifica funcionalidades parcialmente suportadas ou não suportadas que refletem problemas de compatibilidade que poderão impedir a migração de bases de dados do SQL Server no local para a Base de Dados SQL do Azure. As recomendações também são disponibilizadas para o ajudar a resolver esses problemas.

6.  Selecione as opções relevantes para rever os resultados da avaliação relativamente a problemas de bloqueio que impedem a migração e problemas de paridade de funcionalidades.

## <a name="migrate-the-sample-schema"></a>Migrar o esquema de exemplo
Quando se sentir confortável com a avaliação e satisfeito com o facto de a base de dados selecionada for uma candidata viável para migração para a Base de Dados SQL do Azure, utilize o Assistente de Migração de Dados para migrar o esquema para a Base de Dados SQL do Azure.

> [!NOTE]
> Antes de poder criar um projeto de migração no Assistente de Migração de Dados, confirme que já aprovisionou uma base de dados SQL do Azure, conforme mencionado nos pré-requisitos. Para efeitos deste tutorial, pressupõe-se que o nome da Base de Dados SQL do Azure é **AdventureWorksAzure**, mas pode indicar um nome à sua escolha.

Para migrar o esquema **AdventureWorks2012** para a Base de Dados SQL do Azure, realize os passos seguintes:

1.  No Assistente de Migração de Dados, selecione o ícone New (Novo) (+) e, em **Project type** (Tipo de projeto), selecione **Migration** (Migração).
2.  Indique um nome para o projeto, na caixa de texto **Source server type** (Tipo de servidor de origem), selecione **SQL Server** e, na caixa de texto **Target server type** (Tipo de servidor de destino), selecione **Azure SQL Database** (Base de Dados SQL do Azure).
3.  Em **Migration Scope** (Âmbito da migração), selecione **Schema only** (Apenas esquema).

    Depois de realizar os passos anteriores, a interface do Assistente de Migração de Dados deverá ser apresentada, conforme mostrado na imagem abaixo:
    
    ![Criar Projeto do Assistente de Migração de Dados](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

4.  Selecione **Create** (Criar) para criar o projeto.
5.  No Assistente de Migração de Dados, indique os detalhes da ligação de origem do SQL Server, selecione **Connect** (Ligar) e selecione a base de dados **AdventureWorks2012**.

    ![Detalhes da Ligação de Origem do Assistente de Migração de Dados](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)

6.  Selecione **Next** (Seguinte), em **Connect to target server** (Ligar a servidor de destino), especifique os detalhes da ligação de destino da base de dados SQL do Azure, selecione **Connect** (Ligar) e, em seguida, selecione a base de dados **AdventureWorksAzure** que pré-aprovisionou na base de dados SQL do Azure.

    ![Detalhes da Ligação de Destino do Assistente de Migração de Dados](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)

7.  Selecione **Next** (Seguinte) para avançar para o ecrã **Select objects** (Selecionar objetos), no qual pode especificar os objetos do esquema na base de dados **AdventureWorks2012** que têm de ser implementados na Base de Dados SQL do Azure.

    Por predefinição, estão selecionados todos os objetos.

    ![Gerar scripts do SQL](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)

8.  Selecione **Generate SQL script** (Gerar script do SQL) para criar os scripts do SQL e reveja-os para ver se têm erros.

    ![Script do esquema](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)

9.  Selecione **Deploy schema** (Implementar esquema) para implementar o esquema na Base de Dados SQL do Azure e, após a implementação, verifique o servidor de destino quanto a anomalias.

    ![Implementar o esquema](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration
1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.
 
   ![Mostrar subscrições no portal](media\tutorial-sql-server-to-azure-sql\portal-select-subscription1.png)
       
2. Selecione a subscrição na qual pretende criar a instância do Database Migration Service do Azure e, em seguida, selecione **Fornecedores de recursos**.
 
    ![Mostrar fornecedores de recursos](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)
    
3.  Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.
 
    ![Registar o fornecedor de recursos](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Criar uma instância
1.  No portal do Azure, selecione + **Criar um recurso**, procure o Azure Database Migration Service e selecione **Azure Database Migration Service** na lista pendente.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)

2.  No ecrã **Azure Database Migration Service**, selecione **Criar**.
 
    ![Criar instância do Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create1.png)
  
3.  No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização na qual pretende criar a instância do Azure Database Migration Service. 

5. Selecione uma rede virtual (VNET) já existente ou crie uma nova.

    A VNET fornece ao Azure Database Migration Service acesso à instância do SQL Server de origem e à instância da Base de Dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma VNET no portal do Azure, veja o artigo [Criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    Se precisar de ajuda para escolher o escalão certo do Azure Database Migration Service, veja as recomendações [nesta](https://go.microsoft.com/fwlink/?linkid=861067) mensagem.  

     ![Configurar as definições da instância do Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-settings2.png)

7.  Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração
Após a criação do serviço, localize-o no portal do Azure, abra-o e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.
 
      ![Localizar todas as instâncias do Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. No ecrã **Azure Database Migration Services**, procure o nome da instância do Azure Database Migration Service que criou e selecione-a.
 
     ![Localizar a instância do Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Selecione + **Novo Projeto de Migração**.
4. No ecrã **Novo projeto de migração**, indique um nome para o projeto, na caixa de texto **Tipo de servidor de origem**, selecione **SQL Server**, na caixa de texto **Tipo de servidor de destino**, selecione **Base de Dados SQL do Azure** e, em **Escolher tipo de atividade**, selecione **Migração de dados offline**. 

    ![Criar Projeto do Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql\dms-create-project2.png)

5.  Selecione **Criar e executar atividade** para criar o projeto e executar a atividade de migração.

## <a name="specify-source-details"></a>Especificar os detalhes da origem
1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação da instância do SQL Server de origem.
 
    Confirme que utiliza um Nome de Domínio Completamente Qualificado (FQDN) no nome da instância do SQL Server de origem. Também pode utilizar o endereço IP em situações nas quais a resolução de nomes DNS não é possível.

2. Se não tiver um certificado fidedigno instalado no servidor de origem, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media\tutorial-sql-server-to-azure-sql\dms-source-details2.png)

## <a name="specify-target-details"></a>Especificar os detalhes do destino
1. Selecione **Guardar** e, no ecrã, **Detalhes do destino da migração**, especifique os detalhes da ligação da Base de Dados SQL do Azure de destino, que é a Base de Dados SQL do Azure pré-aprovisionada na qual o esquema de **AdventureWorks2012** foi implementada com o Assistente de Migração de Dados.

    ![Selecionar o Destino](media\tutorial-sql-server-to-azure-sql\dms-select-target2.png)

2. Selecione **Guardar** e, no ecrã **Mapear para bases de dados de destino**, mapeie as bases de dados de origem e destino para migração.

    Se o nome da base de dados de destino for o mesmo da de origem, o Azure Database Migration Service seleciona a base de dados de destino por predefinição.

    ![Mapear para as bases de dados de destino](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity2.png)

3. Selecione **Guardar**, no ecrã **Selecionar tabelas**, expanda a lista de tabelas e reveja a lista dos campos afetados.

    Repare que o Azure Database Migration Service seleciona automaticamente todas as tabelas de origem vazias que existem na instância da Base de Dados SQL do Azure de destino. Se quiser voltar a emigrar tabelas que já incluem dados, tem de selecionar explicitamente as tabelas neste painel.

    ![Selecionar tabelas](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity2.png)

4.  Selecione **Guardar**, no ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

5. Expanda a secção **Opção de validação** para ver o ecrã **Escolher opção de validação** e especifique se as bases de dados migradas vão ser validadas relativamente a **Comparação de esquema**, **Consistência de dados** e **Exatidão de consultas**.
    
    ![Escolher a opção de validação](media\tutorial-sql-server-to-azure-sql\dms-configuration2.png)

6.  Selecione **Guardar** e reveja o resumo para confirmar que os detalhes de origem e destino correspondem ao que especificou anteriormente.

    ![Resumo da migração](media\tutorial-sql-server-to-azure-sql\dms-run-migration2.png)

## <a name="run-the-migration"></a>Executar a migração
- Selecione **Executar a migração**.

    É apresentada a janela de atividade da migração e o **Estado** da atividade é **Pendente**.

    ![Estado da Atividade](media\tutorial-sql-server-to-azure-sql\dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorizar a migração
1. No ecrã de atividade da migração, selecione **Atualizar** para atualizar a vista até que o **Estado** da migração apareça como **Concluída**.

    ![Estado da Atividade Concluída](media\tutorial-sql-server-to-azure-sql\dms-completed-activity1.png)

2. Após a conclusão da migração, selecione **Transferir relatório** para obter um relatório que liste os detalhes associados ao processo de migração.

3. Verifique a base ou bases de dados de destino no servidor da Base de Dados SQL do Azure de destino.

### <a name="additional-resources"></a>Recursos adicionais

 * Laboratório prático [SQL migration using Azure Data Migration Service (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) hands-on lab (Utilizar o Azure Database Migration Service (DMS) para a migração de SQL).