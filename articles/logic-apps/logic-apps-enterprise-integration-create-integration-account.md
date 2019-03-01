---
title: Criar e gerir as contas de integração B2B soluções - Azure Logic Apps | Documentos da Microsoft
description: Criar, ligar, mover e excluir contas de integração para integração empresarial e soluções B2B com o Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/30/2018
ms.author: estfan
ms.openlocfilehash: 43ecdafac4f0a5cdc9e619537cdbe2a42ff7fe1b
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191923"
---
# <a name="create-and-manage-integration-accounts-for-b2b-solutions-with-logic-apps"></a>Criar e gerir contas de integração para soluções de B2B no logic apps

Antes de poder compilar [integração empresarial e soluções B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) com [Azure Logic Apps](../logic-apps/logic-apps-overview.md), primeiro tem de ter uma conta de integração, o que é onde cria, armazenar e gerir artefactos de B2B, por exemplo, negociação de parceiros, contratos, mapas, esquemas, certificados e assim por diante. Antes da aplicação lógica pode trabalhar com os artefactos na sua conta de integração e utilizar os conectores de aplicações lógicas B2B, como validação de XML, deve [ligar a sua conta de integração](#link-account) à sua aplicação lógica. Para ligá-las, ambas as sua conta e a lógica de aplicação de integração tem de ter o *mesmo* localização do Azure, ou a região.

Este artigo mostra-lhe como realizar estas tarefas:

* Crie a sua conta de integração.
* Ligar a sua conta de integração para uma aplicação lógica.
* Mover a sua conta de integração para o Azure de outro grupo de recursos ou subscrição.
* Elimine a sua conta de integração.

Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure.

## <a name="create-integration-account"></a>criar conta de integração

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "contas de integração" como o filtro e selecione **contas de integração**.

   ![Localizar contas de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Sob **contas de integração**, escolha **Add**.

   ![Escolha "Adicionar" para criar a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

3. Fornece informações sobre a sua conta de integração: 

   ![Forneça detalhes para a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Necessário | Valor de exemplo | Descrição | 
   |----------|----------|---------------|-------------|
   | Name | Sim | conta de integração de teste | O nome para a sua conta de integração. Neste exemplo, utilize o nome especificado. | 
   | Subscrição | Sim | <*Azure-subscription-name*> | O nome para a subscrição do Azure a utilizar | 
   | Grupo de recursos | Sim | teste-integration-conta-rg | O nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) utilizado para organizar os recursos relacionados. Neste exemplo, crie um novo grupo de recursos com o nome especificado. | 
   | Escalão de Preço | Sim | Gratuito | O escalão de preço que pretende utilizar. Para este exemplo, selecione **gratuito**, mas para obter mais informações, consulte [Logic Apps limites e configuração](../logic-apps/logic-apps-limits-and-config.md) e [preços do Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). | 
   | Localização | Sim | EUA Oeste | A região onde pretende armazenar as suas informações de conta de integração. Selecione a mesma localização que a sua aplicação lógica ou criar uma aplicação lógica na mesma localização como a sua conta de integração. | 
   | Área de trabalho do Log Analytics | Não | Desativado | Mantenha a definição **Desativado** para o registo de diagnósticos. | 
   ||||| 

4. Quando estiver pronto, selecione **Afixar ao dashboard** e escolha **Criar**.

   Quando o Azure implementar a sua conta de integração para a localização selecionada, o que normalmente é concluído dentro de um minuto, o Azure abre a sua conta de integração.

   ![Azure abre-se a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

Agora, antes da aplicação lógica pode utilizar a sua conta de integração, tem de associar a conta de integração à sua aplicação lógica.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Ligar a aplicação lógica

Para conceder o acesso de aplicações lógicas para uma conta de integração que contém os artefactos de B2B, como o comércio de parceiros, contratos, mapas e esquemas, tem de ligar a sua conta de integração à sua aplicação lógica. 

> [!NOTE]
> A aplicação de lógica e a conta de integração tem de existir na mesma região.

1. No portal do Azure, localize e abra a aplicação lógica.

2. No menu da sua aplicação lógica, sob **configurações**, selecione **definições de fluxo de trabalho**. Na **Selecione uma conta de integração** , selecione a conta de integração para ligar a sua aplicação lógica.

   ![Selecione a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-2.png)

3. Para concluir a ligação, escolha **guardar**.

   ![Selecione a sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-3.png)

   Quando a sua conta de integração foi associada com êxito, o Azure mostra uma mensagem de confirmação. 

   ![Azure confirma a ligação com êxito](./media/logic-apps-enterprise-integration-create-integration-account/linkaccount-5.png)

Agora a sua aplicação lógica pode utilizar quaisquer e todos os artefactos na sua conta de integração mais os conectores de B2B, como validação de XML e um ficheiro simples de codificação ou descodificação.  

## <a name="unlink-from-logic-app"></a>Desassociar de aplicação lógica

Para ligar a sua aplicação lógica para outra conta de integração ou deixar de utilizar uma conta de integração com a sua aplicação lógica, pode eliminar a ligação através do Explorador de recursos do Azure.

1. No seu browser, aceda a <a href="https://resources.azure.com" target="_blank">Explorador de recursos do Azure (https://resources.azure.com)</a>. Certifique-se de que iniciou sessão com as mesmas credenciais do Azure.

   ![Explorador de Recursos do Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

2. Na caixa de pesquisa, introduza o nome da sua aplicação lógica, em seguida, localizar e selecionar a sua aplicação lógica.

   ![Localize e selecione a aplicação lógica](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

3. Na barra de título do explorer, escolha **leitura/escrita**.

   ![Ativar o modo de "Leitura/escrita"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-read-write-mode.png)

4. Sobre o **dados** separador, escolha **editar**.

   ![No separador de "Dados", selecione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-choose-edit.png)

5. No editor, encontre o `integrationAccount` propriedade para a integração de conta e eliminar essa propriedade, que tem este formato:

   ```json
   "integrationAccount": {
      "name": "<integration-account-name>",
      "id": "<integration-account-resource-ID>",
      "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por exemplo:

   ![Encontrar a definição de propriedade "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

6. Sobre o **dados** separador, escolha **colocar** para guardar as alterações. 

   ![Escolha "Colocar" para guardar as alterações](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

7. No portal do Azure, na sua aplicação lógica **definições de fluxo de trabalho**, verifique se o **conta de integração** propriedade agora parece vazia.

   ![Verifique que a conta de integração não está ligada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover conta de integração

Pode mover a sua conta de integração para outro grupo de subscrição ou ao recurso do Azure.

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "contas de integração" como o filtro e selecione **contas de integração**.

   ![Localize a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Sob **contas de integração**, selecione a conta de integração que pretende mover. No sua integração de conta de menu, em **configurações**, escolha **propriedades**.

   ![Em "Definições", escolha "Propriedades"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-properties.png)

3. Altere o grupo de recursos do Azure ou de uma subscrição para a sua conta de integração.

   ![Escolha "Alterar o grupo de recursos" ou "Subscrição de alteração"](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

4. Quando tiver terminado, certifique-se de que Atualize todos os scripts com os novos IDs de recurso para seus artefactos.  

## <a name="delete-integration-account"></a>Eliminar conta de integração

1. No menu principal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, introduza "contas de integração" como o filtro e selecione **contas de integração**.

   ![Localize a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

2. Sob **contas de integração**, selecione a conta de integração que pretende eliminar. No menu de conta de integração, escolha **descrição geral**, em seguida, escolha **eliminar**. 

   ![Selecione a conta de integração. Na página "Descrição geral", escolha "Delete"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

3. Para confirmar que pretende eliminar a sua conta de integração, escolha **Sim**.

   ![Para confirmar a eliminação, escolha "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Passos Seguintes

* [Crie parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Criar contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md)
