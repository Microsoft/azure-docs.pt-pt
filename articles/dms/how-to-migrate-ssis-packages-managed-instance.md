---
title: Migrar pacotes do SQL Server Integration Services para uma instância gerida da base de dados do Azure SQL | Documentos da Microsoft
description: Saiba como migrar pacotes do SQL Server Integration Services para uma instância gerida da base de dados do Azure SQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083187"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrar pacotes do SQL Server Integration Services para uma instância gerida da base de dados do Azure SQL
Se utilizar o SQL Server Integration Services (SSIS) e pretender migrar dos projetos/pacotes do SSIS da origem de SSISDB alojado pelo SQL Server para o SSISDB alojado por uma instância gerida da base de dados do Azure SQL de destino, pode utilizar o Azure Database Migration Service.

Se a versão do SSIS que utiliza é anterior ao 2012 ou utilizar tipos de armazenamento do pacote não SSISDB, antes de migrar o SSIS projetos/pacotes, é preciso convertê-los com o Integration Services Project Conversion Wizard, que também pode ser inicializado a partir do SSMS. Para obter mais informações, consulte o artigo [conversão de projetos para o modelo de implementação do projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) atualmente não suporta a base de dados do Azure SQL como um destino de migração de destino. Para Reimplementar projetos/pacotes do SSIS para a base de dados do Azure SQL, consulte o artigo [pacotes de voltar a implementar o SQL Server Integration Services para a base de dados do Azure SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avalie a origem projetos/pacotes do SSIS.
> * Migre projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estes passos, terá de:

* Para criar uma rede Virtual do Azure (VNet) para o serviço de migração de base de dados do Azure com o modelo de implementação Azure Resource Manager, que garante uma conectividade site a site aos seus servidores de origem no local, utilizando um [ExpressRoute ](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações, consulte o artigo [topologias de rede para a base de dados do Azure SQL gerida migrações de instância com o Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Para obter mais informações sobre como criar uma VNet, veja a [documentação das redes virtuais](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.
* Para garantir que as regras do grupo de segurança de rede de VNet não bloqueiam as seguintes portas de comunicação de entrada para o Azure Database Migration Service: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego do Azure VNet NSG, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Para configurar seus [Firewall do Windows para acesso ao motor de base de dados de origem](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Para abrir a Firewall do Windows para permitir que o serviço de migração de base de dados do Azure aceder à origem de SQL Server, que, por predefinição, é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração, bem como ficheiros através da porta SMB 445.
* Uma base de dados do SQL do Azure gerido instância para alojar o SSISDB. Se precisar de criar um, siga os detalhes no artigo [criar um Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para garantir que os inícios de sessão utilizados para ligar a origem de instância gerida do SQL Server e o destino são membros da função de servidor sysadmin.
* Para verificar se o SSIS é aprovisionado no Azure Data Factory (ADF) que contém o Azure-SSIS Integration Runtime (IR) com o SSISDB alojado por uma base de dados do SQL do Azure de destino de instância gerida (conforme descrito no artigo [criar o Azure-SSIS runtime de integração no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar a origem projetos/pacotes do SSIS

Embora a avaliação da origem SSISDB ainda não estiver integrada para a base de dados do Assistente de migração (DMA), dos projetos/pacotes do SSIS serão avaliados/validadas como eles são implantados novamente para o destino que SSISDB hospedado numa instância gerida da base de dados do Azure SQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do serviço de migração de base de dados do Azure e, em seguida, selecione **fornecedores de recursos**.

    ![Mostrar fornecedores de recursos](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal do Azure, selecione + **Criar um recurso**, procure o **Azure Database Migration Service** e selecione **Azure Database Migration Service**, na lista pendente.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecionar uma VNet já existente ou criar um.

    A VNet fornece o Azure Database Migration Service com acesso a origem do SQL Server e a instância gerida de base de dados do Azure SQL de destino.

    Para obter mais informações sobre como criar uma VNet no portal do Azure, consulte o artigo [criar uma rede virtual com o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, consulte o artigo [topologias de rede para o Azure SQL DB geridos migrações de instância com o Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. No ecrã **Azure Database Migration Service**, procure o nome da instância que criou e selecione-a.

3. Selecione + **Novo Projeto de Migração**.

4. Na **novo projeto de migração** ecrã, especifique um nome para o projeto, no **tipo de servidor de origem** caixa de texto, selecione **do SQL Server**, no **servidor de destino tipo** caixa de texto, selecione **instância gerida da base de dados SQL do Azure**e, em seguida, para **Escolher tipo de atividade**, selecione **demigraçãodepacotesdoSSIS**.

   ![Criar o projeto do DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selecione **Create** (Criar) para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações SSL encriptadas que utilizem um certificado autoassinado não proporcionam segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar em SSL com certificados autoassinados num ambiente de produção ou em servidores que estejam ligados à Internet.

   ![Detalhes da origem](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. Sobre o **detalhes do destino de migração** ecrã, especifique os detalhes de ligação para o destino.

     ![Detalhes do destino](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="review-the-migration-summary"></a>Rever o resumo da migração

1. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

2. Para o **projetos do SSIS e ambientes de substituir opção**, especifique se pretende substituir ou ignorar os projetos do SSIS e ambientes existentes.

    ![Resumo do projeto de migração](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Reveja e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

## <a name="next-steps"></a>Passos Seguintes

* Reveja as orientações de migração no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).
