---
title: Use o portal Azure para implementar app de catálogo de serviços
description: Mostra aos consumidores de Aplicações Geridas como implementar uma aplicação de catálogo de serviços através do portal Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ce58fc69496f54c078b0a0a55a8a3c7cad82a051
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391718"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Quickstart: Implementar app de catálogo de serviços através do portal Azure

No [arranque anterior,](publish-service-catalog-app.md)publicou uma definição de candidatura gerida. Neste arranque rápido, cria-se uma aplicação de catálogo de serviços a partir dessa definição.

## <a name="create-service-catalog-app"></a>Criar aplicativo de catálogo de serviços

No portal Azure, utilize os seguintes passos:

1. Selecione **Criar um recurso**.

   ![Criar um recurso](./media/deploy-service-catalog-quickstart/create-new.png)

1. Procure a **Aplicação Gerida** pelo Catálogo de Serviços e selecione-a a partir das opções disponíveis.

   ![Procurar aplicação de catálogo de serviços](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Veja uma descrição do serviço de Aplicação Gerida. Selecione **Criar**.

   ![Selecione criar](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. O portal mostra as definições de aplicação geridas a que tem acesso. A partir das definições disponíveis, selecione a que pretende implementar. Neste arranque rápido, utilize a definição de Conta de **Armazenamento Gerida** que criou no arranque rápido anterior. Selecione **Criar**.

   ![Selecione definição para implementar](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Forneça valores para o separador **Basics.** Selecione a subscrição Azure para implementar a sua aplicação de catálogo de serviços para. Criar um novo grupo de recursos chamado **applicationGroup**. Selecione um local para a sua aplicação. Quando terminar, selecione **OK**.

   ![Fornecer valores para o básico](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Forneça um prefixo para o nome da conta de armazenamento. Selecione o tipo de conta de armazenamento para criar. Quando terminar, selecione **OK**.

   ![Fornecer valores para armazenamento](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Reveja o resumo. Após o sucesso da validação, selecione **OK** para iniciar a implementação.

   ![Ver resumo](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Ver resultados

Depois de a aplicação do catálogo de serviços ter sido implementada, tem dois novos grupos de recursos. Um grupo de recursos detém a aplicação de catálogo de serviços. O outro grupo de recursos detém os recursos para a aplicação de catálogo de serviços.

1. Consulte o grupo de recursos nomeado **applicationGroup** para ver a aplicação do catálogo de serviços.

   ![Visualizar aplicação](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Consulte o grupo de recursos nomeado **applicationGroup{hash-characters}** para ver os recursos para a aplicação de catálogo de serviços.

   ![Visualizar recursos](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Passos seguintes

* Para aprender a criar os ficheiros de definição para uma aplicação gerida, consulte [Criar e publicar uma definição de aplicação gerida](publish-service-catalog-app.md).
* Para o Azure CLI, consulte a aplicação de catálogo de [serviços Deploy com o Azure CLI](./scripts/managed-application-cli-sample-create-application.md).
* Para o PowerShell, consulte a aplicação de catálogo de [serviços Deploy com powerShell](./scripts/managed-application-poweshell-sample-create-application.md).
