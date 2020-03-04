---
title: Criar ou gerir contas de integração B2B
description: Criar, ligar e gerir contas de integração para integração empresarial com apps lógicas Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251002"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Criar e gerir contas de integração para integrações empresariais B2B em Aplicações Lógicas Azure

Antes de criar [soluções B2B e de integração empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md) com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md), precisa de criar uma conta de integração, que é um recurso separado do Azure que fornece um contentor seguro, dimensionável e gerível para os artefatos de integração que define e utiliza com os fluxos de trabalho da aplicação lógica.

Por exemplo, pode criar, armazenar e gerir artefactos B2B, tais como parceiros comerciais, acordos, mapas, schemas, certificados e configurações de lotes. Além disso, antes que a sua aplicação lógica possa trabalhar com estes artefactos e utilizar os conectores Logic Apps B2B, deve ligar a sua conta de [integração](#link-account) à sua aplicação lógica. Tanto a sua conta de integração como a aplicação lógica devem existir no *mesmo* local ou região.

> [!TIP]
> Para criar uma conta de integração dentro de um ambiente de serviço de [integração,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)consulte Criar contas de [integração num ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

Este tópico mostra-lhe como executar estas tarefas:

* Crie a sua conta de integração.
* Ligue a sua conta de integração a uma aplicação lógica.
* Altere o nível de preços para a sua conta de integração.
* Descoine a sua conta de integração a partir de uma aplicação lógica.
* Mova a sua conta de integração para outro grupo de recursos Azure ou subscrição.
* Apague a sua conta de integração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Criar uma conta de integração

Para esta tarefa, pode utilizar o portal Azure seguindo os passos nesta secção, [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount), ou [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, introduza a "conta de integração" como filtro e selecione Conta de **Integração**.

   ![Criar nova conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Em **Conta de Integração,** selecione **Criar**.

   ![Escolha "Adicionar" para criar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Forneça esta informação sobre a sua conta de integração:

   ![Fornecer detalhes da conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <> *de nome de conta de integração* | O nome da sua conta de integração, que pode conter apenas letras, números, hífens (`-`), sublinha (`_`), parênteses (`(`, `)`) e períodos (`.`). Este exemplo usa "Fabrikam-Integration". |
   | **Subscrição** | Sim | <*Azure-subscription-name*> | O nome para a subscrição do Azure |
   | **Grupo de recursos** | Sim | <> de nome de *grupo azure-recursos* | O nome para o grupo de [recursos Azure](../azure-resource-manager/management/overview.md) usar para organizar recursos relacionados. Para este exemplo, crie um novo grupo de recursos com o nome "FabrikamIntegration-RG". |
   | **Escalão de Preço** | Sim | <> *de nível de preços* | O nível de preços da conta de integração, que pode mudar mais tarde. Para este exemplo, selecione **Free**. Para mais informações, consulte estes tópicos: <p>modelo de preços de [apps lógicas](../logic-apps/logic-apps-pricing.md#integration-accounts) -  <p>limites e configuração de [aplicações lógicas](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) -  <p>[Preços de aplicações lógicas](https://azure.microsoft.com/pricing/details/logic-apps/) -  |
   | **Localização** | Sim | <> *da região de Azure* | A região onde armazenar os metadados da sua conta de integração. Ou selecione a mesma localização que a sua aplicação lógica, ou crie as suas aplicações lógicas no mesmo local que a sua conta de integração. Para este exemplo, use "West US". <p>**Nota:** Para criar uma conta de integração dentro de um ambiente de serviço de [integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), selecione esse ISE como localização. Para obter mais informações, consulte Criar contas de [integração num ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Não | Off, On | Mantenha o ajuste **off** para este exemplo. |
   |||||

1. Quando terminar, selecione **Criar**.

   Após a implementação concluída, o Azure abre a sua conta de integração.

   ![Azure abre conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Antes que a sua aplicação lógica possa usar a sua conta de integração, siga os próximos passos para ligar a sua conta de integração e app lógica.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Link para app lógica

Para dar às suas aplicações lógicas acesso a uma conta de integração que contenha os seus artefactos B2B, tem primeiro de ligar a sua conta de integração à sua aplicação lógica. Tanto a aplicação lógica como a conta de integração devem existir na mesma região. Para completar esta tarefa, pode utilizar o portal Azure. Se utilizar o Visual Studio e a sua aplicação lógica estiver num [projeto do Grupo de Recursos Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)pode ligar a sua aplicação lógica a uma conta de integração utilizando o Visual [Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. No [portal Azure,](https://portal.azure.com)abra uma aplicação lógica existente ou crie uma nova aplicação lógica.

1. No menu da sua aplicação lógica, em **Definições,** selecione **definições**de Fluxo de Trabalho . Na **conta integração,** abra a lista **De Integração Select.** Selecione a conta de integração para ligar à sua aplicação lógica.

   ![Selecione a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Para terminar a ligação, selecione **Guardar**.

   ![Selecione a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Depois da sua conta de integração estar ligada com sucesso, o Azure mostra uma mensagem de confirmação.

   ![Azure confirma ligação bem sucedida](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Agora a sua aplicação lógica pode usar os artefactos na sua conta de integração mais os conectores B2B, tais como validação XML e codificação de ficheiros planos ou descodificação.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Alterar escalão de preço

Para aumentar os [limites](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) para uma conta de integração, pode [fazer upgrade para um nível](#upgrade-pricing-tier)de preços mais elevado, se disponível. Por exemplo, pode atualizar do nível Livre para o nível Básico ou Nível Standard. Também pode [descer para um nível inferior,](#downgrade-pricing-tier)se disponível. Para obter mais informações sobre preços, consulte estes tópicos:

* [Preços de Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Nível de preços de upgrade

Para efetuar esta alteração, pode utilizar o portal Azure seguindo os passos nesta secção ou o [Azure CLI](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa principal do Azure, introduza "contas de integração" como filtro e selecione **contas integrativas.**

   ![Encontre conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração nas suas subscrições do Azure.

1. Nas contas de **Integração,** selecione a conta de integração que pretende mover. No menu da sua conta de integração, selecione **Visão Geral**.

   ![No menu de conta de integração, selecione "Visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel overview, selecione O nível de **preços de upgrade,** que lista quaisquer níveis mais elevados disponíveis. Quando se leciona um nível, a alteração faz efeito imediatamente.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>CLI do Azure

1. Se ainda não o fez, [instale os pré-requisitos do Azure CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. No portal Azure, abra o ambiente Azure [**Cloud Shell.** ](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No pedido de comando, entre no comando de [ **recursos az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)e coloque `skuName` para o nível mais alto que desejar.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Por exemplo, se tiver o nível Básico, pode definir `skuName` para `Standard`:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Nível de preços de desvalorização

Para efazer esta alteração, utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Se ainda não o fez, [instale os pré-requisitos do Azure CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. No portal Azure, abra o ambiente Azure [**Cloud Shell.** ](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No pedido de comando, entre no comando de [ **recursos az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) e coloque `skuName` para o nível inferior que desejar.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Por exemplo, se tiver o nível Standard, pode definir `skuName` para `Basic`:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Desvinculação da aplicação lógica

Se pretender ligar a sua aplicação lógica a outra conta de integração, ou deixar de utilizar uma conta de integração com a sua aplicação lógica, elimine o link utilizando o Azure Resource Explorer.

1. Abra a janela do seu navegador e vá ao [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Inscreva-se com as mesmas credenciais de conta Azure.

   ![Explorador de Recursos do Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Na caixa de pesquisa, introduza o nome da sua aplicação lógica para que possa encontrar e selecionar a sua aplicação lógica.

   ![Localizar e selecionar app lógica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na barra de título do explorador, selecione **Ler/Escrever**.

   ![Ligue o modo "Ler/Escrever"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. No separador **Dados,** **selecione Editar**.

   ![No separador "Dados", selecione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. No editor, encontre o objeto `integrationAccount`, e elimine esse imóvel, que tem este formato:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por exemplo:

   ![Encontre o objeto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. No separador **Dados,** selecione **Colocar** para guardar as alterações.

   ![Para guardar alterações, selecione "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. No portal Azure, encontre e selecione a sua aplicação lógica. De acordo com **as definições**de Workflow da sua aplicação, verifique se a propriedade da **conta integration** agora parece vazia.

   ![Verifique se a conta de integração não está ligada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover conta de integração

Pode mover a sua conta de integração para outro grupo de recursos Azure ou subscrição Azure. Ao movimentar recursos, o Azure cria novos IDs de recursos, por isso certifique-se de que utiliza os novos IDs e atualize quaisquer scripts ou ferramentas associadas aos recursos movidos. Se pretender alterar a subscrição, deve também especificar um grupo de recursos existente ou novo.

Para esta tarefa, pode utilizar o portal Azure seguindo os passos nesta secção ou o [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa principal do Azure, introduza "contas de integração" como filtro e selecione **contas integrativas.**

   ![Encontre conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração nas suas subscrições do Azure.

1. Nas contas de **Integração,** selecione a conta de integração que pretende mover. No menu da sua conta de integração, selecione **Visão Geral**.

   ![No menu de conta de integração, selecione "Visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Ao lado de qualquer **grupo de recursos** ou nome de **subscrição,** selecione **alteração**.

   ![Alterar grupo de recursos ou subscrição](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecione quaisquer recursos relacionados que também queira mover.

1. Com base na sua seleção, siga estes passos para alterar o grupo de recursos ou subscrição:

   * Grupo de recursos: Da lista do **grupo Derecursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo**de recursos .

   * Subscrição: A partir da lista de **subscrição,** selecione a subscrição do destino. A partir da lista de **grupos de Recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **Criar um novo grupo**de recursos .

1. Para reconhecer a sua compreensão de que quaisquer scripts ou ferramentas associadas aos recursos movidos não funcionarão até que os atualize com os novos IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

1. Depois de terminar, certifique-se de atualizar todos e quaisquer scripts com as novas iDs de recursos para os seus recursos movidos.  

## <a name="delete-integration-account"></a>Eliminar conta de integração

Para esta tarefa, pode utilizar o portal Azure seguindo os passos nesta secção, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete), ou [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa principal do Azure, introduza "contas de integração" como filtro e selecione **contas integrativas.**

   ![Encontre conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração nas suas subscrições do Azure.

1. Nas **contas de Integração,** selecione a conta de integração que pretende eliminar. No menu da sua conta de integração, selecione **Visão Geral**.

   ![No menu de conta de integração, selecione "Visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel de visão geral, **selecione Delete**.

   ![No painel "Visão geral", selecione "Delete"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Para confirmar que pretende eliminar a sua conta de integração, selecione **Sim**.

   ![Para confirmar a eliminação, selecione "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie parceiros comerciais na sua conta de integração](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Crie acordos entre parceiros na sua conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)
