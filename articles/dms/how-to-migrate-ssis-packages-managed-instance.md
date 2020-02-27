---
title: Migrar pacotes SSIS para instância gerida pela SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar pacotes e projetos de integração de servidores SQL (SSIS) para uma base de dados Azure SQL gerida através do Serviço de Migração de Bases de Dados Azure ou do Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a0669724888f02672d18ef9e8f725eef1c744f90
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650969"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Pacotes de Serviços de Integração de Servidores SQL migrados para uma base de dados Azure SQL gerida
Se utilizar os Serviços de Integração de Servidores SQL (SSIS) e pretender migrar os seus projetos/pacotes SSIS da fonte SSISDB hospedada pelo SQL Server para o destino SSISDB hospedado por uma instância gerida pela Azure SQL Database, pode utilizar o Serviço de Migração azure Database.

Se a versão do SSIS que utiliza for mais cedo do que em 2012 ou utilizar tipos de lojas de pacotes não-SSISDB, antes de migrar os seus projetos/pacotes SSIS, precisa convertê-los utilizando o Assistente de Conversão de Projetos de Integração, que também pode ser lançado a partir de SSMS. Para mais informações, consulte o artigo Conversão de projetos para o modelo de [implantação do projeto.](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)

> [!NOTE]
> O Azure Database Migration Service (DMS) não suporta atualmente a Base de Dados Azure SQL como destino de migração alvo. Para redistribuir projetos/pacotes SSIS para a Base de Dados SQL Azure, consulte o artigo Redeployie os pacotes de Serviços de Integração de [Servidores SQL para a Base de Dados Azure SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avaliar os projetos/pacotes SSIS de origem.
> * Migrar projetos/pacotes SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, precisa de:

* Para criar uma rede virtual do Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-a-site aos seus servidores de origem no local, utilizando [expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações, consulte o artigo [Topoologias de rede para base de dados Azure SQL geridas por migrações de instâncias utilizando o Serviço]( https://aka.ms/dmsnetworkformi)de Migração de Bases de Dados Azure . Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação](https://docs.microsoft.com/azure/virtual-network/)da Rede Virtual , e especialmente os artigos quickstart com detalhes passo a passo.
* Para garantir que as regras do Grupo de Segurança da rede virtual não bloqueiam as seguintes portas de comunicação de entrada para o Serviço de Migração de Bases de Dados Azure: 443, 53, 9354, 445, 12000. Para mais detalhes sobre a filtragem de tráfego da rede virtual NSG, consulte o artigo Filtrar o tráfego da [rede com grupos](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)de segurança da rede .
* Para configurar o [seu Windows Firewall para acesso](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)ao motor de base de dados de origem .
* Para abrir o seu Windows Firewall para permitir que o Serviço de Migração de Bases de Dados Azure aceda à fonte Do Servidor SQL, que por padrão é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração, bem como ficheiros através da porta SMB 445.
* Uma Base de Dados Azure SQL conseguiu acolher o SSISDB. Se precisar de criar um, siga os detalhes no artigo Crie uma instância gerida por base de [dados Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para garantir que os logins utilizados para ligar a fonte SQL Server e a instância gerida pelo alvo são membros da função do servidor de sysadmin.
* Para verificar se o SSIS está aprovisionado na Azure Data Factory (ADF) contendo o Tempo de Execução de Integração Azure-SSIS (IR) com o destino SSISDB hospedado por uma instância gerida pela Azure SQL Database (conforme descrito no artigo [Create the Azure-SSIS integration runtime in Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

## <a name="assess-source-ssis-projectspackages"></a>Avaliar os projetos/pacotes ssis de origem

Embora a avaliação da fonte SSISDB ainda não esteja integrada no Assistant de Migração de Bases de Dados (DMA), os seus projetos/pacotes SSIS serão avaliados/validados à medida que forem redistribuídos para o destino SSISDB hospedado numa instância gerida pela Base de Dados Azure SQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores**de Recursos .

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

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso à fonte SQL Server e ao target Azure SQL Database gerido.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](https://aka.ms/DMSVnet).

    Para mais detalhes, consulte o artigo [Topoologias de rede para migrações de instâncias geridas azure SQL DB utilizando o Serviço](https://aka.ms/dmsnetworkformi)de Migração de Bases de Dados Azure .

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

4. No ecrã do **novo projeto de migração,** especifique um nome para o projeto, na caixa de texto **tipo servidor Fonte,** selecione **SQL Server**, na caixa de texto do tipo servidor **Target,** selecione **Azure SQL Database Managed Instance**, e depois para Escolher tipo de **atividade,** selecione migração de **pacotes SSIS**.

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

1. No ecrã de detalhes do **alvo da Migração,** especifique os detalhes de ligação para o alvo.

     ![Detalhes do alvo](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="review-the-migration-summary"></a>Rever o resumo da migração

1. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

2. Para **a(s) opção de sobreescrita do projeto SSIS e**do ambiente, especifique se deve ou não substituir ou ignorar os projetos e ambientes ssis existentes.

    ![Resumo do projeto de migração](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Reveja e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação de migração no Guia de [Migração](https://datamigration.microsoft.com/)da Microsoft Database .
