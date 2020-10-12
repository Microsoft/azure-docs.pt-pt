---
title: Adicionar recursos a ambientes de serviço de integração
description: Adicione aplicativos lógicos, contas de integração, conectores personalizados e conectores geridos ao seu ambiente de serviço de integração (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: dc4798df05b760074ff06d95d9712204a3cf3e5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269748"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Adicione recursos ao seu ambiente de serviço de integração (ISE) em Azure Logic Apps

Depois de criar um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)adicione recursos como aplicações lógicas, contas de integração e conectores para que possam aceder aos recursos na sua rede virtual Azure. Por exemplo, os conectores ISE geridos que ficam disponíveis depois de criar o seu ISE não aparecem automaticamente no Logic App Designer. Antes de poder utilizar estes conectores ISE, tem de adicionar e implantar manualmente [esses conectores no seu ISE para](#add-ise-connectors-environment) que estes apareçam no Logic App Designer.

> [!IMPORTANT]
> Para que as aplicações lógicas e contas de integração funcionem em conjunto num ISE, ambos devem usar o *mesmo ISE que* a sua localização.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O ISE que criou para executar as suas aplicações lógicas. Se não tiver um ISE, [crie primeiro um ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Para criar, adicionar ou atualizar recursos que são implantados para um ISE, precisa de ser atribuído o papel de Proprietário ou Contribuinte nesse ISE, ou tem permissões herdadas através da subscrição Azure ou do grupo de recursos Azure associados ao ISE. Para pessoas que não têm permissões de proprietário, contribuinte ou herdadas, podem ser atribuídas a função de Contribuidor do Ambiente do Serviço de Integração ou o Papel de Desenvolvedor do Ambiente do Serviço de Integração. Para obter mais informações, veja [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Criar aplicativos lógicos

Para construir aplicativos lógicos que funcionam no seu ambiente de serviço de integração (ISE), siga estes passos:

1. Encontre e abra o seu ISE, se não já estiver aberto. A partir do menu ISE, em **Definições,** selecione **Aplicações Lógica**  >  **Adicionar**.

   ![Adicione nova app lógica ao ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Forneça informações sobre a aplicação lógica que pretende criar, por exemplo:

   ![Screenshot que mostra a janela "Logic App" "Create" com informações de exemplo inseridas.](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome para a app lógica para criar |
   | **Subscrição** | Sim | O nome para a subscrição Azure para usar |
   | **Grupo de recursos** | Sim | O nome para o grupo de recursos Azure (novo ou existente) para usar |
   | **Localização** | Sim | Em **ambientes de serviço de integração,** selecione o ISE para utilizar, se ainda não selecionado. <p><p> **Importante**: Para utilizar as suas aplicações lógicas com uma conta de integração, ambas devem utilizar o mesmo ISE. |
   ||||

1. Quando concluir, selecione **Criar**.

1. Continue a [criar a sua aplicação lógica da forma habitual.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   Para diferenças na forma como os gatilhos e ações funcionam e como são rotulados quando se usa um ISE em comparação com o serviço de Aplicações Lógicas multi-arrendatários, consulte [Isolado versus multi-inquilino na visão geral do ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

1. Para gerir aplicações lógicas e conexões API no seu ISE, consulte [Gerir o seu ambiente de serviço de integração.](../logic-apps/ise-manage-integration-service-environment.md)

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Criar contas de integração

Com base no [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, o seu ISE inclui o uso específico da conta de integração sem custos adicionais. As aplicações lógicas que existem num ambiente de serviço de integração (ISE) só podem referenciar contas de integração que existam no mesmo ISE. Assim, para que uma conta de integração funcione com aplicações lógicas num ISE, tanto a conta de integração como as aplicações lógicas devem usar o *mesmo ambiente* que a sua localização. Para obter mais informações sobre contas de integração e ISEs, consulte [contas de integração com o ISE.](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)

Para criar uma conta de integração que utilize um ISE, siga estes passos:

1. Encontre e abra o seu ISE, se não já estiver aberto. A partir do menu ISE, em **Definições,** selecione **Contas de Integração**  >  **Adicionar**.

   ![Adicionar nova conta de integração ao ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Forneça informações sobre a aplicação lógica que pretende criar, por exemplo:

   ![Selecione ambiente de serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome** | Sim | O nome da conta de integração que pretende criar |
   | **Subscrição** | Sim | O nome da subscrição Azure que pretende usar |
   | **Grupo de recursos** | Sim | O nome para o grupo de recursos Azure (novo ou existente) para usar |
   | **Escalão de preço** | Sim | O nível de preços a utilizar para a conta de integração |
   | **Localização** | Sim | Em **ambientes de serviço de Integração,** selecione o mesmo ISE que as suas aplicações lógicas utilizam, se ainda não selecionadas. <p><p> **Importante**: Para utilizar a sua conta de integração com aplicações lógicas, ambas devem utilizar o mesmo ISE. |
   ||||

1. Quando concluir, selecione **Criar**.

1. [Ligue a sua aplicação lógica à sua conta de integração da forma habitual.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Continue adicionando recursos à sua conta de integração, como [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [acordos.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Para gerir contas de integração no seu ISE, consulte [Gerir o seu ambiente de serviço de integração.](../logic-apps/ise-manage-integration-service-environment.md)

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Adicionar conectores ISE

Os conectores geridos pela Microsoft que ficam disponíveis depois de criar o seu ISE não aparecem automaticamente no conector no Logic App Designer. Antes de poder utilizar estes conectores ISE, tem de adicionar e implantar manualmente estes conectores no seu ISE para que estes apareçam no Logic App Designer.

1. No seu menu ISE, em **Definições,** selecione **Conectores Geridos**. Na barra de ferramentas, **selecione Adicionar**.

   ![Ver conectores geridos](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. No Add um novo painel **de conector gerido,** abra a lista **de conector Find.** Selecione o conector ISE que pretende utilizar mas ainda não está implantado no seu ISE. Selecione **Criar**.

   ![Selecione o conector ISE que pretende implantar no seu ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Apenas os conectores ISE que são elegíveis mas ainda não implantados no seu ISE aparecem disponíveis para você selecionar. Os conectores que já estão implantados no seu ISE parecem não estar disponíveis para seleção.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Criar conectores personalizados

Para utilizar conectores personalizados no seu ISE, crie esses conectores personalizados diretamente dentro do seu ISE.

1. Encontre e abra o seu ISE, se não já estiver aberto. A partir do menu ISE, em **Definições**, selecione **conectores personalizados**  >  **Adicione**.

   ![Criar conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Forneça o nome, subscrição Azure e grupo de recursos Azure (novo ou existente) para usar para o seu conector personalizado.

1. A partir da lista **Localização,** na secção ambientes de **serviço de Integração,** selecione o mesmo ISE que as suas aplicações lógicas usam, e selecione **Criar,** por exemplo:

   ![Screenshot que mostra a janela "Create Logic Apps Custom Connector" com informações de exemplo selecionadas.](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Selecione o seu novo conector personalizado e, em seguida, **selecione Editar,** por exemplo:

   ![Selecione e edite o conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continue criando o conector da forma habitual a partir de uma [definição OpenAPI](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para gerir os conectores personalizados no seu ISE, consulte [Gerir o seu ambiente de serviço de integração.](../logic-apps/ise-manage-integration-service-environment.md)

## <a name="next-steps"></a>Passos seguintes

* [Gerir ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
