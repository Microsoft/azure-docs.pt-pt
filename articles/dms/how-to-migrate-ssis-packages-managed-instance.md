---
title: Migrar pacotes SSIS para sql caso gerido
titleSuffix: Azure Database Migration Service
description: Saiba como migrar pacotes e projetos de integração de servidores SQL (SSIS) para uma Instância Gerida Azure SQL utilizando o Serviço de Migração da Base de Dados Azure ou o Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e3e2aa055baf3dfb4bee0629040fc7c140844637
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094018"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>Migrar pacotes de Serviços de Integração de ServidorES SQL para uma instância gerida Azure SQL
Se utilizar os Serviços de Integração de Servidores SQL (SSIS) e quiser migrar os seus projetos/pacotes SSIS da fonte SSISDB hospedado pelo SQL Server para o destino SSISDB hospedado por um Azure SQL Managed Instance, pode utilizar o Azure Database Migration Service.

Se a versão do SSIS que utiliza for anterior a 2012 ou utilizar tipos de lojas de pacotes não SSISDB, antes de migrar os seus projetos/pacotes SSIS, tem de os converter utilizando o Assistente de Conversão de Projetos de Integração, que também pode ser lançado a partir de SSMS. Para mais informações, consulte o artigo [Converter projetos para o modelo de implementação do projeto.](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)

> [!NOTE]
> Atualmente, o Azure Database Migration Service (DMS) não suporta a Base de Dados Azure SQL como destino de migração alvo. Para recolocar projetos/pacotes SSIS na Base de Dados Azure SQL, consulte o artigo [Reploy SQL Server Integration Services packages to Azure SQL Database](./how-to-migrate-ssis-packages.md).

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avaliar os projetos/pacotes SSIS de origem.
> * Migrar projetos/pacotes SSIS para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, você precisa:

* Para criar uma Rede Virtual Microsoft Azure para o Serviço de Migração de Bases de Dados Azure utilizando o modelo de implementação do Gestor de Recursos Azure, que fornece conectividade site-to-site aos seus servidores de origem no local, utilizando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [o VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para obter mais informações, consulte as [topologias da rede para migrações de casos geridos sql utilizando o Serviço de Migração da Base de Dados Azure]( https://aka.ms/dmsnetworkformi). Para obter mais informações sobre a criação de uma rede virtual, consulte a [Documentação da Rede Virtual,](../virtual-network/index.yml)e especialmente os artigos quickstart com detalhes passo a passo.
* Para garantir que as regras do grupo de segurança da rede virtual não bloqueiem a porta de saída 443 do ServiceTag para ServiceBus, Storage e AzureMonitor. Para obter mais detalhes sobre a filtragem de tráfego NSG da rede virtual, consulte o artigo Filtrar o [tráfego da rede com grupos de segurança de rede](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Para configurar o seu [Windows Firewall para acesso ao motor de base de dados de origem](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Para abrir o seu Windows Firewall para permitir que o Serviço de Migração da Base de Dados Azure aceda ao servidor SQL de origem, que por padrão é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração, bem como ficheiros através da porta SMB 445.
* Um SQL Managed Instance para hospedar O SSISDB. Se precisar de criar um, siga os detalhes do artigo [Crie um Exemplo Gerido Azure SQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Para garantir que os logins utilizados para ligar o sql server de origem e a instância gerida pelo alvo são membros da função do servidor sysadmin.
* Para verificar se o SSIS é a provisionado na Azure Data Factory (ADF) que contém o tempo de execução da integração Azure-SSIS (IR) com o destino SSISDB hospedado por uma SQL Managed Instance (conforme descrito no artigo [Criar o tempo de integração Azure-SSIS na Fábrica de Dados Azure).](../data-factory/create-azure-ssis-integration-runtime.md)

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes SSIS de origem

Embora a avaliação da fonte SSISDB ainda não esteja integrada no Assistente de Migração da Base de Dados (DMA), os seus projetos/pacotes SSIS serão avaliados/validados à medida que forem redistribuídos para o destino SSISDB hospedado numa Instância Gerida Azure SQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Criar uma instância do Azure Database Migration Service

1. No portal Azure, selecione + **Crie um recurso,** procure o **Serviço de Migração da Base de Dados Azure** e, em seguida, selecione O Serviço de **Migração da Base de Dados Azure** a partir da lista de espera.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. No ecrã **Criar Serviço de Migração**, especifique um nome para o serviço, a subscrição e um grupo de recursos novo ou já existente.

4. Selecione a localização em que pretende criar a instância do DMS.

5. Selecione uma rede virtual existente ou crie uma.

    A rede virtual fornece ao Azure Database Migration Service acesso ao servidor SQL de origem e direciona a Azure SQL Managed Instance.

    Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

    Para mais detalhes, consulte as topologias da rede para as migrações de [instância gerida do Azure SQL utilizando o Serviço de Migração da Base de Dados Azure](./resource-network-topologies.md).

6. Selecione um escalão de preço.

    Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).

    ![Criar o serviço DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Selecione **Criar** para criar o serviço.

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Após a criação de uma instância do serviço, localize-a no portal do Azure, abra-a e crie um projeto de migração novo.

1. No portal do Azure, selecione **Todos os serviços**, procure Azure Database Migration Service e selecione **Azure Database Migration Services**.

    ![Localizar todas as instâncias do Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. No ecrã do **Serviço de Migração da Base de Dados Azure,** procure o nome do caso que criou e, em seguida, selecione o caso.

3. Selecione + **Novo Projeto de Migração**.

4. No novo ecrã do **projeto de migração,** especifique um nome para o projeto, na caixa de texto **do tipo do servidor Fonte,** selecione **SQL Server**, na caixa de texto do tipo do servidor **Target,** selecione **Azure SQL Managed Instance**, e, em seguida, para Escolher tipo de atividade , selecione migração **de** **pacote SSIS**.

   ![Criar o projeto do DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Selecione **Create** (Criar) para criar o projeto.

## <a name="specify-source-details"></a>Especificar os detalhes da origem

1. No ecrã **Detalhes da origem da migração**, especifique os detalhes da ligação do SQL Server de origem.

2. Se não tiver um certificado fidedigno instalado no seu servidor, selecione a caixa de verificação **Confiar no certificado de servidor**.

    Se não tiver um certificado fidedigno instalado, o SQL Server gera um certificado autoassinado quando a instância é iniciada. Este certificado é utilizado para encriptar as credenciais para as ligações de cliente.

    > [!CAUTION]
    > As ligações TLS que são encriptadas usando um certificado auto-assinado não proporcionam uma segurança forte. São suscetíveis a ataques man-in-the-middle. Não deve confiar no TLS utilizando certificados auto-assinados num ambiente de produção ou em servidores ligados à internet.

   ![Detalhes da origem](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Selecione **Guardar**.

## <a name="specify-target-details"></a>Especificar os detalhes do destino

1. No ecrã de **detalhes do alvo da migração,** especifique os detalhes da ligação para o alvo.

     ![Detalhes do destino](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="review-the-migration-summary"></a>Rever o resumo da migração

1. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

2. Para a **opção de substituição do projeto SSIS e do ambiente,** especifique se deve substituir ou ignorar os projetos e ambientes existentes em SSIS.

    ![Resumo do projeto de migração](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Reveja e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação de migração no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft.