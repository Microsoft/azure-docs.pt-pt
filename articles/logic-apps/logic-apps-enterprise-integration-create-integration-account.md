---
title: Criar ou gerir contas de integração B2B
description: Criar, ligar e gerir contas de integração para integração empresarial com Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: 191832ab227e854b40938183e335c1b6ea52199c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347850"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Criar e gerir contas de integração para integrações empresariais B2B no Azure Logic Apps.

Antes de criar [soluções B2B e de integração empresarial](../logic-apps/logic-apps-enterprise-integration-overview.md) com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md), precisa de criar uma conta de integração, que é um recurso separado do Azure que fornece um contentor seguro, dimensionável e gerível para os artefatos de integração que define e utiliza com os fluxos de trabalho da aplicação lógica.

Por exemplo, pode criar, armazenar e gerir artefactos B2B, tais como parceiros comerciais, acordos, mapas, esquemas, certificados e configurações de lote. Além disso, antes que a sua aplicação lógica possa trabalhar com estes artefactos e utilizar os conectores Logic Apps B2B, deve ligar a sua conta de [integração](#link-account) à sua aplicação lógica. Tanto a sua conta de integração como a aplicação lógica devem existir no *mesmo* local ou região.

> [!TIP]
> Para criar uma conta de integração dentro de um ambiente de serviço de [integração,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)consulte [Criar contas de integração num ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

Este tópico mostra-lhe como executar estas tarefas:

* Crie a sua conta de integração.
* Ligue a sua conta de integração a uma aplicação lógica.
* Altere o nível de preços para a sua conta de integração.
* Desvincula a sua conta de integração de uma aplicação lógica.
* Mude a sua conta de integração para outro grupo de recursos Azure ou subscrição.
* Elimine a sua conta de integração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Criar uma conta de integração

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para esta tarefa, pode utilizar o portal Azure seguindo os passos desta secção, [Azure PowerShell,](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)ou [Azure CLI](/cli/azure/resource#az-resource-create).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso**. Na caixa de pesquisa, insira a "conta de integração" como filtro e selecione **Conta de Integração.**

   ![Criar nova conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Na **Conta de Integração** , selecione **Criar**.

   ![Escolha "Adicionar" para criar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Forneça estas informações sobre a sua conta de integração:

   ![Fornecer detalhes da conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*integração-nome de conta*> | O nome da sua conta de integração, que pode conter apenas letras, números, hífenes `-` (), sublinhantes `_` (), parênteses `(` e `)` períodos `.` (). Este exemplo usa "Fabrikam-Integration". |
   | **Subscrição** | Sim | <*Nome de subscrição Azure*> | O nome para a subscrição do Azure |
   | **Grupo de recursos** | Sim | <*Nome de grupo Azure-recursos*> | O nome para o [grupo de recursos Azure](../azure-resource-manager/management/overview.md) usar para organizar recursos relacionados. Para este exemplo, crie um novo grupo de recursos com o nome "FabrikamIntegration-RG". |
   | **Escalão de Preço** | Sim | <*nível de preços*> | O nível de preços da conta de integração, que pode alterar mais tarde. Para este exemplo, selecione **Grátis**. Para obter mais informações, veja estes tópicos: <p>- [Modelo de preços de Apps Lógicas](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Limites e configuração de Apps lógicas](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Preços de Apps lógicas](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Localização** | Sim | <*Região de Azure*> | A região onde armazenar os metadados da sua conta de integração. Selecione o mesmo local que a sua aplicação lógica ou crie as suas aplicações lógicas no mesmo local que a sua conta de integração. Para este exemplo, use "West US". <p>**Nota:** Para criar uma conta de integração dentro de um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selecione o ISE como local. Para obter mais informações, consulte [Criar contas de integração num ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment) |
   | **Log Analytics** | No | Off, On | Mantenha a definição **Off** para este exemplo. |
   |||||

1. Quando terminar, **selecione Criar**.

   Após a implementação concluída, o Azure abre a sua conta de integração.

   ![Azure abre conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Antes que a sua aplicação lógica possa usar a sua conta de integração, siga os próximos passos para ligar a sua conta de integração e a aplicação lógica em conjunto.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode criar uma conta de integração utilizando os comandos Azure CLI nesta secção.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-an-integration-account"></a>Criar uma conta de Integração

Use estes comandos para criar uma conta de integração.

1. Para adicionar a extensão da [conta de integração lógica az,](/cli/azure/ext/logic/logic/integration-account) utilize o comando [de adicionar extensão az:](/cli/azure/extension#az_extension_add)

   ```azurecli
   az extension add –-name logic
   ```

1. Para criar um grupo de recursos ou utilizar um grupo de recursos existente, executar o [grupo az criar](/cli/azure/group#az_group_create) comando:

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Para listar as contas de integração de um grupo de recursos, utilize o comando [da lista de conta de integração lógica az:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_list)

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Para criar uma conta de integração, executar a [conta de integração lógica az criar](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_create) comando:

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   O nome da sua conta de integração só pode conter letras, números, hífens (-), sublinhados (_), parênteses ((,) e períodos (.).

   > [!TIP]
   > Para criar uma conta de integração dentro de um [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)selecione o ISE como a localização. Para obter mais informações, consulte [Criar contas de integração num ISE.](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

   Para visualizar uma conta de integração específica, utilize o comando [az logic integration-account show:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_show)

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Pode alterar o seu SKU, ou nível de preços, utilizando o comando [de atualização da conta de integração lógica az:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_update)

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Para obter mais informações sobre preços, consulte estes recursos:

   * [Modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md#integration-accounts)
   * [Limites e configuração de Apps lógicas](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)
   * [Preços de Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps/)

Para importar uma conta de integração utilizando um ficheiro JSON, utilize o comando [de importação de conta de integração lógica az:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_import)

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

Pode eliminar uma conta de integração utilizando o comando [de eliminação de conta de integração lógica az:](/cli/azure/ext/logic/logic/integration-account#ext_logic_az_logic_integration_account_delete)

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

Antes da sua aplicação lógica poder utilizar a sua conta de integração, ligue a sua conta de integração e a sua aplicação lógica. A secção seguinte descreve a ligação.

---
<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Associar a aplicação lógica

Para dar às suas aplicações lógicas acesso a uma conta de integração que contenha os seus artefactos B2B, tem primeiro de ligar a sua conta de integração à sua aplicação lógica. Tanto a aplicação lógica como a conta de integração devem existir na mesma região. Para completar esta tarefa, pode utilizar o portal Azure. Se utilizar o Visual Studio e a sua aplicação lógica estiver num [projeto do Azure Resource Group,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)pode [ligar a sua aplicação lógica a uma conta de integração utilizando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. No portal Azure, encontre e abra a sua aplicação lógica.

1. No [portal Azure,](https://portal.azure.com)abra uma app lógica existente ou crie uma nova aplicação lógica.

1. No menu da sua aplicação lógica, em **Definições,** selecione **as definições do Fluxo de Trabalho**. Na **conta de Integração,** abra a lista **de conta Select a Integration.** Selecione a conta de integração para ligar à sua aplicação lógica.

   ![Selecione a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Para terminar a ligação, **selecione Save**.

   ![Screenshot que mostra onde selecionar Salvar para escolher a sua conta de integração.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Depois de a sua conta de integração estar ligada com sucesso, o Azure mostra uma mensagem de confirmação.

   ![Azure confirma ligação bem sucedida](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Agora a sua aplicação lógica pode usar os artefactos na sua conta de integração mais os conectores B2B, tais como validação de XML e codificação ou descodificação de ficheiros planos.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Alterar escalão de preço

Para aumentar os [limites](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) para uma conta de integração, pode [fazer upgrade para um nível de preços mais elevado,](#upgrade-pricing-tier)se disponível. Por exemplo, pode fazer upgrade do nível Free para o nível Básico ou standard. Também pode [descer para um nível inferior,](#downgrade-pricing-tier)se disponível. Para obter mais informações sobre preços de informação, consulte estes tópicos:

* [Preços de Aplicações Lógicas](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modelo de preços de Aplicações Lógicas](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Nível de preços de upgrade

Para fazer esta alteração, pode utilizar o portal Azure ou o Azure CLI.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa principal do Azure, insira as "contas de integração" como filtro e selecione **contas de Integração**.

   ![Encontrar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração nas suas subscrições Azure.

1. Nas **contas de Integração,** selecione a conta de integração que pretende mover. No menu da sua conta de integração, **selecione Overview**.

   ![No menu de conta de integração, selecione "Overview"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel de visão geral, selecione **o nível de preços de upgrade,** que lista quaisquer níveis mais altos disponíveis. Quando seleciona um nível, a alteração entra imediatamente em vigor.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Se ainda não o fez, [instale os pré-requisitos do Azure CLI](/cli/azure/get-started-with-azure-cli).

1. No portal Azure, abra o ambiente [Azure Cloud Shell.](../cloud-shell/overview.md)

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na pronta do comando, insira o comando [ **de recursos az,**](/cli/azure/resource#az-resource-update)e desaprote `skuName` o nível mais alto que deseja.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Por exemplo, se tiver o nível básico, pode definir `skuName` `Standard` para:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Nível de preços de downgrade

Para fazer esta alteração, utilize o [Azure CLI](/cli/azure/get-started-with-azure-cli).

1. Se ainda não o fez, [instale os pré-requisitos do Azure CLI](/cli/azure/get-started-with-azure-cli).

1. No portal Azure, abra o ambiente [Azure Cloud Shell.](../cloud-shell/overview.md)

   ![Abrir o Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. Na pronta do comando, insira o comando [ **de recursos az**](/cli/azure/resource#az-resource-update) e `skuName` coloque-o no nível inferior que deseja.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Por exemplo, se tiver o nível Standard, pode definir `skuName` `Basic` para:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Desassociar de aplicação lógica

Se pretender ligar a sua aplicação lógica a outra conta de integração, ou deixar de utilizar uma conta de integração com a sua aplicação lógica, elimine o link utilizando o Azure Resource Explorer.

1. Abra a janela do seu navegador e vá ao [Azure Resource https://resources.azure.com) Explorer (.](https://resources.azure.com) Inscreva-se com as mesmas credenciais de conta Azure.

   ![Explorador de Recursos do Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Na caixa de pesquisa, insira o nome da sua aplicação lógica para que possa encontrar e selecionar a sua aplicação lógica.

   ![Localizar e selecionar aplicativo de lógica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na barra de título do explorador, selecione **Read/Write**.

   ![Ligue o modo "Ler/Escrever"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. No separador **Dados,** **selecione Editar.**

   ![No separador "Dados", selecione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. No editor, encontre o `integrationAccount` objeto e elimine essa propriedade, que tem este formato:

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

1. No separador **Dados,** selecione **Colocar** para guardar as suas alterações.

   ![Para guardar alterações, selecione "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. No portal Azure, encontre e selecione a sua aplicação lógica. Nas **definições** de Workflow da sua aplicação, verifique se a propriedade da **conta Integração** aparece agora vazia.

   ![Verifique se a conta de integração não está ligada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover conta de Integração

Pode mover a sua conta de integração para outro grupo de recursos Azure ou subscrição do Azure. Ao mover recursos, o Azure cria novos IDs de recursos, por isso certifique-se de que utiliza os novos IDs e atualiza quaisquer scripts ou ferramentas associados aos recursos movidos. Se quiser alterar a subscrição, também deve especificar um grupo de recursos existente ou novo.

Para esta tarefa, pode utilizar o portal Azure seguindo os passos desta secção ou o [CLI Azure](/cli/azure/resource#az-resource-move).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa principal do Azure, insira as "contas de integração" como filtro e selecione **contas de Integração**.

   ![Encontrar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração nas suas subscrições Azure.

1. Nas **contas de Integração,** selecione a conta de integração que pretende mover. No menu da sua conta de integração, **selecione Overview**.

   ![No menu de conta de integração, selecione "Overview"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Ao lado de um **grupo de recursos** ou nome de **assinatura,** selecione **alteração**.

   ![Alterar grupo de recursos ou subscrição](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecione quaisquer recursos relacionados que também queira mover.

1. Com base na sua seleção, siga estes passos para alterar o grupo de recursos ou subscrição:

   * Grupo de recursos: A partir da lista do **grupo de recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, **selecione Criar um novo grupo de recursos.**

   * Subscrição: A partir da lista **de assinaturas,** selecione a subscrição do destino. Na lista de **grupos de Recursos,** selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, **selecione Criar um novo grupo de recursos.**

1. Para reconhecer a sua compreensão de que quaisquer scripts ou ferramentas associados aos recursos movidos não funcionarão até que os atualize com os novos IDs de recursos, selecione a caixa de confirmação e, em seguida, selecione **OK**.

1. Depois de terminar, certifique-se de atualizar todos e quaisquer scripts com os novos IDs de recursos para os seus recursos movidos.  

## <a name="delete-integration-account"></a>Eliminar conta de Integração

Para esta tarefa, pode utilizar o portal Azure seguindo os passos desta secção, [Azure CLI](/cli/azure/resource#az-resource-delete)ou [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com as credenciais da sua conta do Azure.

1. Na caixa de pesquisa principal do Azure, insira as "contas de integração" como filtro e selecione **contas de Integração**.

   ![Encontrar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração nas suas subscrições Azure.

1. Nas **contas de Integração,** selecione a conta de integração que pretende eliminar. No menu da sua conta de integração, **selecione Overview**.

   ![No menu de conta de integração, selecione "Overview"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel de visão geral, **selecione Delete**.

   ![No painel "Visão Geral", selecione "Delete"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Para confirmar que pretende eliminar a sua conta de integração, selecione **Sim**.

   ![Para confirmar a eliminação, selecione "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Próximos passos

* [Crie parceiros comerciais na sua conta de integração](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Crie acordos entre parceiros na sua conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)
