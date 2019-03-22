---
title: Autenticar com identidades geridas - Azure Logic Apps | Documentos da Microsoft
description: Para se autenticar sem iniciar sessão, pode criar uma identidade gerida (anteriormente denominadas identidade do serviço gerido ou MSI), para a sua aplicação lógica pode aceder a recursos em outro Azure Active Directory (Azure AD) inquilinos sem credenciais ou segredos
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 046aed64d3551d5c0b6ddae44b925452c01c297a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337600"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Autenticar e aceder a recursos com identidades geridas no Azure Logic Apps

Para aceder a recursos em outros inquilinos do Azure Active Directory (Azure AD) e autenticar a sua identidade sem iniciar sessão, a sua aplicação lógica pode utilizar um [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecido como identidade do serviço gerido ou MSI), em vez as credenciais ou segredos. O Azure gere esta identidade para si e ajuda a proteger as suas credenciais, porque não precisa fornecer ou girar segredos. Este artigo mostra como pode configurar e utilizar uma identidade gerida atribuído de sistema para a aplicação lógica. Para obter mais informações sobre identidades geridas, consulte [o que há de identidades geridas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Pode atualmente ter até 10 logic app fluxos de trabalho com atribuído o sistema gerido identidades em cada subscrição do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure, ou se não tiver uma subscrição <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>.

* A aplicação lógica em que pretende utilizar o sistema-atribuídas a identidade gerida. Se não tiver uma aplicação lógica, consulte [criar seu primeiro fluxo de trabalho de aplicação de lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Ativar a identidade gerida

Para identidades geridas atribuído de sistema, não é necessário que criar manualmente essa identidade. Para configurar uma identidade gerida atribuído de sistema para a aplicação lógica, pode usar essas formas: 

* [Portal do Azure](#azure-portal) 
* [Modelos Azure Resource Manager](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Portal do Azure

Para ativar uma identidade gerida atribuído de sistema para a sua aplicação lógica no portal do Azure, ative o **sistema atribuído** definição nas definições de identidade da sua aplicação lógica.

1. Na [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Estruturador da aplicação lógica.

1. No menu da aplicação lógica, sob **configurações**, selecione **identidade**. 

1. Sob **sistema atribuído** > **estado**, escolha **no**. Em seguida, escolha **salvar** > **Sim**.

   ![Ativar a definição de identidade gerida](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   A aplicação lógica agora tem uma identidade gerida atribuído de sistema registrada no Azure Active Directory:

   ![GUIDs para o ID de objeto](./media/create-managed-service-identity/object-id.png)

   | Propriedade | Value | Descrição | 
   |----------|-------|-------------| 
   | **ID de objeto** | <*identity-resource-ID*> | Um identificador exclusivo global (GUID) que representa o atribuído de sistema gerido identidade para a sua aplicação lógica no inquilino do Azure AD | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Quando deseja automatizar a criação e a implementar recursos do Azure como o logic apps, pode utilizar [modelos Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). Para criar uma identidade gerida atribuído de sistema para a sua aplicação lógica com o modelo, adicione a `"identity"` elemento e `"type"` propriedade para a definição de fluxo de trabalho de aplicação lógica no seu modelo de implementação: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Por exemplo:

```json
{
   "apiVersion": "2016-06-01", 
   "type": "Microsoft.logic/workflows", 
   "name": "[variables('logicappName')]", 
   "location": "[resourceGroup().location]", 
   "identity": { 
      "type": "SystemAssigned" 
   }, 
   "properties": { 
      "definition": { 
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#", 
         "actions": {}, 
         "parameters": {}, 
         "triggers": {}, 
         "contentVersion": "1.0.0.0", 
         "outputs": {} 
   }, 
   "parameters": {}, 
   "dependsOn": [] 
}
```

Quando o Azure cria a aplicação lógica, a definição de fluxo de trabalho essa aplicação lógica inclui estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade | Value | Descrição | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Um identificador exclusivo global (GUID) que representa a aplicação lógica no inquilino do Azure AD e, às vezes, é apresentada como uma "ID de objeto" ou `objectID` | 
| **tenantId** | <*Azure-AD-tenant-ID*> | Um identificador exclusivo global (GUID) que representa o inquilino do Azure AD em que a aplicação lógica agora é um membro. Dentro de inquilino do Azure AD, o principal de serviço tem o mesmo nome que a instância da aplicação lógica. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Aceder a recursos com a identidade gerida

Depois de criar uma identidade gerida atribuído de sistema para a aplicação lógica, poderá [conceder esse acesso de identidades para outros recursos do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Em seguida, pode usar essa identidade para a autenticação, como qualquer outro [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> A identidade gerida atribuído de sistema e o recurso onde pretende atribuir acesso tem de ter a mesma subscrição do Azure.

### <a name="assign-access-to-managed-identity"></a>Atribuir acesso a identidade gerida

Para dar acesso a outro recurso do Azure para a identidade gerida de atribuído de sistema da sua aplicação lógica, siga estes passos:

1. No portal do Azure, vá para o recurso do Azure onde pretende atribuir acesso para a sua identidade gerida. 

1. No menu do recurso, selecione **controlo de acesso (IAM)** e escolha **adicionar atribuição de função**. 

   ![Adicionar atribuição de função](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Sob **adicionar atribuição de função**, selecione a **função** pretende para a identidade. 

1. Na **atribuir acesso aos** propriedade, selecione **utilizador, grupo ou principal de serviço do Azure AD**, se ainda não estiver selecionado.

1. Na **selecione** caixa, começando com o primeiro caráter no nome da sua aplicação lógica, introduza o nome da sua aplicação lógica. Quando for apresentada a sua aplicação lógica, selecione a aplicação lógica.

   ![Selecione a aplicação lógica com a identidade gerida](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Quando tiver terminado, escolha **Save** (Guardar).

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Autenticar com uma identidade gerida na aplicação lógica

Depois de configurar a aplicação lógica com um atribuído de sistema de identidade gerido e atribuído o acesso ao recurso que pretende para essa identidade, agora, pode utilizar essa identidade para a autenticação. Por exemplo, pode utilizar uma ação de HTTP para que a sua aplicação lógica pode enviar uma solicitação HTTP ou ligue a esse recurso. 

1. Na sua aplicação lógica, adicione a **HTTP** ação. 

1. Forneça os detalhes necessários para esta ação, por exemplo, a solicitação **método** e **URI** localização do recurso que pretende chamar.

   Por exemplo, suponha que estiver usando a autenticação do Azure Active Directory (Azure AD) com [um destes serviços do Azure que suportam o Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   Na **URI** , introduza o URL de ponto final para esse serviço do Azure. 
   Então, se estiver a utilizar o Azure Resource Manager, introduza este valor na **URI** propriedade:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version-2016-06-01`

1. Na ação de HTTP, escolha **Mostrar opções avançadas**. 

1. Partir do **autenticação** lista, selecione **identidade gerido**. Depois de selecionar esta autenticação, o **público-alvo** propriedade é apresentada com o valor de ID de recurso predefinido:

   ![Selecione "Identidade gerida"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > Na **público-alvo** propriedade, o valor de ID de recurso deve corresponder exatamente o que espera que o Azure AD, incluindo quaisquer necessários à direita de barras. 
   > Pode encontrar estes valores de ID de recurso desta [que suportam o Azure AD dos serviços de tabela que descreve Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Por exemplo, se estiver a utilizar o ID de recurso do Gestor de recursos do Azure, certifique-se de que o URI tem uma barra à direita.

1. Continue a criar a aplicação lógica à sua maneira.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Remover a identidade gerida

Para desativar uma identidade gerida atribuído de sistema na sua aplicação lógica, pode seguir os passos semelhantes para a forma como configura a identidade através do portal do Azure, modelos de implementação Azure Resource Manager ou do Azure PowerShell. 

Ao eliminar a aplicação lógica, o Azure remove automaticamente a identidade de sistema atribuído da sua aplicação lógica do Azure AD.

### <a name="azure-portal"></a>Portal do Azure

Para remover uma identidade gerida atribuído de sistema para a sua aplicação lógica no portal do Azure, desative a **sistema atribuído** definição nas definições de identidade da sua aplicação lógica.

1. Na [portal do Azure](https://portal.azure.com), abra a aplicação lógica no Estruturador da aplicação lógica.

1. No menu da aplicação lógica, sob **configurações**, selecione **identidade**. 

1. Sob **sistema atribuído** > **estado**, escolha **desativar**. Em seguida, escolha **salvar** > **Sim**.

   ![Desativar a definição de identidade gerida](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modelo de implementação

Se tiver criado a identidade gerida de sistema atribuído a aplicação lógica com um modelo de implementação Azure Resource Manager, defina o `"identity"` do elemento `"type"` propriedade `"None"`. Esta ação também elimina o ID de principal do Azure AD. 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).