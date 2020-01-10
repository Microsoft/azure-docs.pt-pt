---
title: Migrar pacotes do SSIS para a instância gerenciada do SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar pacotes do SQL Server Integration Services (SSIS) e projetos para uma instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure ou o Assistente de Migração de Dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 22f3e6a0e4c041024e826a7ed724d788ce77da62
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751213"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>Migrar pacotes SQL Server Integration Services para uma instância gerenciada do banco de dados SQL do Azure
Se você usar SQL Server Integration Services (SSIS) e quiser migrar seus projetos/pacotes do SSIS do SSISDB de origem hospedado por SQL Server para o SSISDB de destino, hospedado por uma instância gerenciada do banco de dados SQL do Azure, você poderá usar o serviço de migração de banco de dados do Azure.

Se a versão do SSIS usada for anterior à 2012 ou você usar tipos de armazenamento de pacotes não SSISDB, antes de migrar seus projetos/pacotes do SSIS, você precisará convertê-los usando o assistente de conversão de projeto Integration Services, que também pode ser iniciado no SSMS. Para obter mais informações, consulte o artigo [convertendo projetos para o modelo de implantação de projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> O serviço de migração de banco de dados do Azure (DMS) atualmente não dá suporte ao banco de dados SQL do Azure como destino de migração de destino. Para reimplantar projetos/pacotes do SSIS no banco de dados SQL do Azure, consulte o artigo [reimplantar SQL Server Integration Services pacotes no banco de dados SQL do Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisará de:

* Para criar um Rede Virtual do Microsoft Azure para o serviço de migração de banco de dados do Azure usando o modelo de implantação Azure Resource Manager, que fornece conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obter mais informações, consulte o artigo [topologias de rede para migrações de instância gerenciada do banco de dados SQL do Azure usando o serviço de migração de banco de]( https://aka.ms/dmsnetworkformi) Para obter mais informações sobre como criar uma rede virtual, consulte a [documentação da rede virtual](https://docs.microsoft.com/azure/virtual-network/)e especialmente os artigos de início rápido com detalhes passo a passo.
* Para garantir que suas regras de grupo de segurança de rede de rede virtual não bloqueiem as seguintes portas de comunicação de entrada para o serviço de migração de banco de dados do Azure: 443, 53, 9354, 445, 12000. Para obter mais detalhes sobre a filtragem de tráfego NSG de rede virtual, consulte o artigo [filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Para configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados de origem](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Para abrir o Firewall do Windows para permitir que o serviço de migração de banco de dados do Azure acesse o SQL Server de origem, que por padrão é a porta TCP 1433.
* Se estiver a executar várias instâncias nomeadas do SQL Server em portas dinâmicas, poderá ser útil ativar o SQL Browser Service e permitir o acesso à porta UDP 1434 através das suas firewalls, de modo a que o Azure Database Migration Service se possa ligar a uma instância nomeada no servidor de origem.
* Se estiver a utilizar uma aplicação de firewall à frente da base ou bases de dados, poderá ter de adicionar regras de firewall para permitir que o Azure Database Migration Service aceda à base ou bases de dados de origem para migração, bem como ficheiros através da porta SMB 445.
* Uma instância gerenciada do banco de dados SQL do Azure para hospedar o SSISDB. Se você precisar criar um, siga os detalhes no artigo [criar um instância gerenciada do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para garantir que os logons usados para conectar o SQL Server de origem e a instância gerenciada de destino sejam membros da função de servidor sysadmin.
* Para verificar se o SSIS é provisionado no Azure Data Factory (ADF) contendo Azure-SSIS Integration Runtime (IR) com o arquivo de destino SSISDB hospedado por uma instância gerenciada do banco de dados SQL do Azure (conforme descrito no artigo [criar o tempo de execução de integração do Azure-SSIS no Azure data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem

Embora a avaliação da origem SSISDB ainda não esteja integrada ao DMA (Assistente de Migração de banco de dados), seus projetos/pacotes do SSIS serão avaliados/validados conforme são reimplantados no SSISDB de destino armazenado em uma instância gerenciada do banco de dados SQL do Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registar o fornecedor de recursos Microsoft.DataMigration

1. Inicie sessão no portal do Azure, selecione **Todos os serviços** e **Subscrições**.

    ![Mostrar subscrições no portal](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

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

    A rede virtual fornece ao serviço de migração de banco de dados do Azure acesso ao SQL Server de origem e à instância gerenciada do banco de dados SQL do Azure de destino.

    Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/DMSVnet).

    Para obter detalhes adicionais, consulte o artigo [topologias de rede para migrações de instância gerenciada do BD SQL do Azure usando o serviço de migração de banco de dados do Azure](https://aka.ms/dmsnetworkformi).

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

4. Na tela **novo projeto de migração** , especifique um nome para o projeto, na caixa de texto **tipo de servidor de origem** , selecione **SQL Server**, na caixa de texto tipo de **servidor de destino** , selecione **instância gerenciada do banco de dados SQL do Azure**e, para **escolher tipo de atividade**, selecione **migração de pacote SSIS**.

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

1. Na tela **detalhes do destino de migração** , especifique os detalhes de conexão para o destino.

     ![Detalhes de destino](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Selecione **Guardar**.

## <a name="review-the-migration-summary"></a>Rever o resumo da migração

1. No ecrã **Resumo da migração**, na caixa de texto **Nome da atividade**, indique um nome para a atividade de migração.

2. Para os **projetos do SSIS e a opção de substituição de ambiente (s)** , especifique se deseja substituir ou ignorar projetos e ambientes SSIS existentes.

    ![Resumo do projeto de migração](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Reveja e verifique os detalhes associados ao projeto de migração.

## <a name="run-the-migration"></a>Executar a migração

* Selecione **Executar a migração**.

## <a name="next-steps"></a>Passos seguintes

* Examine as diretrizes de migração no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.
