---
title: Autenticar com identidades geridas
description: Aceder a recursos em outros inquilinos do Azure Ative Directory sem se inscrever com credenciais ou segredos usando uma identidade gerida
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77117202"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticar acesso aos recursos do Azure utilizando identidades geridas em Aplicações Lógicas Azure

Para aceder a recursos em outros inquilinos do Azure Ative Directory (Azure AD) e autenticar a sua identidade sem iniciar sessão, a sua aplicação lógica pode usar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente Identidade de Serviço Gerida ou MSI), em vez de credenciais ou segredos. O Azure gere esta identidade para si e ajuda a garantir as suas credenciais porque não tem de fornecer ou rodar segredos.

As Aplicações Lógicas Azure suportam identidades geridas [*atribuídas*](../active-directory/managed-identities-azure-resources/overview.md) ao sistema e atribuídas ao [*utilizador.*](../active-directory/managed-identities-azure-resources/overview.md) A sua aplicação lógica pode utilizar a identidade atribuída ao sistema ou uma *única* identidade atribuída ao utilizador, que pode partilhar através de um grupo de aplicações lógicas, mas não ambas. Atualmente, apenas [gatilhos e ações específicos](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) suportam identidades geridas, não geridos conectores ou conexões, por exemplo:

* HTTP
* Funções do Azure
* API Management do Azure
* Serviços de Aplicações do Azure

Este artigo mostra como configurar ambos os tipos de identidades geridas para a sua aplicação lógica. Para obter mais informações, veja estes tópicos:

* [Gatilhos e ações que suportam identidades geridas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipos de autenticação suportado em chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limites de identidade geridos para aplicações lógicas](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Serviços Azure que suportam autenticação DaAzure com identidades geridas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). Tanto a identidade gerida como o recurso-alvo Azure onde precisa de acesso devem utilizar a mesma subscrição Azure.

* Para dar acesso de identidade gerido a um recurso Azure, é necessário adicionar um papel ao recurso-alvo dessa identidade. Para adicionar funções, precisa de permissões de administrador da [Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) que possam atribuir funções a identidades no correspondente inquilino da AD Azure.

* O recurso azure alvo a que quer aceder. Neste recurso, você irá adicionar um papel para a identidade gerida, que ajuda a aplicação lógica autenticando o acesso ao recurso alvo.

