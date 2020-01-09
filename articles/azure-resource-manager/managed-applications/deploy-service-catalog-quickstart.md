---
title: Usar portal do Azure para implantar o aplicativo de catálogo de serviços
description: Mostra os consumidores de aplicativos gerenciados como implantar um aplicativo de catálogo de serviços por meio do portal do Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 3fa9709e096e908907772c940fc5e2f2895b7eb3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650789"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Implantar o aplicativo de catálogo de serviços por meio de portal do Azure

No guia de [início rápido anterior](publish-managed-app-definition-quickstart.md), você publicou uma definição de aplicativo gerenciado. Neste guia de início rápido, você cria um aplicativo de catálogo de serviços a partir dessa definição.

## <a name="create-service-catalog-app"></a>Criar aplicativo de catálogo de serviços

No portal do Azure, use as seguintes etapas:

1. Selecione **criar um recurso**.

   ![Criar um recurso](./media/deploy-service-catalog-quickstart/create-new.png)

1. Pesquise **aplicativo gerenciado pelo catálogo de serviços** e selecione-o nas opções disponíveis.

   ![Pesquisar aplicativo de catálogo de serviços](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Você verá uma descrição do serviço de aplicativo gerenciado. Selecione **Criar**.

   ![Selecione criar](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. O portal mostra as definições de aplicativos gerenciados aos quais você tem acesso. Nas definições disponíveis, selecione aquela que você deseja implantar. Neste guia de início rápido, use a definição da **conta de armazenamento gerenciado** que você criou no guia de início rápido anterior. Selecione **Criar**.

   ![Selecione a definição a ser implantada](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Forneça valores para a guia **noções básicas** . Selecione a assinatura do Azure para implantar seu aplicativo de catálogo de serviços. Crie um novo grupo de recursos **chamado**FileGroup. Selecione um local para seu aplicativo. Quando terminar, selecione **OK**.

   ![Forneça valores para básico](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Forneça um prefixo para o nome da conta de armazenamento. Selecione o tipo de conta de armazenamento a ser criada. Quando terminar, selecione **OK**.

   ![Fornecer valores para armazenamento](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Reveja o resumo. Após a validação ser realizada com sucesso, selecione **OK** para iniciar a implantação.

   ![Exibir Resumo](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Ver resultados

Depois que o aplicativo do catálogo de serviços tiver sido implantado, você terá dois novos grupos de recursos. Um grupo de recursos contém o aplicativo de catálogo de serviços. O outro grupo de recursos contém os recursos para o aplicativo de catálogo de serviços.

1. Exiba o grupo de recursos chamado FileGroup para ver o aplicativo do **Catálogo de serviços** .

   ![Ver a aplicação](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Exiba o grupo de recursos chamado FileGroup **{hash-Characters}** para ver os recursos do aplicativo de catálogo de serviços.

   ![Exibir recursos](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber como criar os arquivos de definição para um aplicativo gerenciado, consulte [criar e publicar uma definição de aplicativo gerenciado](publish-service-catalog-app.md).
* Para CLI do Azure, consulte [implantar aplicativo de catálogo de serviços com CLI do Azure](./scripts/managed-application-cli-sample-create-application.md).
* Para o PowerShell, consulte [implantar aplicativo do catálogo de serviços com o PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).