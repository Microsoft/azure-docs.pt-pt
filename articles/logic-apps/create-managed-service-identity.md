---
title: Autenticar com identidades geridas
description: Acessar recursos em outros locatários Azure Active Directory sem entrar com credenciais ou segredos usando uma identidade gerenciada
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 714faa43f34de965055ceba80de08972dd4192ac
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861205"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Autenticar o acesso aos recursos do Azure usando identidades gerenciadas em aplicativos lógicos do Azure

Para acessar os recursos em outros locatários do Azure Active Directory (AD do Azure) e autenticar sua identidade sem entrar, seu aplicativo lógico pode usar a [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) atribuída pelo sistema (anteriormente conhecida como identidade de serviço gerenciada ou msi), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não precisa fornecer ou girar segredos. Este artigo mostra como configurar e usar a identidade gerenciada atribuída pelo sistema em seu aplicativo lógico. Atualmente, as identidades gerenciadas funcionam apenas com [gatilhos e ações internas específicas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls), não conectores ou conexões gerenciadas.

Para obter mais informações, veja estes tópicos:

* [Gatilhos e ações que dão suporte a identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Serviços do Azure que dão suporte à autenticação do Azure AD com identidades gerenciadas](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Tipos de autenticação com suporte em chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limites de identidade gerenciada para aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure, ou se você não tiver uma assinatura, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/). A identidade gerenciada e o recurso do Azure de destino onde você deseja que o acesso precise usar a mesma assinatura do Azure.

* [Permissões de administrador do Azure ad](../active-directory/users-groups-roles/directory-assign-admin-roles.md) que podem atribuir funções a identidades gerenciadas no mesmo locatário do Azure AD que o recurso de destino. Para conceder acesso de identidade gerenciada a um recurso do Azure, você precisa adicionar uma função para essa identidade no recurso de destino.

* O recurso de destino do Azure que você deseja acessar

