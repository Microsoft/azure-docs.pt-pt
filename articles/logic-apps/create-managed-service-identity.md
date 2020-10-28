---
title: Autenticar com identidades geridas
description: Aceder a recursos protegidos pelo Azure Ative Directory sem iniciar sessão com credenciais ou segredos utilizando uma identidade gerida
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: bb146f03000f17d94d3d2ffc93b55c42eea20dac
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736405"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticar o acesso aos recursos do Azure utilizando identidades geridas em Azure Logic Apps

Para aceder facilmente a outros recursos protegidos pelo Azure Ative Directory (Azure AD) e autenticar a sua identidade sem iniciar sessão, a sua aplicação lógica pode utilizar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) (anteriormente Identidade de Serviço Gerida ou MSI), em vez de credenciais ou segredos. O Azure gere esta identidade automaticamente e ajuda a proteger as credenciais, uma vez que não precisa de introduzir segredos nem proceder a sua rotação.

A Azure Logic Apps suporta identidades geridas [*atribuídas pelo sistema*](../active-directory/managed-identities-azure-resources/overview.md) e [*atribuídas pelo utilizador.*](../active-directory/managed-identities-azure-resources/overview.md) A sua aplicação lógica pode usar a identidade atribuída ao sistema ou uma *única* identidade atribuída ao utilizador, que pode partilhar através de um grupo de aplicações lógicas, mas não ambas. Atualmente, [apenas gatilhos e ações específicas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) suportam identidades geridas, não conectores geridos ou ligações, por exemplo:

* HTTP
* Funções do Azure
* API Management do Azure
* Serviços de Aplicações do Azure

Este artigo mostra como configurar ambos os tipos de identidades geridas para a sua aplicação lógica. Para obter mais informações, veja estes tópicos:

