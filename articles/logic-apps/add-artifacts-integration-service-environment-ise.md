---
title: Adicionar recursos aos ambientes de serviçode integração
description: Adicione aplicações lógicas, contas de integração, conectores personalizados e conectores geridos ao seu ambiente de serviço de integração (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: d6e5eb8875e6b7d930a002708079dab0e357297f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250932"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Adicione recursos ao seu ambiente de serviço de integração (ISE) em Aplicações Lógicas Azure

Depois de criar um ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)adicione recursos como aplicações lógicas, contas de integração e conectores para que possam aceder aos recursos na sua rede virtual Azure. Por exemplo, os conectores ISE geridos que ficam disponíveis depois de criar o seu ISE não aparecem automaticamente no Logic App Designer. Antes de poder utilizar estes conectores ISE, tem de adicionar e implantar manualmente [esses conectores no ISE](#add-ise-connectors-environment) para que apareçam no Logic App Designer.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O ISE que criou para executar as suas aplicações lógicas. Se não tiver um ISE, [crie primeiro um ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Para criar, adicionar ou atualizar recursos que são implantados num ISE, é necessário atribuir-lhe o papel de Proprietário ou Colaborador nesse ISE, ou tem permissões herdadas através da subscrição Azure ou do grupo de recursos Azure associado ao ISE. Para pessoas que não tenham permissões de proprietário, contribuinte ou herdadas, podem ser atribuídas ao papel de Contribuidor ambiental do Serviço de Integração ou ao Papel de Desenvolvedor de Ambiente do Serviço de Integração. Para obter mais informações sobre o controlo de acesso baseado em papéis (RBAC), ver [O que é o controlo de acesso baseado em papéis (RBAC) para recursos Azure?](../role-based-access-control/overview.md)

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Criar aplicativos lógicos

Para construir aplicações lógicas que funcionam no seu ambiente de serviço de integração (ISE), siga estes passos:

1. Encontre e abra o seu ISE, se ainda não estiver aberto. A partir do menu ISE, em **Definições,** selecione **aplicações lógicas** > **Adicionar**.

   ![Adicione nova aplicação lógica ao ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Forneça informações sobre a aplicação lógica que pretende criar, por exemplo:

   ![Selecione ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome para a aplicação lógica para criar |
   | **Subscrição** | Sim | O nome da subscrição Azure para usar |
   | **Grupo de recursos** | Sim | O nome para o grupo de recursos Azure (novo ou existente) para usar |
   | **Localização** | Sim | No âmbito dos ambientes de **serviço integração,** selecione o ISE para utilizar, se ainda não estiver selecionado. <p><p> **Importante**: Para utilizar as suas aplicações lógicas com uma conta de integração, ambas devem utilizar o mesmo ISE. |
   ||||

1. Quando terminar, selecione **Criar**.

1. Continue a [criar a sua aplicação lógica da forma habitual.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   Para diferenças na forma como os gatilhos e as ações funcionam e como são rotulados quando se usa um ISE em comparação com o serviço global de Aplicações Lógicas, consulte [Isolado versus global na visão geral do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Para gerir aplicações lógicas e conexões API no seu ISE, consulte Gerir o seu ambiente de serviço de [integração.](../logic-apps/ise-manage-integration-service-environment.md)

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Criar contas de integração

Com base no [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, o ise inclui o uso específico da conta de integração sem custos adicionais. As aplicações lógicas que existem num ambiente de serviço de integração (ISE) só podem fazer referência a contas de integração que existam no mesmo ISE. Assim, para que uma conta de integração funcione com aplicações lógicas num ISE, tanto a conta de integração como as aplicações lógicas devem usar o *mesmo ambiente* que a sua localização. Para obter mais informações sobre contas de integração e ISEs, consulte [contas de Integração com o ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Para criar uma conta de integração que utilize um ISE, siga estes passos:

1. Encontre e abra o seu ISE, se ainda não estiver aberto. A partir do menu ISE, em **Definições,** selecione **contas de integração** > **Adicionar**.

   ![Adicione nova conta de integração ao ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Forneça informações sobre a aplicação lógica que pretende criar, por exemplo:

   ![Selecione ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome para a conta de integração que quer criar |
   | **Subscrição** | Sim | O nome da subscrição Azure que pretende utilizar |
   | **Grupo de recursos** | Sim | O nome para o grupo de recursos Azure (novo ou existente) para usar |
   | **Escalão de preço** | Sim | O nível de preços a utilizar para a conta de integração |
   | **Localização** | Sim | No âmbito dos ambientes de **serviço integração,** selecione o mesmo ISE que as suas aplicações lógicas utilizam, se ainda não for em selecionado. <p><p> **Importante**: Para utilizar a sua conta de integração com aplicações lógicas, ambas devem utilizar o mesmo ISE. |
   ||||

1. Quando terminar, selecione **Criar**.

1. [Ligue a sua aplicação lógica à sua conta de integração da forma habitual.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Continue adicionando recursos à sua conta de integração, como [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [acordos.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Para gerir as contas de integração no seu ISE, consulte Gerir o seu ambiente de serviço de [integração.](../logic-apps/ise-manage-integration-service-environment.md)

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Adicionar conectores ISE

Os conectores geridos pela Microsoft que ficam disponíveis depois de criar o seu ISE não aparecem automaticamente no conector do Designer de Aplicações Lógica. Antes de poder utilizar estes conectores ISE, tem de adicionar e implantar manualmente estes conectores ao ISE para que apareçam no Logic App Designer.

1. No menu ISE, em **Definições,** selecione **conectores geridos**. Na barra de ferramentas, selecione **Adicionar**.

   ![Ver conectores geridos](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. No Add um novo painel de **conector gerido,** abra a lista do **conector Find.** Selecione o conector ISE que pretende utilizar mas ainda não está implantado no ISE. Selecione **Criar**.

   ![Selecione o conector ISE que pretende implementar no seu ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Apenas os conectores ISE que são elegíveis mas ainda não estão implantados no ISE aparecem disponíveis para selecionar. Os conectores que já estão implantados no ISE não estão disponíveis para seleção.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Criar conectores personalizados

Para utilizar conectores personalizados no seu ISE, crie esses conectores personalizados a partir diretamente do seu ISE.

1. Encontre e abra o seu ISE, se ainda não estiver aberto. A partir do menu ISE, em **Definições,** selecione **conectores Personalizados** > **Adicionar**.

   ![Criar conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Forneça o nome, subscrição Azure e grupo de recursos Azure (novo ou existente) para usar para o seu conector personalizado.

1. A partir da lista **de Localização,** sob a secção de ambientes de **serviço integração,** selecione o mesmo ISE que as suas aplicações lógicas usam, e selecione **Create,** por exemplo:

   ![Selecione ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selecione o seu novo conector personalizado e, em seguida, selecione **Editar,** por exemplo:

   ![Selecione e edite o conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continue criando o conector da forma habitual a partir de uma [definição OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para gerir conectores personalizados no seu ISE, consulte Gerir o seu ambiente de serviço de [integração.](../logic-apps/ise-manage-integration-service-environment.md)

## <a name="next-steps"></a>Passos seguintes

* [Gerir ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