* A aplicação lógica onde pretende usar o [gatilho ou ações que suportam identidades geridas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Ativar a identidade gerida

Para configurar a identidade gerida que pretende utilizar, siga o link para essa identidade:

* [Identidade atribuída ao sistema](#system-assigned)
* [Identidade atribuída ao utilizador](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Ativar a identidade atribuída ao sistema

Ao contrário das identidades atribuídas ao utilizador, não é preciso criar manualmente a identidade atribuída ao sistema. Para configurar a identidade atribuída ao sistema para a sua aplicação lógica, aqui estão as opções que pode utilizar:

* [Portal do Azure](#azure-portal-system-logic-app)
* [Modelos do Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Ativar identidade atribuída ao sistema no portal Azure

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicações lógicas, em **Definições,** selecione **Identidade**. Selecione **sistema atribuído** > **em** > **Save**. Quando o Azure lhe pedir para confirmar, selecione **Sim**.

   ![Ativar a identidade atribuída ao sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se tiver um erro que só pode ter uma identidade gerida, a sua aplicação lógica já está associada à identidade atribuída ao utilizador. Antes de poder adicionar a identidade atribuída ao sistema, tem primeiro de *remover* a identidade atribuída ao utilizador da sua aplicação lógica.

   A sua aplicação lógica pode agora utilizar a identidade atribuída ao sistema, que está registada no Azure Ative Directory e está representada por um ID de objeto.

   ![ID do objeto para identidade atribuída ao sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **ID de objeto** | <*identidade-recurso-ID*> | Um identificador globalmente único (GUID) que representa a identidade atribuída pelo sistema para a sua aplicação lógica em um inquilino DaD Azure |
   ||||

1. Agora siga [os passos que dão acesso a essa identidade ao recurso](#access-other-resources) mais tarde neste tópico.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Ativar identidade atribuída ao sistema no modelo do Gestor de Recursos Azure

Para automatizar a criação e implementação de recursos Azure, como aplicações lógicas, pode utilizar modelos do Gestor de [Recursos Azure.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Para ativar a identidade gerida atribuída pelo sistema para a `identity` sua aplicação lógica no modelo, adicione o objeto e a `type` propriedade da criança à definição de recursos da aplicação lógica no modelo, por exemplo:

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

Quando o Azure cria a `identity` definição de recursos de aplicações lógicas, o objeto obtém estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | O Identificador Globalmente Único (GUID) do principal objeto de serviço para a identidade gerida que representa a sua aplicação lógica no inquilino DaD Azure. Este GUID às vezes aparece como `objectID`um "ID de objeto" ou . |
| `tenantId` | <*Azure-AD-inquilino-ID*> | O Identificador Globalmente Único (GUID) que representa o inquilino Azure AD onde a aplicação lógica é agora membro. Dentro do inquilino da AD Azure, o diretor de serviço tem o mesmo nome que a instância da aplicação lógica. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Ativar a identidade atribuída ao utilizador

Para configurar uma identidade gerida atribuída ao utilizador para a sua aplicação lógica, tem primeiro de criar essa identidade como recurso Azure autónomo separado. Aqui estão as opções que pode utilizar:

* [Portal do Azure](#azure-portal-user-identity)
* [Modelos do Azure Resource Manager](#template-user-identity)
* Azure PowerShell
  * [Criar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* CLI do Azure
  * [Criar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Adicionar atribuição de função](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* API REST do Azure
  * [Criar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Adicionar atribuição de função](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Criar identidade atribuída ao utilizador no portal Azure

1. No [portal Azure,](https://portal.azure.com)na caixa de pesquisa `managed identities`em qualquer página, introduza, e selecione **Identidades Geridas**.

   ![Localizar e selecionar "Identidades Geridas"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Sob **identidades geridas,** selecione **Adicionar**.

   ![Adicionar nova identidade gerida](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Forneça informações sobre a sua identidade gerida e, em seguida, selecione **Criar,** por exemplo:

   ![Criar identidade gerida atribuída ao utilizador](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome do recurso** | Sim | <*user-atribuído-identidade-nome*> | O nome para dar a identidade atribuída ao utilizador. Este exemplo utiliza "Fabrikam-user-user-designado-identidade". |
   | **Subscrição** | Sim | <*Nome de assinatura Azure*> | O nome da subscrição Azure para usar |
   | **Grupo de recursos** | Sim | <*Nome do grupo azure-recursos*> | O nome para o grupo de recursos usar. Crie um novo grupo ou selecione um grupo existente. Este exemplo cria um novo grupo chamado "fabrikam-managed-identities-RG". |
   | **Localização** | Sim | <*Região azul*> | A região de Azure onde armazenar informações sobre o seu recurso. Este exemplo usa "West US". |
   |||||

   Agora pode adicionar a identidade atribuída ao utilizador na sua aplicação lógica. Não pode adicionar mais do que uma identidade atribuída ao utilizador na sua aplicação lógica.

1. No portal Azure, encontre e abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicações lógicas, em **Definições,** selecione **Identidade**, e, em seguida, selecione **User designado** > **Add**.

   ![Adicionar identidade gerida atribuída ao utilizador](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. No painel de identidade gerido pelo **utilizador Add,** a partir da lista **de subscrição,** selecione a sua subscrição Azure se ainda não estiver selecionada. A partir da lista que mostra *todas as* identidades geridas nessa subscrição, encontre e selecione a identidade atribuída ao utilizador que deseja. Para filtrar a lista, na caixa de pesquisa de **identidades geridas atribuída** ao Utilizador, introduza o nome para a identidade ou grupo de recursos. Quando terminar, selecione **Adicionar**.

   ![Selecione a identidade atribuída ao utilizador para usar](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se tiver um erro que só pode ter uma única identidade gerida, a sua aplicação lógica já está associada à identidade atribuída ao sistema. Antes de poder adicionar a identidade atribuída ao utilizador, tem primeiro de desativar a identidade atribuída ao sistema na sua aplicação lógica.

   A sua aplicação lógica está agora associada à identidade gerida atribuída pelo utilizador.

   ![Associação com identidade atribuída ao utilizador](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Agora siga [os passos que dão acesso a essa identidade ao recurso](#access-other-resources) mais tarde neste tópico.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Criar identidade atribuída ao utilizador num modelo de Gestor de Recursos Azure

Para automatizar a criação e implementação de recursos Azure, como aplicações [lógicas,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar modelos do Gestor de Recursos Azure , que suportam [identidades atribuídas ao utilizador para autenticação.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) Na secção do `resources` seu modelo, a definição de recursos da sua aplicação lógica requer estes itens:

* Um `identity` objeto `type` com a propriedade definida para`UserAssigned`

* Um `userAssignedIdentities` objeto infantil que especifica o ID de recursos da identidade, que é outro objeto infantil que tem as `principalId` propriedades e propriedades `clientId`

Este exemplo mostra uma definição lógica de recursos de aplicações `identity` para um pedido HTTP PUT e inclui um objeto não parametrizado. A resposta ao pedido DE PUT e `identity` subsequente operação GET também tem este objeto:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Propriedade (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | O Identificador Globalmente Único (GUID) para a identidade gerida atribuída pelo utilizador no inquilino da AD Azure |
| `clientId` | <*cliente-ID*> | Um identificador globalmente único (GUID) para a nova identidade da sua aplicação lógica que é usada para chamadas durante o tempo de execução |
||||

Se o seu modelo também incluir a definição de recursos `identity` da identidade gerida, pode parametrizar o objeto. Este exemplo mostra `userAssignedIdentities` como o `userAssignedIdentity` objeto infantil refere uma variável que define na secção do `variables` seu modelo. Esta variável refere o ID de recurso para a sua identidade atribuída ao utilizador.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Propriedade (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-inquilino-ID*> | O Identificador Globalmente Único (GUID) que representa o inquilino Azure AD onde a identidade atribuída ao utilizador é agora membro. Dentro do inquilino da AD Azure, o diretor de serviço tem o mesmo nome que o nome de identidade atribuído ao utilizador. |
| `principalId` | <*principal-ID*> | O Identificador Globalmente Único (GUID) para a identidade gerida atribuída pelo utilizador no inquilino da AD Azure |
| `clientId` | <*cliente-ID*> | Um identificador globalmente único (GUID) para a nova identidade da sua aplicação lógica que é usada para chamadas durante o tempo de execução |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Dar acesso de identidade aos recursos

Antes de poder utilizar a identidade gerida da sua aplicação lógica para autenticação, instale acesso a essa identidade no recurso Azure onde pretende utilizar a identidade. Para completar esta tarefa, atribuir o papel adequado a essa identidade no recurso-alvo Azure. Aqui estão as opções que pode utilizar:

* [Portal do Azure](#azure-portal-assign-access)
* [Modelo de Gestor de Recursos Azure](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) - Para mais informações, consulte adicionar a atribuição de [funções utilizando o Azure RBAC e o Azure PowerShell.](../role-based-access-control/role-assignments-powershell.md)
* Azure CLI ([az role assignment create](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) - Para mais informações, consulte Adicionar a atribuição de [funções utilizando o Azure RBAC e o Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [API REST do Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Atribuir acesso no portal Azure

1. No [portal Azure,](https://portal.azure.com)dirija-se ao recurso Azure onde pretende que a sua identidade gerida tenha acesso.

1. A partir do menu do recurso, selecione**atribuições** de funções de controlo de **acesso (IAM)** > onde pode rever as atribuições de funções atuais para esse recurso. Na barra de ferramentas, selecione **Adicionar** > **designação de função**Add .

   ![Selecione "Adicionar" > "Adicionar atribuição de funções"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se a opção de atribuição de **funções Add** for desativada, é provável que não tenha permissões. Para obter mais informações sobre as permissões que lhe permitem gerir funções para recursos, consulte [permissões de funções do Administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. No âmbito da **atribuição de funções Add,** selecione uma **Função** que dê à sua identidade o acesso necessário ao recurso-alvo.

   Para o exemplo deste tópico, a sua identidade precisa de um papel que possa aceder à bolha num recipiente de [Armazenamento Azure.](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![Selecione a função "Storage Blob Data Contributor"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Siga estes passos para a sua identidade gerida:

   * **Identidade atribuída ao sistema**

     1. No **acesso atribuído à** caixa, selecione **Logic App**. Quando a propriedade **Subscrição** aparecer, selecione a subscrição Azure que está associada à sua identidade.

        ![Selecione acesso para identidade atribuída ao sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Na caixa **Select,** selecione a sua aplicação lógica da lista. Se a lista for demasiado longa, utilize a caixa **Select** para filtrar a lista.

        ![Selecione aplicativo lógico para identidade atribuída ao sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identidade atribuída ao utilizador**

     1. No **acesso** atribuído à caixa, selecione **A identidade gerida atribuída**pelo Utilizador . Quando a propriedade **Subscrição** aparecer, selecione a subscrição Azure que está associada à sua identidade.

        ![Selecione acesso para identidade atribuída ao utilizador](./media/create-managed-service-identity/assign-access-user.png)

     1. Na caixa **Select,** selecione a sua identidade a partir da lista. Se a lista for demasiado longa, utilize a caixa **Select** para filtrar a lista.

        ![Selecione a sua identidade atribuída ao utilizador](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Quando tiver terminado, selecione **Guardar**.

   A lista de atribuições de recursos-alvo mostra agora a identidade e o papel geridos selecionados. Este exemplo mostra como pode usar a identidade atribuída ao sistema para uma aplicação lógica e uma identidade atribuída ao utilizador para um grupo de outras aplicações lógicas.

   ![Identidades e funções geridas adicionadas ao recurso-alvo](./media/create-managed-service-identity/added-roles-for-identities.png)

   Para mais informações, [atribua um acesso de identidade gerido a um recurso utilizando o portal Azure](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Siga agora os [passos para autenticar o acesso com a identidade](#authenticate-access-with-identity) num gatilho ou ação que suporte identidades geridas.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticar acesso com identidade gerida

Depois de [ativar a identidade gerida para a sua aplicação lógica](#azure-portal-system-logic-app) e dar a essa identidade acesso ao recurso ou entidade [alvo,](#access-other-resources)pode utilizar essa identidade em [gatilhos e ações que suportem identidades geridas.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Se tiver uma função Azure onde pretenda utilizar a identidade atribuída ao sistema, primeiro ative a [autenticação para funções Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Estes passos mostram como usar a identidade gerida com um gatilho ou ação através do portal Azure. Para especificar a identidade gerida na definição json subjacente ou de ação, consulte a [autenticação de identidade gerida](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Se ainda não o fez, adicione o [gatilho ou a ação que suporta identidades geridas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Por exemplo, o gatilho ou ação HTTP pode usar a identidade atribuída ao sistema que permitiu para a sua aplicação lógica. Em geral, o gatilho ou ação HTTP utiliza estas propriedades para especificar o recurso ou entidade a que pretende aceder:

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Método** | Sim | O método HTTP que é usado pela operação que pretende executar |
   | **URI** | Sim | O URL de ponto final para aceder ao recurso ou entidade azure alvo. A sintaxe URI geralmente inclui o ID de [recursos](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para o recurso ou serviço Azure. |
   | **Cabeçalhos** | Não | Quaisquer valores cabeçalho que você precisa ou deseja incluir no pedido de saída, como o tipo de conteúdo |
   | **Consultas** | Não | Quaisquer parâmetros de consulta que necessite ou deseja incluir no pedido, como o parâmetro para uma operação específica ou a versão API para a operação que pretende executar |
   | **Autenticação** | Sim | O tipo de autenticação a utilizar para autenticar o acesso ao recurso ou entidade-alvo |
   ||||

   Como exemplo específico, suponha que pretenda executar a [operação Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) numa bolha na conta De armazenamento Azure, onde previamente configurao acesso à sua identidade. No entanto, o [conector de armazenamento De blob Azure](https://docs.microsoft.com/connectors/azureblob/) não oferece atualmente esta operação. Em vez disso, pode executar esta operação utilizando a [ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou outra [operação Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Para aceder às contas de armazenamento do Azure por detrás de firewalls utilizando pedidos HTTP e identidades geridas, certifique-se de que também configura a sua conta de armazenamento com a [exceção que permite o acesso por serviços fidedignos](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)da Microsoft .

   Para executar a [operação Snapshot Blob,](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)a ação HTTP especifica estas propriedades:

   | Propriedade | Necessário | Valor de exemplo | Descrição |
   |----------|----------|---------------|-------------|
   | **Método** | Sim | `PUT`| O método HTTP que a operação Snapshot Blob utiliza |
   | **URI** | Sim | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | O id de recurso para um ficheiro de armazenamento Azure Blob no ambiente Azure Global (público), que usa esta sintaxe |
   | **Cabeçalhos** | Sim, para o Armazenamento Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Os `x-ms-blob-type` `x-ms-version` valores e cabeçalhos que são necessários para as operações de Armazenamento Azure. <p><p>**Importante**: Nos pedidos de ação de saída http e `x-ms-version` de ação para o Armazenamento Azure, o cabeçalho requer a propriedade e a versão API para a operação que pretende executar. <p>Para obter mais informações, veja estes tópicos: <p><p>- [Cabeçalhos de pedido - Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Versão para serviços de armazenamento Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Consultas** | Sim, para esta operação. | `comp` = `snapshot` | O nome e valor do parâmetro de consulta para a operação Snapshot Blob. |
   |||||

   Aqui está o exemplo de ação HTTP que mostra todos estes valores de propriedade:

   ![Adicione uma ação HTTP para aceder a um recurso Azure](./media/create-managed-service-identity/http-action-example.png)

1. Adicione agora a propriedade **De autenticação** à ação HTTP. A partir da **lista de novos parâmetros,** selecione **Autenticação**.

   ![Adicione a propriedade "Autenticação" à ação HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem todos os gatilhos e ações suportam que você adicione um tipo de autenticação. Para mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. A partir da lista do **tipo de autenticação,** selecione **Identidade Gerida**.

   ![Para "Autenticação", selecione "Identidade Gerida"](./media/create-managed-service-identity/select-managed-identity.png)

1. A partir da lista de identidade gerida, selecione entre as opções disponíveis com base no seu cenário.

   * Se configurar a identidade atribuída ao sistema, selecione **Identidade Gerida Atribuída** ao Sistema se não for selecionada.

     ![Selecione "Identidade Gerida Atribuída ao Sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se configurar uma identidade atribuída ao utilizador, selecione essa identidade se ainda não estiver selecionada.

     ![Selecione a identidade atribuída ao utilizador](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Este exemplo continua com o **Sistema Identidade Gerida Atribuída**.

1. Em alguns gatilhos e ações, a propriedade **do Público** também aparece para você definir o ID de recurso alvo. Detete a propriedade **do Público** para o [ID de recurso para o recurso ou serviço alvo](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Caso contrário, por padrão, a `https://management.azure.com/` propriedade do **Público** utiliza o ID de recurso, que é o ID de recurso para o Gestor de Recursos Azure.

   > [!IMPORTANT]
   > Certifique-se de que o ID de recurso alvo *corresponde exatamente* ao valor que o Azure Ative Directory (AD) espera, incluindo quaisquer cortes de rasto necessários. Por exemplo, o ID de recursos para todas as contas de Armazenamento De Blob Azure requer um corte de rasto. No entanto, o ID de recursos para uma conta de armazenamento específica não requer um corte de rasto. Verifique os [iDs de recursos dos serviços Azure que suportam a Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Este exemplo define a `https://storage.azure.com/` propriedade do **Público** para que os tokens de acesso utilizados para a autenticação sejam válidos para todas as contas de armazenamento. No entanto, também pode especificar `https://fabrikamstorageaccount.blob.core.windows.net`o URL do serviço radicular, para uma conta de armazenamento específica.

   ![Definir a propriedade "Audience" para direcionar o ID do recurso](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para mais informações sobre a autorização de acesso com AD Azure para armazenamento azure, consulte estes tópicos:

   * [Autorizar acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](../storage/common/storage-auth-aad.md)
   * [Autorizar acesso ao Armazenamento Azure com Diretório Ativo Azure](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continue a construir a aplicação lógica da forma que quiser.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Desativar a identidade gerida

Para parar de usar uma identidade gerida para a sua aplicação lógica, você tem estas opções:

* [Portal do Azure](#azure-portal-disable)
* [Modelos do Azure Resource Manager](#template-disable)
* Azure PowerShell
  * [Remover a atribuição de funções](../role-based-access-control/role-assignments-powershell.md)
  * [Eliminar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* CLI do Azure
  * [Remover a atribuição de funções](../role-based-access-control/role-assignments-cli.md)
  * [Eliminar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST do Azure
  * [Remover a atribuição de funções](../role-based-access-control/role-assignments-rest.md)
  * [Eliminar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se eliminar a sua aplicação lógica, o Azure remove automaticamente a identidade gerida do Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Desativar identidade gerida no portal Azure

No portal Azure, primeiro remova o acesso da identidade ao [seu recurso-alvo](#disable-identity-target-resource). Em seguida, desligue a identidade atribuída ao sistema ou remova a identidade atribuída ao utilizador da [sua aplicação lógica](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Remover o acesso de identidade aos recursos

1. No [portal Azure,](https://portal.azure.com)dirija-se ao recurso Azure alvo onde pretende remover o acesso à identidade gerida.

1. A partir do menu do recurso-alvo, selecione controlo de **acesso (IAM)**. Sob a barra de ferramentas, selecione atribuições de **funções**.

1. Na lista de funções, selecione as identidades geridas que pretende remover. Na barra de ferramentas, **selecione Remover**.

   > [!TIP]
   > Se a opção **Remover** estiver desativada, é provável que não tenha permissões. Para obter mais informações sobre as permissões que lhe permitem gerir funções para recursos, consulte [permissões de funções do Administrador no Diretório Ativo do Azure](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A identidade gerida é agora removida e já não tem acesso ao recurso-alvo.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Desativar a identidade gerida na aplicação lógica

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicações lógicas, em **Definições,** selecione **Identidade,** e siga os passos para a sua identidade:

   * Selecione **sistema atribuído** > **em** > **Save**. Quando o Azure lhe pedir para confirmar, selecione **Sim**.

     ![Desativar a identidade atribuída ao sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecione **o Utilizador atribuído** e a identidade gerida e, em seguida, selecione **Remover**. Quando o Azure lhe pedir para confirmar, selecione **Sim**.

     ![Remover a identidade atribuída ao utilizador](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A identidade gerida está agora desativada na sua aplicação lógica.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Desativar a identidade gerida no modelo do Gestor de Recursos Azure

Se criou a identidade gerida da aplicação lógica utilizando um modelo `identity` de `type` Gestor de `None`Recursos Azure, detetete a propriedade do objeto para . Para a identidade gerida pelo sistema, esta ação também elimina o ID principal da Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passos seguintes

* [Acesso seguro e dados em Aplicações Lógicas Azure](../logic-apps/logic-apps-securing-a-logic-app.md)