* [Desencadeamentos e ações que suportam identidades geridas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Tipos de autenticação suportados em chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limites às identidades geridas para aplicações lógicas](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Serviços Azure que suportam autenticação AZURE AD com identidades geridas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição, [inscreva-se numa conta do Azure gratuita](https://azure.microsoft.com/free/). Tanto a identidade gerida como o recurso Azure alvo onde precisa de acesso devem utilizar a mesma subscrição do Azure.

* Para dar acesso de identidade gerido a um recurso Azure, você precisa adicionar um papel ao recurso-alvo para essa identidade. Para adicionar funções, precisa de permissões de [administrador AD Azure](../active-directory/roles/permissions-reference.md) que possam atribuir funções a identidades no inquilino AZure AD correspondente.

* O recurso Azure alvo a que pretende aceder. Neste recurso, irá adicionar um papel para a identidade gerida, o que ajuda a aplicação lógica a autenticar o acesso ao recurso-alvo.

* A aplicação lógica onde pretende usar o [gatilho ou ações que suportam identidades geridas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Ativar a identidade gerida

Para configurar a identidade gerida que pretende utilizar, siga o link para essa identidade:

* [Identidade atribuída ao sistema](#system-assigned)
* [Identidade atribuída ao utilizador](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Ativar a identidade atribuída ao sistema

Ao contrário das identidades atribuídas pelo utilizador, não é preciso criar manualmente a identidade atribuída ao sistema. Para configurar a identidade atribuída ao sistema para a sua aplicação lógica, aqui estão as opções que pode utilizar:

* [Portal do Azure](#azure-portal-system-logic-app)
* [Modelos de gestor de recursos Azure](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Ativar a identidade atribuída ao sistema no portal Azure

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicativos lógico, em **Definições,** selecione **Identidade** . Selecione **sistema atribuído**  >  **On**  >  **no Save** . Quando O Azure lhe pedir para confirmar, selecione **Sim** .

   ![Ativar a identidade atribuída ao sistema](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Se tiver um erro que possa ter apenas uma identidade gerida, a sua aplicação lógica já está associada à identidade atribuída ao utilizador. Antes de poder adicionar a identidade atribuída ao sistema, tem primeiro de *remover* a identidade atribuída ao utilizador da sua aplicação lógica.

   A sua aplicação lógica pode agora utilizar a identidade atribuída ao sistema, que está registada no Azure Ative Directory e é representada por um ID de objeto.

   ![ID do objeto para identidade atribuída ao sistema](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **ID de objeto** | <*identidade-recursos-ID*> | Um Identificador Globalmente Único (GUID) que representa a identidade atribuída ao sistema para a sua aplicação lógica num inquilino AD Azure |
   ||||

1. Agora siga os [passos que dão a essa identidade acesso ao recurso](#access-other-resources) mais tarde neste tópico.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Ativar a identidade atribuída ao sistema no modelo do Gestor de Recursos Azure

Para automatizar a criação e implementação de recursos Azure, como aplicações lógicas, pode utilizar [modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para ativar a identidade gerida atribuída pelo sistema para a sua aplicação lógica no modelo, adicione o `identity` objeto e a propriedade da criança à `type` definição de recursos da aplicação lógica no modelo, por exemplo:

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

Quando o Azure cria a definição de recursos de aplicação lógica, o `identity` objeto obtém estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*iD principal*> | O Identificador Globalmente Único (GUID) do objeto principal de serviço para a identidade gerida que representa a sua aplicação lógica no inquilino Azure AD. Este GUID às vezes aparece como um "ID de objeto" ou `objectID` . |
| `tenantId` | <*Azure-AD-inquilino-ID*> | O Identificador Globalmente Único (GUID) que representa o inquilino AZure AD onde a aplicação lógica é agora membro. Dentro do inquilino AZure AD, o diretor de serviço tem o mesmo nome que a instância da aplicação lógica. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Ativar a identidade atribuída ao utilizador

Para configurar uma identidade gerida atribuída pelo utilizador para a sua aplicação lógica, tem primeiro de criar essa identidade como um recurso autónomo separado da Azure. Aqui estão as opções que pode utilizar:

* [Portal do Azure](#azure-portal-user-identity)
* [Modelos de gestor de recursos Azure](#template-user-identity)
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

1. No [portal Azure,](https://portal.azure.com)na caixa de pesquisa em qualquer página, insira `managed identities` e selecione **Identidades Geridas** .

   ![Localizar e selecionar "Identidades Geridas"](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Em **Identidades Geridas** , selecione **Add** .

   ![Adicionar nova identidade gerida](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Forneça informações sobre a sua identidade gerida e, em seguida, selecione **Review + Create** , por exemplo:

   ![Criar identidade gerida atribuída ao utilizador](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Subscrição** | Sim | <*Nome de subscrição Azure*> | O nome para a subscrição Azure para usar |
   | **Grupo de recursos** | Sim | <*Nome de grupo Azure-recursos*> | O nome para o grupo de recursos a utilizar. Criar um novo grupo ou selecionar um grupo existente. Este exemplo cria um novo grupo chamado `fabrikam-managed-identities-RG` . |
   | **Região** | Sim | <*Região de Azure*> | A região de Azure onde armazenar informações sobre o seu recurso. Este exemplo usa "West US". |
   | **Name** | Sim | <*nome de identidade atribuído pelo utilizador*> | O nome para dar a sua identidade atribuída ao utilizador. Este exemplo `Fabrikam-user-assigned-identity` utiliza. |
   |||||

   Depois de validar estes detalhes, o Azure cria a sua identidade gerida. Agora pode adicionar a identidade atribuída ao utilizador à sua aplicação lógica. Não é possível adicionar mais do que uma identidade atribuída ao utilizador à sua aplicação lógica.

1. No portal Azure, encontre e abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicações lógicas, em **Definições** , selecione **Identidade** e, em seguida, selecione **Utilizador designado**  >  **Adicionar** .

   ![Adicionar identidade gerida atribuída ao utilizador](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. No painel de **identidade gerido do utilizador Add,** a partir da lista de **subscrição,** selecione a sua subscrição Azure se ainda não tiver sido selecionada. A partir da lista que mostra *todas as* identidades geridas nessa subscrição, encontre e selecione a identidade atribuída ao utilizador que pretende. Para filtrar a lista, na caixa de pesquisa de **identidades geridas atribuídas ao Utilizador, insira** o nome para o grupo de identidade ou recursos. Quando terminar, **selecione Add** .

   ![Selecione a identidade atribuída ao utilizador para usar](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Se tiver um erro que possa ter apenas uma identidade gerida, a sua aplicação lógica já está associada à identidade atribuída ao sistema. Antes de poder adicionar a identidade atribuída ao utilizador, tem primeiro de desativar a identidade atribuída ao sistema na sua aplicação lógica.

   A sua aplicação lógica está agora associada à identidade gerida atribuída pelo utilizador.

   ![Associação com identidade atribuída ao utilizador](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Agora siga os [passos que dão a essa identidade acesso ao recurso](#access-other-resources) mais tarde neste tópico.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Criar identidade atribuída ao utilizador num modelo de Gestor de Recursos Azure

Para automatizar a criação e implementação de recursos Azure, como aplicações lógicas, pode utilizar [modelos do Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), que [suportam identidades atribuídas pelo utilizador para a autenticação.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) Na secção do seu `resources` modelo, a definição de recursos da sua aplicação lógica requer estes itens:

* Um `identity` objeto com a propriedade definida `type` para `UserAssigned`

* Um objeto infantil `userAssignedIdentities` que especifica o recurso e nome atribuídos pelo utilizador

Este exemplo mostra uma definição lógica de recurso de aplicação para um pedido HTTP PUT e inclui um objeto não parametrizado. `identity` A resposta ao pedido PUT e posterior operação GET também tem este `identity` objeto:

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
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
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

Se o seu modelo também incluir a definição de recursos da identidade gerida, pode parametrizar o `identity` objeto. Este exemplo mostra como o objeto da criança `userAssignedIdentities` faz referência a uma `userAssignedIdentity` variável que define na secção do seu `variables` modelo. Esta variável faz referência ao ID de recurso para a sua identidade atribuída ao utilizador.

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
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Dar acesso identitário aos recursos

Antes de poder utilizar a identidade gerida da sua aplicação lógica para a autenticação, confiem o acesso a essa identidade no recurso Azure onde planeia utilizar a identidade. Para completar esta tarefa, atribua o papel adequado a essa identidade no recurso Target Azure. Aqui estão as opções que pode utilizar:

* [Portal do Azure](#azure-portal-assign-access)
* [Modelo Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment)](/powershell/module/az.resources/new-azroleassignment)- Para obter mais informações, consulte [adicionar a atribuição de funções utilizando a Azure RBAC e a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI[(az role assignment create)](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true#az-role-assignment-create)- Para obter mais informações, consulte [adicionar a atribuição de funções utilizando Azure RBAC e Azure CLI](../role-based-access-control/role-assignments-cli.md).
* [API REST do Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Atribuir acesso no portal Azure

1. No [portal Azure,](https://portal.azure.com)aceda ao recurso Azure onde pretende que a sua identidade gerida tenha acesso.

1. A partir do menu do recurso, selecione atribuições de funções de controlo de **acesso (IAM)**  >  **Role assignments** onde pode rever as atribuições de funções atuais para esse recurso. Na barra de **Add** ferramentas, selecione  >  **Adicionar a função** de função .

   ![Selecione "Adicionar" > "Adicionar atribuição de funções"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se a opção **de atribuição de funções Add** estiver desativada, é provável que não tenha permissões. Para obter mais informações sobre as permissões que lhe permitem gerir funções de recursos, consulte [permissões de função de Administrador no Diretório Ativo Azure](../active-directory/roles/permissions-reference.md).

1. No **âmbito da atribuição de funções Add** , selecione uma **Função** que dá à sua identidade o acesso necessário ao recurso-alvo.

   Para o exemplo deste tópico, a sua identidade necessita de uma [função que possa aceder à bolha num recipiente de Armazenamento Azure,](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)por isso selecione o papel **de Colaborador de Dados do Blob de Armazenamento** para a identidade gerida.

   ![Selecione a função "Storage Blob Data Contributor"](./media/create-managed-service-identity/select-role-for-identity.png)

1. Siga estes passos para a sua identidade gerida:

   * **Identidade atribuída ao sistema**

     1. No acesso à caixa **de atribuição,** selecione **Logic App** . Quando a propriedade **de Subscrição** aparecer, selecione a subscrição Azure que está associada à sua identidade.

        ![Selecione acesso para identidade atribuída ao sistema](./media/create-managed-service-identity/assign-access-system.png)

     1. Na caixa **Select,** selecione a sua aplicação lógica da lista. Se a lista for demasiado longa, utilize a caixa **Select** para filtrar a lista.

        ![Selecione app lógica para identidade atribuída ao sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Identidade atribuída ao utilizador**

     1. No acesso à caixa **do Atribua,** selecione **identidade gerida do Utilizador atribuído.** Quando a propriedade **de Subscrição** aparecer, selecione a subscrição Azure que está associada à sua identidade.

        ![Selecione acesso para identidade atribuída ao utilizador](./media/create-managed-service-identity/assign-access-user.png)

     1. Na caixa **Select,** selecione a sua identidade na lista. Se a lista for demasiado longa, utilize a caixa **Select** para filtrar a lista.

        ![Selecione a sua identidade atribuída ao utilizador](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Quando tiver terminado, selecione **Guardar** .

   A lista de atribuições de funções do recurso-alvo mostra agora a identidade e o papel geridos selecionados. Este exemplo mostra como pode usar a identidade atribuída ao sistema para uma aplicação lógica e uma identidade atribuída ao utilizador para um grupo de outras aplicações lógicas.

   ![Identidades e funções geridas adicionadas para direcionar o recurso](./media/create-managed-service-identity/added-roles-for-identities.png)

   Para mais informações, [atribua um acesso de identidade gerido a um recurso utilizando o portal Azure.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

1. Agora siga os [passos para autenticar o acesso com a identidade](#authenticate-access-with-identity) num gatilho ou ação que suporte identidades geridas.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticar acesso com identidade gerida

Depois de [ativar a identidade gerida para a sua aplicação lógica](#azure-portal-system-logic-app) e [dar essa identidade ao recurso ou entidade alvo,](#access-other-resources)pode utilizar essa identidade em [gatilhos e ações que suportem identidades geridas.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Se tiver uma função Azure onde pretende utilizar a identidade atribuída ao sistema, [primeiro ative a autenticação para funções Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Estes passos mostram como usar a identidade gerida com um gatilho ou ação através do portal Azure. Para especificar a identidade gerida na definição de JSON subjacente ao gatilho ou ação, consulte [a autenticação de identidade gerida](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. Se ainda não o fez, adicione o [gatilho ou a ação que suporta identidades geridas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Por exemplo, o gatilho ou ação HTTP pode utilizar a identidade atribuída ao sistema que ativou para a sua aplicação lógica. Em geral, o gatilho ou ação HTTP utiliza estas propriedades para especificar o recurso ou entidade a que pretende aceder:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Método** | Sim | O método HTTP que é usado pela operação que pretende executar |
   | **URI** | Sim | O URL de ponto final para aceder ao recurso ou entidade target Azure. A sintaxe URI geralmente inclui o [ID de recurso](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para o recurso ou serviço Azure. |
   | **Cabeçalhos** | Não | Quaisquer valores de cabeçalho que necessite ou queira incluir no pedido de saída, como o tipo de conteúdo |
   | **Consultas** | Não | Quaisquer parâmetros de consulta que necessite ou pretenda incluir no pedido, como o parâmetro para uma operação específica ou a versão API para a operação que pretende executar |
   | **Autenticação** | Sim | O tipo de autenticação a utilizar para autenticar o acesso ao recurso ou entidade-alvo |
   ||||

   Como exemplo específico, suponha que pretende executar a [operação Snapshot Blob](/rest/api/storageservices/snapshot-blob) numa bolha na conta de Armazenamento Azure onde previamente estabeleceu acesso para a sua identidade. No entanto, o [conector de armazenamento Azure Blob](/connectors/azureblob/) não oferece atualmente esta operação. Em vez disso, pode executar esta operação utilizando a ação [HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou outra [operação API do Blob Service REST](/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Para aceder às contas de armazenamento do Azure por trás de firewalls utilizando pedidos HTTP e identidades geridas, certifique-se de que também configura a sua conta de armazenamento com a [exceção que permite o acesso por serviços confiáveis da Microsoft.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

   Para executar a [operação Snapshot Blob](/rest/api/storageservices/snapshot-blob), a ação HTTP especifica estas propriedades:

   | Propriedade | Necessário | Valor de exemplo | Descrição |
   |----------|----------|---------------|-------------|
   | **Método** | Sim | `PUT`| O método HTTP que a operação Snapshot Blob utiliza |
   | **URI** | Sim | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | O ID de recurso para um ficheiro de armazenamento Azure Blob no ambiente Azure Global (público), que usa esta sintaxe |
   | **Cabeçalhos** | Para armazenamento Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r'}` | Os `x-ms-blob-type` `x-ms-version` valores , e `x-ms-date` cabeçalho são necessários para as operações de Armazenamento Azure. <p><p>**Importante** : Nos pedidos de acionamento HTTP de saída e pedidos de ação para o Azure Storage, o cabeçalho requer a `x-ms-version` propriedade e a versão API para a operação que pretende executar. Deve `x-ms-date` ser a data atual. Caso contrário, a sua aplicação lógica falha com um `403 FORBIDDEN` erro. Para obter a data atual no formato requerido, pode utilizar a expressão no valor do exemplo. <p>Para obter mais informações, veja estes tópicos: <p><p>- [Cabeçalhos de pedido - Snapshot Blob](/rest/api/storageservices/snapshot-blob#request) <br>- [Versão para serviços de armazenamento Azure](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Consultas** | Apenas para a operação Snapshot Blob | `comp` = `snapshot` | O nome do parâmetro de consulta e o valor para a operação. |
   |||||

   Aqui está o exemplo http ação que mostra todos estes valores de propriedade:

   ![Adicione uma ação HTTP para aceder a um recurso Azure](./media/create-managed-service-identity/http-action-example.png)

1. Agora adicione o **imóvel autenticação** à ação HTTP. A partir da nova lista **de parâmetros,** selecione **Autenticação.**

   ![Adicionar propriedade "Autenticação" à ação HTTP](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem todos os gatilhos e ações suportam permitir adicionar um tipo de autenticação. Para obter mais informações, consulte [Adicionar autenticação às chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Na lista de **tipos de autenticação,** selecione **Identidade Gerida** .

   ![Para "Autenticação", selecione "Identidade Gerida"](./media/create-managed-service-identity/select-managed-identity.png)

1. A partir da lista de identidade gerida, selecione entre as opções disponíveis com base no seu cenário.

   * Se configurar a identidade atribuída ao sistema, selecione **Identidade Gerida Atribuída do Sistema** se ainda não estiver selecionado.

     ![Selecione "Identidade Gerida Atribuída ao Sistema"](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Se configurar uma identidade atribuída ao utilizador, selecione essa identidade se ainda não tiver sido selecionada.

     ![Selecione a identidade atribuída ao utilizador](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Este exemplo continua com a Identidade Gerida Atribuída ao **Sistema.**

1. Em alguns gatilhos e ações, a propriedade **do Audience** também aparece para definir o ID do recurso alvo. Desaprova a propriedade **do Público** no ID de [recursos para o recurso ou serviço alvo.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) Caso contrário, por padrão, a propriedade **audience** usa o `https://management.azure.com/` ID de recurso, que é o ID de recurso para Azure Resource Manager.

   > [!IMPORTANT]
   > Certifique-se de que o ID do recurso-alvo *corresponde exatamente* ao valor que o Azure Ative Directory (AD) espera, incluindo quaisquer cortes de rasto necessários. Por exemplo, o ID de recursos para todas as contas de Armazenamento Azure Blob requer um corte de fuga. No entanto, a identificação de recursos para uma conta de armazenamento específica não requer um corte de fuga. Consulte os [IDs de recursos dos serviços Azure que suportam a Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Este exemplo define a propriedade **do Audience** para que `https://storage.azure.com/` os tokens de acesso utilizados para a autenticação sejam válidos para todas as contas de armazenamento. No entanto, também pode especificar o URL do serviço de raiz, `https://fabrikamstorageaccount.blob.core.windows.net` para uma conta de armazenamento específica.

   ![Definir a propriedade "Audience" para direcionar o ID do recurso](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para obter mais informações sobre a autorização de acesso com a Azure AD para armazenamento Azure, consulte estes tópicos:

   * [Autorizar o acesso a blobs e filas Azure utilizando o Azure Ative Directory](../storage/common/storage-auth-aad.md)
   * [Autorizar o acesso ao Azure Storage com o Azure Ative Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Continue a construir a aplicação lógica da forma que quiser.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Desativar identidade gerida

Para parar de usar uma identidade gerida para a sua aplicação lógica, tem estas opções:

* [Portal do Azure](#azure-portal-disable)
* [Modelos de gestor de recursos Azure](#template-disable)
* Azure PowerShell
  * [Remover atribuição de funções](../role-based-access-control/role-assignments-powershell.md)
  * [Eliminar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* CLI do Azure
  * [Remover atribuição de funções](../role-based-access-control/role-assignments-cli.md)
  * [Eliminar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* API REST do Azure
  * [Remover atribuição de funções](../role-based-access-control/role-assignments-rest.md)
  * [Eliminar identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Se eliminar a sua aplicação lógica, o Azure remove automaticamente a identidade gerida do Azure AD.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Desativar identidade gerida no portal Azure

No portal Azure, primeiro remova o acesso da identidade ao [seu recurso alvo](#disable-identity-target-resource). Em seguida, desligue a identidade atribuída ao sistema ou remova a identidade atribuída ao utilizador da [sua aplicação lógica](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Remover o acesso à identidade dos recursos

1. No [portal Azure,](https://portal.azure.com)aceda ao recurso Azure alvo onde pretende remover o acesso à identidade gerida.

1. A partir do menu do recurso-alvo, selecione **Access control (IAM)** . Sob a barra de ferramentas, selecione **atribuições de funções** .

1. Na lista de funções, selecione as identidades geridas que pretende remover. Na barra de ferramentas, selecione **Remover** .

   > [!TIP]
   > Se a opção **Remover** estiver desativada, é provável que não tenha permissões. Para obter mais informações sobre as permissões que lhe permitem gerir funções de recursos, consulte [permissões de função de Administrador no Diretório Ativo Azure](../active-directory/roles/permissions-reference.md).

A identidade gerida é agora removida e já não tem acesso ao recurso-alvo.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Desativar a identidade gerida na aplicação lógica

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicativos lógicos, em **Definições,** selecione **Identidade,** e, em seguida, siga os passos para a sua identidade:

   * Selecione **sistema atribuído**  >  **On**  >  **no Save** . Quando O Azure lhe pedir para confirmar, selecione **Sim** .

     ![Desativar a identidade atribuída ao sistema](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecione **o Utilizador atribuído** e a identidade gerida e, em seguida, selecione **Remover** . Quando O Azure lhe pedir para confirmar, selecione **Sim** .

     ![Remover a identidade atribuída ao utilizador](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A identidade gerida está agora desativada na sua aplicação lógica.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Desativar a identidade gerida no modelo do Gestor de Recursos Azure

Se criou a identidade gerida da aplicação lógica utilizando um modelo de Gestor de Recursos Azure, desconfie `identity` da propriedade do objeto para `type` `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passos seguintes

* [Acesso seguro e dados em Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
