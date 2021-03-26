---
title: 'Quickstart: Criar um caso utilizando o portal Azure'
titleSuffix: Azure Database Migration Service
description: Utilize o portal Azure para criar uma instância do Serviço de Migração da Base de Dados Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/29/2021
ms.openlocfilehash: 8a500104a0273b9e131815c4dc832bd33729cd51
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566596"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Início Rápido: Criar uma instância do Azure Database Migration Service com o portal do Azure

Neste arranque rápido, você usa o portal Azure para criar uma instância do Azure Database Migration Service. Depois de criar o caso, pode usá-lo para migrar dados de várias fontes de base de dados para plataformas de dados Azure, tais como do SQL Server para Azure SQL Database ou do SQL Server para uma Azure SQL Managed Instance.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser, navegue para o [portal do Microsoft Azure](https://portal.azure.com/) e, em seguida, introduza as suas credenciais para iniciar sessão no portal. A vista predefinida é o dashboard de serviço.

> [!NOTE]
> Pode criar até 10 casos de DMS por subscrição por região. Se necessitar de um maior número de ocorrências, por favor crie um bilhete de apoio.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Registe o fornecedor de recursos Microsoft.DataMigration antes de criar a primeira instância do Database Migration Service.

1. No portal Azure, procure e selecione **Subscrições.**

   ![Mostrar subscrições no portal](media/quickstart-create-data-migration-service-portal/portal-select-subscription.png)

2. Selecione a subscrição na qual pretende criar a instância do Serviço de Migração da Base de Dados Azure e, em seguida, selecione **fornecedores de Recursos**.

    ![Mostrar fornecedores de recursos](media/quickstart-create-data-migration-service-portal/portal-select-resource-provider.png)

3. Procure por migração e, em seguida, **selecione Register** for **Microsoft.DataMigration**.

    ![Registar o fornecedor de recursos](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Criar uma instância do serviço

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**. Procure e selecione **O Serviço de Migração da Base de Dados Azure.**

    ![Azure Marketplace](media/quickstart-create-data-migration-service-portal/portal-marketplace.png)

2. No ecrã **Azure Database Migration Service**, selecione **Criar**.

    ![Criar instância do Azure Database Migration Service](media/quickstart-create-data-migration-service-portal/dms-create.png)

3. No ecrã básico do **Serviço de Migração Create:**

     - Selecione uma subscrição.
     - Crie um novo grupo de recursos ou escolha um existente.
     - Especifique um nome para o exemplo do Serviço de Migração da Base de Dados Azure.
     - Selecione a localização em que pretende criar a instância do Serviço de Migração da Base de Dados Azure.
     - Escolha **Azure** como o modo de serviço.
     - Selecione um escalão de preço. Para obter mais informações sobre os custos e os escalões de preços, veja a [página de preços](https://aka.ms/dms-pricing).
     
    ![Configurar a azure Database Migration Service definições básicas](media/quickstart-create-data-migration-service-portal/dms-create-basics.png)

     - Selecione Seguinte: Rede.

4. No ecrã de rede **do Serviço de Migração Create:**

    - Selecione uma rede virtual existente ou crie uma nova. A rede virtual fornece ao Azure Database Migration Service acesso à base de dados de origem e ao ambiente alvo. Para obter mais informações sobre como criar uma rede virtual no portal Azure, consulte o artigo [Criar uma rede virtual utilizando o portal Azure](../virtual-network/quick-create-portal.md).

    ![Configurar configurações de rede do Serviço de Migração de Bases de Dados Azure](media/quickstart-create-data-migration-service-portal/dms-network-settings.png)

    - Selecione **Review + Criar** para criar o serviço. 
    
    - Após alguns momentos, o seu caso de serviço de migração de bases de dados Azure é criado e pronto a ser utilizado:

    ![O serviço de migração foi criado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Pode limpar os recursos criados neste quickstart eliminando o [grupo de recursos Azure.](../azure-resource-manager/management/overview.md) Para eliminar o grupo de recursos, navegue para a instância do Azure Database Migration Service que criou. Selecione o nome do **Grupo de recursos** e selecione **Eliminar grupo de recursos**. Esta ação elimina todos os recursos no grupo de recursos, bem como o próprio grupo.

## <a name="next-steps"></a>Passos seguintes

* [Migrar o SQL Server para a Base de Dados SQL do Azure offline](tutorial-sql-server-to-azure-sql.md)
* [Migrar o SQL Server para a Base de Dados SQL do Azure online](./tutorial-sql-server-to-azure-sql.md)
* [Migrar o SqL Server para um Azure SQL Managed Instance offline](tutorial-sql-server-to-managed-instance.md)
* [Migrar o SQL Server para um Azure SQL Managed Instance online](tutorial-sql-server-managed-instance-online.md)