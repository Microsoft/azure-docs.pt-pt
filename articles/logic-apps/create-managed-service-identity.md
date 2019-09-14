---
title: Autenticar com identidades gerenciadas-aplicativos lógicos do Azure
description: Para autenticar sem entrar, você pode criar uma identidade gerenciada (anteriormente chamada de Identidade de Serviço Gerenciada ou MSI) para que seu aplicativo lógico possa acessar recursos em outros locatários do Azure Active Directory (AD do Azure) sem credenciais ou segredos
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: d6cf19a07829afea924d3d799b1309cfc5f6329f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959959"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Autenticar e acessar recursos com identidades gerenciadas em aplicativos lógicos do Azure

Para acessar recursos em outros locatários do Azure Active Directory (AD do Azure) e autenticar sua identidade sem entrar, seu aplicativo lógico pode usar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente conhecida como identidade de serviço gerenciada ou msi), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não precisa fornecer ou girar segredos. Este artigo mostra como você pode configurar e usar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico. Para obter mais informações sobre identidades gerenciadas, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Seu aplicativo lógico pode usar identidades gerenciadas somente com conectores que dão suporte a identidades gerenciadas. Atualmente, somente o conector HTTP dá suporte a identidades gerenciadas.
>
> No momento, você pode ter até 100 fluxos de trabalho de aplicativo lógico com identidades gerenciadas atribuídas pelo sistema em cada assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure, ou se você não tiver uma assinatura, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja usar a identidade gerenciada atribuída pelo sistema. Se você não tiver um aplicativo lógico, consulte [criar seu primeiro fluxo de trabalho do aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Habilitar identidade gerenciada

Para identidades gerenciadas atribuídas pelo sistema, você não precisa criar essa identidade manualmente. Para configurar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico, você pode usar estas maneiras: 

* [Azure portal](#azure-portal) 
* [Modelos de Azure Resource Manager](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Portal do Azure

Para habilitar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico por meio do portal do Azure, ative a configuração **atribuída pelo sistema** nas configurações de identidade do aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **identidade**.

1. Em**status** **atribuído** > pelo sistema, selecione **ativado**. Em seguida, selecione **salvar** > **Sim**.

   ![Ativar configuração de identidade gerenciada](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   Seu aplicativo lógico agora tem uma identidade gerenciada atribuída pelo sistema registrada em Azure Active Directory:

   ![GUIDs da ID de objeto](./media/create-managed-service-identity/object-id.png)

   | Propriedade | Value | Descrição |
   |----------|-------|-------------|
   | **ID do objeto** | <*identity-resource-ID*> | Um GUID (identificador global exclusivo) que representa a identidade gerenciada atribuída pelo sistema para seu aplicativo lógico em um locatário do Azure AD |
   ||||

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Quando desejar automatizar a criação e implantação de recursos do Azure, como aplicativos lógicos, você pode usar [modelos de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para criar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico por meio de um modelo `"identity"` , adicione `"type"` o elemento e a propriedade à definição de fluxo de trabalho do aplicativo lógico em seu modelo de implantação: 

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

Quando o Azure cria seu aplicativo lógico, a definição de fluxo de trabalho do aplicativo lógico inclui estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade | Value | Descrição |
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Um GUID (identificador global exclusivo) que representa o aplicativo lógico no locatário do Azure AD e, às vezes, aparece como uma "ID de objeto" ou`objectID` |
| **tenantId** | <*Azure-AD-tenant-ID*> | Um GUID (identificador global exclusivo) que representa o locatário do Azure AD em que o aplicativo lógico agora é um membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. |
||||

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Acessar recursos com identidade gerenciada

Depois de criar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico, você pode [conceder a essa identidade acesso a outros recursos do Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Em seguida, você pode usar essa identidade para autenticação, assim como qualquer outra [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> Tanto a identidade gerenciada atribuída pelo sistema quanto o recurso ao qual você deseja atribuir acesso devem ter a mesma assinatura do Azure.

### <a name="assign-access-to-managed-identity"></a>Atribuir acesso à identidade gerenciada

Para conceder acesso a outro recurso do Azure para a identidade gerenciada atribuída pelo sistema de seu aplicativo lógico, siga estas etapas:

1. Na portal do Azure, vá para o recurso do Azure no qual você deseja atribuir acesso à sua identidade gerenciada.

1. No menu do recurso, selecione **controle de acesso (iam)** . Na barra de ferramentas, escolha **Adicionar** > **Adicionar atribuição de função**.

   ![Adicionar atribuição de função](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. Em **Adicionar atribuição de função**, selecione a **função** desejada para a identidade.

1. Na propriedade **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**, se ainda não tiver selecionado.

1. Na caixa **selecionar** , começando com o primeiro caractere no nome do aplicativo lógico, insira o nome do aplicativo lógico. Quando o aplicativo lógico for exibido, selecione o aplicativo lógico.

   ![Selecionar aplicativo lógico com identidade gerenciada](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Quando tiver terminado, escolha **Save** (Guardar).

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Autenticar com identidade gerenciada no aplicativo lógico

Depois de configurar seu aplicativo lógico com uma identidade gerenciada atribuída pelo sistema e o acesso atribuído ao recurso desejado para essa identidade, agora você poderá usar essa identidade para autenticação. Por exemplo, você pode usar uma ação HTTP para que seu aplicativo lógico possa enviar uma solicitação HTTP ou chamar para esse recurso. 

1. Em seu aplicativo lógico, adicione a ação **http** .

1. Forneça os detalhes necessários para essa ação, como o **método** de solicitação e o local do **URI** para o recurso que você deseja chamar.

   Por exemplo, suponha que você esteja usando a autenticação do Azure Active Directory (Azure AD) com [um desses serviços do Azure que dão suporte ao Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Na caixa **URI** , insira a URL do ponto de extremidade para o serviço do Azure. Portanto, se você estiver usando Azure Resource Manager, insira esse valor na propriedade **URI** :

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. Na ação HTTP, escolha **Mostrar opções avançadas**.

1. Na lista **autenticação** , selecione **identidade gerenciada**. Depois de selecionar essa autenticação, a propriedade **Audience** aparece com o valor de ID de recurso padrão:

   ![Selecione "identidade gerenciada"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > Na propriedade **Audience** , o valor da ID de recurso deve corresponder exatamente ao que o Azure ad espera, incluindo as barras à direita necessárias. 
   > Você pode encontrar esses valores de ID de recurso nesta [tabela que descrevem os serviços do Azure que dão suporte ao Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Por exemplo, se você estiver usando a ID de recurso Azure Resource Manager, verifique se o URI tem uma barra à direita.

1. Continue criando o aplicativo lógico da maneira desejada.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Remover identidade gerenciada

Para desabilitar uma identidade gerenciada atribuída pelo sistema em seu aplicativo lógico, você pode seguir as etapas semelhantes a como você configura a identidade por meio do portal do Azure, Azure Resource Manager modelos de implantação ou Azure PowerShell.

Quando você exclui seu aplicativo lógico, o Azure remove automaticamente a identidade atribuída pelo sistema de seu aplicativo lógico do Azure AD.

### <a name="azure-portal"></a>Portal do Azure

Para remover uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico por meio do portal do Azure, desative a configuração **atribuída pelo sistema** nas configurações de identidade do aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **identidade**.

1. Em**status** **atribuído** > pelo sistema, escolha **desativado**. Em seguida, escolha **salvar** > **Sim**.

   ![Desativar configuração de identidade gerenciada](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Modelo de implementação

Se você criou a identidade gerenciada atribuída pelo sistema do aplicativo lógico com um modelo de implantação Azure Resource Manager, `"identity"` defina a `"type"` Propriedade do `"None"`elemento como. Essa ação também exclui a ID da entidade de segurança do Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passos Seguintes

* [Proteger o acesso e os dados no aplicativo lógico do Azure](../logic-apps/logic-apps-securing-a-logic-app.md)