* Um aplicativo lógico que usa [gatilhos e ações que dão suporte a identidades gerenciadas](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Habilitar identidade atribuída pelo sistema

Ao contrário das identidades atribuídas pelo usuário, você não precisa criar manualmente a identidade atribuída pelo sistema. Para configurar a identidade atribuída pelo sistema de seu aplicativo lógico, aqui estão as opções que você pode usar:

* [Portal do Azure](#azure-portal-system-logic-app)
* [Modelos de Azure Resource Manager](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Habilitar identidade atribuída pelo sistema no portal do Azure

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **identidade** > **sistema atribuído**. Em **status** **, selecione > ** **salvar** > **Sim**.

   ![Habilitar a identidade atribuída pelo sistema](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Seu aplicativo lógico agora pode usar a identidade atribuída pelo sistema, que é registrada com Azure Active Directory e é representada por uma ID de objeto.

   ![ID de objeto para identidade atribuída pelo sistema](./media/create-managed-service-identity/object-id.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **ID do objeto** | <*identity-resource-ID*> | Um GUID (identificador global exclusivo) que representa a identidade atribuída pelo sistema para seu aplicativo lógico em seu locatário do Azure AD |
   ||||

1. Agora, siga as [etapas que dão acesso à identidade ao recurso](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Habilitar identidade atribuída pelo sistema no modelo Azure Resource Manager

Para automatizar a criação e implantação de recursos do Azure, como aplicativos lógicos, você pode usar [modelos de Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Para habilitar a identidade gerenciada atribuída pelo sistema para seu aplicativo lógico no modelo, adicione o objeto `identity` e a propriedade filho `type` à definição de recurso do aplicativo lógico no modelo, por exemplo:

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

Quando o Azure cria sua definição de recurso de aplicativo lógico, o objeto `identity` obtém estas propriedades adicionais:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Propriedade (JSON) | Valor | Descrição |
|-----------------|-------|-------------|
| `principalId` | <*principal-ID*> | O GUID (identificador global exclusivo) do objeto de entidade de serviço para a identidade gerenciada que representa seu aplicativo lógico no locatário do Azure AD. Esse GUID, às vezes, aparece como uma "ID de objeto" ou `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | O GUID (identificador global exclusivo) que representa o locatário do Azure AD em que o aplicativo lógico agora é um membro. Dentro do locatário do Azure AD, a entidade de serviço tem o mesmo nome que a instância do aplicativo lógico. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Conceder acesso de identidade aos recursos

Antes de usar a identidade gerenciada atribuída pelo sistema do aplicativo lógico para autenticação, conceda a essa identidade acesso ao recurso do Azure em que você planeja usar a identidade. Para concluir essa tarefa, atribua a função apropriada a essa identidade no recurso de destino do Azure. Aqui estão as opções que você pode usar:

* [Portal do Azure](#azure-portal-assign-access)
* [Modelo do Azure Resource Manager](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment))-para obter mais informações, consulte [Adicionar atribuição de função usando o RBAC do Azure e Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).
* CLI do Azure ([criação de atribuição de função AZ](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) – para obter mais informações, consulte [Adicionar atribuição de função usando o RBAC e CLI do Azure do Azure](../role-based-access-control/role-assignments-cli.md).
* [API REST do Azure](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Atribuir acesso no portal do Azure

1. Na [portal do Azure](https://portal.azure.com), vá para o recurso do Azure no qual você deseja que sua identidade gerenciada tenha acesso.

1. No menu do recurso, selecione **controle de acesso (iam)**  > **atribuições de função** em que você pode examinar as atribuições de função atuais para esse recurso. Na barra de ferramentas, selecione **adicionar** > **Adicionar atribuição de função**.

   ![Selecione "Adicionar" > "Adicionar atribuição de função"](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Se a opção **Adicionar atribuição de função** estiver desabilitada, provavelmente você não terá permissões. Para obter mais informações sobre as permissões que permitem gerenciar funções para recursos, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. Em **Adicionar atribuição de função**, selecione uma **função** que dê à sua identidade o acesso necessário ao recurso de destino.

   Para o exemplo deste tópico, sua identidade precisa de uma [função que possa acessar o blob em um contêiner de armazenamento do Azure](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![Selecione a função "colaborador de dados do blob de armazenamento"](./media/create-managed-service-identity/assign-role.png)

1. Na caixa **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**.

   ![Selecionar acesso para identidade atribuída pelo sistema](./media/create-managed-service-identity/assign-access-system.png)

1. Na caixa **selecionar** , localize e selecione seu aplicativo lógico.

   ![Selecionar aplicativo lógico para identidade atribuída pelo sistema](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Quando tiver terminado, selecione **Guardar**.

   A lista de atribuições de função do recurso de destino agora mostra a identidade e a função gerenciadas selecionadas.

   ![Funções e identidades gerenciadas adicionadas ao recurso de destino](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Agora, siga as [etapas para autenticar o acesso com a identidade](#authenticate-access-with-identity) em um gatilho ou ação que dá suporte a identidades gerenciadas.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Autenticar o acesso com identidade gerenciada

Depois de [habilitar a identidade gerenciada para seu aplicativo lógico](#azure-portal-system-logic-app) e [conceder a essa identidade acesso ao recurso ou à entidade de destino](#access-other-resources), você pode usar essa identidade em [gatilhos e ações que dão suporte a identidades gerenciadas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Se você tiver uma função do Azure na qual deseja usar a identidade atribuída pelo sistema, primeiro [habilite a autenticação para o Azure Functions](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Estas etapas mostram como usar a identidade gerenciada com um gatilho ou ação por meio do portal do Azure. Para especificar a identidade gerenciada em uma definição de JSON subjacente de um gatilho ou ação, consulte [autenticação de identidade gerenciada](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

1. Se você ainda não tiver feito isso, adicione o [gatilho ou a ação que dá suporte a identidades gerenciadas](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   Por exemplo, o gatilho ou a ação HTTP pode usar a identidade atribuída pelo sistema que você habilitou para seu aplicativo lógico. Em geral, o gatilho ou a ação HTTP usa essas propriedades para especificar o recurso ou a entidade que você deseja acessar:

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Método** | Sim | O método HTTP usado pela operação que você deseja executar |
   | **URI** | Sim | A URL do ponto de extremidade para acessar a entidade ou o recurso do Azure de destino. A sintaxe de URI geralmente inclui a [ID de recurso](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) para o recurso ou serviço do Azure. |
   | **Headers** (Cabeçalhos) | Não | Todos os valores de cabeçalho que você precisa ou deseja incluir na solicitação de saída, como o tipo de conteúdo |
   | **Consultas** | Não | Todos os parâmetros de consulta que você precisa ou deseja incluir na solicitação, como o parâmetro para uma operação específica ou a versão da API para a operação que você deseja executar |
   | **Autenticação** | Sim | O tipo de autenticação a ser usado para autenticar o acesso ao recurso ou à entidade de destino |
   ||||

   Como um exemplo específico, suponha que você deseja executar a [operação de blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) em um blob na conta de armazenamento do Azure em que você configurou o acesso para sua identidade anteriormente. No entanto, o [conector do armazenamento de BLOBs do Azure](https://docs.microsoft.com/connectors/azureblob/) atualmente não oferece essa operação. Em vez disso, você pode executar essa operação usando a [ação http](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) ou outra [operação da API REST do serviço blob](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Para acessar contas de armazenamento do Azure por trás de firewalls usando solicitações HTTP e identidades gerenciadas, verifique se você também configurou sua conta de armazenamento com a [exceção que permite o acesso por serviços confiáveis da Microsoft](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Para executar a [operação de blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob), a ação http especifica essas propriedades:

   | Propriedade | Obrigatório | Valor de exemplo | Descrição |
   |----------|----------|---------------|-------------|
   | **Método** | Sim | `PUT`| O método HTTP usado pela operação de blob de instantâneo |
   | **URI** | Sim | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | A ID de recurso para um arquivo de armazenamento de BLOBs do Azure no ambiente global (público) do Azure, que usa essa sintaxe |
   | **Headers** (Cabeçalhos) | Sim, para o armazenamento do Azure | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Os valores de cabeçalho `x-ms-blob-type` e `x-ms-version` que são necessários para operações de armazenamento do Azure. <p><p>**Importante**: em solicitações de ação e gatilho http de saída para o armazenamento do Azure, o cabeçalho requer a propriedade `x-ms-version` e a versão da API para a operação que você deseja executar. <p>Para obter mais informações, veja estes tópicos: <p><p>[cabeçalhos de solicitação de - -blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- o [controle de versão dos serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Consultas** | Sim, para esta operação | `comp` = `snapshot` | O nome e o valor do parâmetro de consulta para a operação de blob de instantâneo. |
   | **Autenticação** | Sim | `Managed Identity` | O tipo de autenticação a ser usado para autenticar o acesso ao blob do Azure |
   |||||

   Aqui está o exemplo de ação HTTP que mostra todos esses valores de propriedade:

   ![Adicionar uma ação HTTP para acessar um recurso do Azure](./media/create-managed-service-identity/http-action-example.png)

   Para obter mais informações sobre todas as operações disponíveis da API REST do Azure, consulte a [referência da API REST do Azure](https://docs.microsoft.com/rest/api/azure/).

1. Na lista **autenticação** , selecione **identidade gerenciada**. Se a [propriedade de **autenticação** tiver suporte](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , mas oculta, abra a lista **Adicionar novo parâmetro** e selecione **autenticação**.

   > [!NOTE]
   > Nem todos os gatilhos e ações permitem selecionar um tipo de autenticação. Para obter mais informações, consulte [Adicionar autenticação a chamadas de saída](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Na propriedade "autenticação", selecione "identidade gerenciada"](./media/create-managed-service-identity/select-managed-identity.png)

1. Depois de selecionar a **identidade gerenciada**, a propriedade **Audience** é exibida para alguns gatilhos e ações. Se a propriedade **Audience** tiver suporte, mas oculta, abra a lista **Adicionar novo parâmetro** e selecione **público**.

1. Certifique-se de definir o valor **público** como a ID de recurso para o recurso ou serviço de destino. Caso contrário, por padrão, a propriedade **Audience** usa a ID de recurso `https://management.azure.com/`, que é a ID de recurso para Azure Resource Manager.

   > [!IMPORTANT]
   > Verifique se a ID do recurso de destino *corresponde exatamente* ao valor que Azure Active Directory (AD) espera, incluindo as barras à direita necessárias. Por exemplo, a ID de recurso para todas as contas de armazenamento de BLOBs do Azure requer uma barra à direita. No entanto, a ID de recurso para uma conta de armazenamento específica não requer uma barra à direita. Verifique as [IDs de recurso dos serviços do Azure que dão suporte ao Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Este exemplo define a propriedade **Audience** como `https://storage.azure.com/` para que os tokens de acesso usados para autenticação sejam válidos para todas as contas de armazenamento. No entanto, você também pode especificar a URL do serviço raiz, `https://fabrikamstorageaccount.blob.core.windows.net`, para uma conta de armazenamento específica.

   ![Especificar a ID do recurso de destino na propriedade "Audience"](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Para obter mais informações sobre como autorizar o acesso com o Azure AD para o armazenamento do Azure, consulte estes tópicos:

   * [Autorizar o acesso a BLOBs e filas do Azure usando Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Autorizar o acesso ao armazenamento do Azure com Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Remover identidade atribuída pelo sistema

Para parar de usar a identidade atribuída pelo sistema para seu aplicativo lógico, você tem estas opções:

* [Portal do Azure](#azure-portal-disable)
* [Modelos de Azure Resource Manager](#template-disable)
* [O Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Se você excluir seu aplicativo lógico, o Azure removerá automaticamente a identidade gerenciada do Azure AD.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Remover a identidade atribuída pelo sistema no portal do Azure

No portal do Azure, remova a identidade atribuída pelo sistema [do seu aplicativo lógico e o](#disable-identity-logic-app) acesso da identidade [do recurso de destino](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Remover identidade atribuída pelo sistema do aplicativo lógico

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer de aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **identidade** > **sistema atribuído**. Em **status**, selecione **desativado** > **salvar** > **Sim**.

   ![Parar de usar a identidade atribuída pelo sistema](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Remover acesso de identidade dos recursos

1. Na [portal do Azure](https://portal.azure.com), vá para o recurso de destino do Azure no qual você deseja remover o acesso para uma identidade gerenciada.

1. No menu do recurso de destino, selecione **controle de acesso (iam)** . Na barra de ferramentas, selecione **atribuições de função**.

1. Na lista funções, selecione as identidades gerenciadas que você deseja remover. Na barra de ferramentas, selecione **remover**.

   > [!TIP]
   > Se a opção **remover** estiver desabilitada, provavelmente você não terá permissões. Para obter mais informações sobre as permissões que permitem gerenciar funções para recursos, consulte [permissões de função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A identidade gerenciada agora é removida e não tem mais acesso ao recurso de destino.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Desabilitar identidade gerenciada no modelo Azure Resource Manager

Se você habilitou a identidade gerenciada pelo sistema do aplicativo lógico usando um modelo de Azure Resource Manager, defina a propriedade filho `type` do objeto `identity` como `None`. Essa ação também exclui a ID da entidade de segurança da identidade gerenciada pelo sistema do Azure AD.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Passos seguintes

* [Proteger o acesso e os dados no aplicativo lógico do Azure](../logic-apps/logic-apps-securing-a-logic-app.md)
