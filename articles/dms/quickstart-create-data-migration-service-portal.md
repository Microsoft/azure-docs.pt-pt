---
title: 'Início rápido: criar uma instância usando o portal do Azure'
titleSuffix: Azure Database Migration Service
description: Use o portal do Azure para criar uma instância do serviço de migração de banco de dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 01/08/2020
ms.openlocfilehash: 42638af9f51770a9a5738cd92f91c28439af273a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750094"
---
# <a name="quickstart-create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Início Rápido: Criar uma instância do Azure Database Migration Service com o portal do Azure

Neste guia de início rápido, você usa o portal do Azure para criar uma instância do serviço de migração de banco de dados do Azure.  Depois de criar o serviço, você pode usá-lo para migrar dados do SQL Server local para o banco de dado SQL do Azure.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Abra o browser, navegue para o [portal do Microsoft Azure](https://portal.azure.com/) e, em seguida, introduza as suas credenciais para iniciar sessão no portal.

A vista predefinida é o dashboard de serviço.

## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos

Registe o fornecedor de recursos Microsoft.DataMigration antes de criar a primeira instância do Database Migration Service.

1. No portal do Azure, selecione **Todos os serviços** e, em seguida, selecione **Subscrições**.

2. Selecione a assinatura na qual você deseja criar a instância do serviço de migração de banco de dados do Azure e, em seguida, selecione **provedores de recursos**.

3. Procure por migração e, à direita de **Microsoft.DataMigration**, selecione **Registar**.

    ![Registar o fornecedor de recursos](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Criar uma instância do serviço

1. Selecione +**criar um recurso** para criar uma instância do serviço de migração de banco de dados do Azure.

2. Procure "migração" no marketplace, selecione **Azure Database Migration Service** e, em seguida, no ecrã **Azure Database Migration Service** selecione **Criar**.

3. No ecrã **Criar Serviço de Migração**:

    - Escolha um **nome de serviço** que seja fácil de memorizar e exclusivo para identificar sua instância do serviço de migração de banco de dados do Azure.
    - Selecione a sua **Subscrição** do Azure, na qual pretende criar a instância.
    - Selecione um **Grupo de Recursos** existente ou crie um novo.
    - Escolha a **Localização** que esteja mais próxima do seu servidor de origem ou de destino.
    - Selecione uma **rede virtual** existente ou crie uma.

        A rede virtual fornece o serviço de migração de banco de dados do Azure com acesso ao banco de dados de origem e ao ambiente de destino.

        Para obter mais informações sobre como criar uma rede virtual no portal do Azure, consulte o artigo [criar uma rede virtual usando o portal do Azure](https://aka.ms/vnet).

    - Selecione Básico: 1 vCore para o **Escalão de preço**.

        ![Criar serviço de migração](media/quickstart-create-data-migration-service-portal/dms-create-service1.png)

4. Selecione **Criar**.

    Após alguns instantes, sua instância do serviço de migração de banco de dados do Azure é criada e pronta para uso. O serviço de migração de banco de dados do Azure é exibido conforme mostrado na imagem a seguir:

    ![O serviço de migração foi criado](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Limpar recursos

Pode limpar os recursos criados neste Início Rápido ao eliminar o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md). Para eliminar o grupo de recursos, navegue para a instância do Azure Database Migration Service que criou. Selecione o nome do **Grupo de recursos** e selecione **Eliminar grupo de recursos**. Esta ação elimina todos os recursos no grupo de recursos, bem como o próprio grupo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar o SQL Server local para a Base de Dados SQL do Azure](tutorial-sql-server-to-azure-sql.md)