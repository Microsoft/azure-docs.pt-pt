---
title: Adicionar artefatos a ISEs (ambientes de serviço de integração) nos aplicativos lógicos do Azure
description: Adicione aplicativos lógicos, conectores personalizados e contas de integração ao seu ISE (ambiente do serviço de integração) para acessar redes virtuais do Azure (VNETs), mantendo-se privado e isolado do Azure público ou "global"
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: df43b52514eebc3216dbec01cff0d8a3b14e7940
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517660"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Adicionar artefatos ao seu ISE (ambiente do serviço de integração) nos aplicativos lógicos do Azure

Depois de criar um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), adicione artefatos como aplicativos lógicos, contas de integração e conectores personalizados para que eles possam acessar os recursos em sua rede virtual do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O ISE que você criou para executar seus aplicativos lógicos. Se você não tiver um ISE, [crie um ISE primeiro](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps-in-an-ise"></a>Criar aplicativos lógicos em um ISE

Para criar aplicativos lógicos que são executados em seu ISE (ambiente do serviço de integração), siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **aplicativos** > lógicos**Adicionar**.

   ![Adicionar novo aplicativo lógico ao ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -ou-

   No menu principal do Azure, selecione **criar um recurso** > **aplicativo lógico**de**integração** > .

1. Forneça o nome, a assinatura do Azure e o grupo de recursos do Azure (novo ou existente) a serem usados para seu aplicativo lógico.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o ISE, por exemplo:

   ![Selecione o ambiente do serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Se você quiser usar seus aplicativos lógicos com uma conta de integração, esses aplicativos lógicos e a conta de integração deverão usar o mesmo ISE.

1. Continue [criando seu aplicativo lógico da maneira usual](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Para obter diferenças em como os gatilhos e as ações funcionam e como eles são rotulados quando você usa um ISE em comparação com o serviço de aplicativos lógicos globais, consulte [isolado versus global na visão geral do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Para gerenciar aplicativos lógicos e conexões de API no ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts-in-an-ise"></a>Criar contas de integração em um ISE

Com base no [SKU do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, seu ISE inclui um uso de conta de integração específico sem custo adicional. Os aplicativos lógicos que existem em um ambiente do serviço de integração (ISE) podem referenciar somente as contas de integração que existem no mesmo ISE. Portanto, para que uma conta de integração funcione com aplicativos lógicos em um ISE, tanto a conta de integração quanto os aplicativos lógicos devem usar o *mesmo ambiente* que o local. Para obter mais informações sobre contas de integração e ISEs [, consulte contas de](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
)integração com o ISE.

Para criar uma conta de integração que usa um ISE, siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **contas** > de integração**Adicionar**.

   ![Adicionar nova conta de integração ao ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -ou-

   No menu principal do Azure, selecione **criar uma** > **conta de integração**de**integração** > de recursos.

1. Forneça o nome, a assinatura do Azure, o grupo de recursos do Azure (novo ou existente) e o tipo de preço a ser usado para sua conta de integração.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam, por exemplo:

   ![Selecione o ambiente do serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Vincule seu aplicativo lógico à sua conta de integração da maneira usual](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continue adicionando artefatos à sua conta de integração, como [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Para gerenciar contas de integração no ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors-in-an-ise"></a>Criar conectores personalizados em um ISE

Para usar conectores personalizados no ISE, crie esses conectores personalizados diretamente dentro do ISE.

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **conectores** > personalizados**Adicionar**.

   ![Criar conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Forneça o nome, a assinatura do Azure e o grupo de recursos do Azure (novo ou existente) a serem usados para o conector personalizado.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam e selecione **criar**, por exemplo:

   ![Selecione o ambiente do serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Selecione o novo conector personalizado e, em seguida, selecione **Editar**, por exemplo:

   ![Selecionar e editar conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continue criando o conector da maneira usual de uma definição de [openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para gerenciar conectores personalizados em seu ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Passos seguintes

* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
