---
title: Utilizar o portal do Azure para implementar a aplicação do catálogo de serviço | Documentos da Microsoft
description: Mostra os consumidores das aplicações geridas como implementar uma aplicação do catálogo de serviço através do portal do Azure.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589047"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Implementar o serviço catálogo de aplicações através do portal do Azure

Na [antes do início rápido](publish-managed-app-definition-quickstart.md), publicou uma definição da aplicação gerida. Neste início rápido, vai criar uma aplicação do catálogo de serviço dessa definição.

## <a name="create-service-catalog-app"></a>Criar aplicação do catálogo de serviço

No portal do Azure, utilize os seguintes passos:

1. Selecione **criar um recurso**.

   ![Criar um recurso](./media/deploy-service-catalog-quickstart/create-new.png)

1. Procure **aplicação gerida do catálogo de serviço** e selecione-o nas opções disponíveis.

   ![Procure a aplicação do catálogo de serviço](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Verá uma descrição do serviço de aplicações geridas. Selecione **Criar**.

   ![Selecione criar](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. O portal mostra as definições de aplicação gerida que tem acesso. Das definições disponíveis, selecione aquela que pretende implementar. Neste início rápido, utilize o **conta de armazenamento gerida** definição que criou no início rápido anterior. Selecione **Criar**.

   ![Selecione a definição para implementar](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Fornecer valores para o **Noções básicas** separador. Selecione a subscrição do Azure para implementar a sua aplicação do catálogo de serviço para. Criar um novo grupo de recursos chamado **applicationGroup**. Selecione uma localização para a sua aplicação. Quando terminar, selecione **OK**.

   ![Fornecer valores para o básico](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Fornece um prefixo para o nome da conta de armazenamento. Selecione o tipo de conta de armazenamento para criar. Quando terminar, selecione **OK**.

   ![Forneça valores para o armazenamento](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Reveja o resumo. Depois da validação tiver êxito, selecione **OK** para iniciar a implantação.

   ![Ver resumo](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Ver resultados

Depois de implementar a aplicação do catálogo de serviço, tem dois novos grupos de recursos. Um grupo de recursos contém o serviço de catálogo de aplicações. O grupo de recursos contém os recursos para o serviço de catálogo de aplicações.

1. Ver o grupo de recursos com o nome **applicationGroup** para ver a aplicação do catálogo de serviço.

   ![Aplicação de vista](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Ver o grupo de recursos com o nome **applicationGroup {carateres de hash}** para ver os recursos para a aplicação do catálogo de serviço.

   ![Ver recursos](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Passos Seguintes

* Para saber como criar os ficheiros de definição de um aplicativo gerenciado, veja [criar e publicar uma definição da aplicação gerida](publish-service-catalog-app.md).
* Para a CLI do Azure, consulte [implementar serviço catálogo de aplicações com a CLI do Azure](./scripts/managed-application-cli-sample-create-application.md).
* Para o PowerShell, consulte [implementar serviço catálogo de aplicações com o PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).