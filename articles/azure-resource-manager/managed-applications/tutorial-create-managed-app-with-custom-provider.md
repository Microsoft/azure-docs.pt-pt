---
title: Tutorial-& recursos de ações personalizadas
description: Este tutorial descreve como criar um aplicativo gerenciado do Azure com um provedor personalizado do Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650080"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Tutorial: criar um aplicativo gerenciado com ações e recursos personalizados

Neste tutorial, você criará seu próprio aplicativo gerenciado com ações e recursos personalizados. O aplicativo gerenciado conterá uma ação personalizada na página `Overview`, um tipo de recurso personalizado exibido como um item de menu separado em `Table of Content` e uma ação de contexto Personalizada na página de recursos personalizados.

Este tutorial inclui as seguintes etapas:

> [!div class="checklist"]
> * Autor do arquivo de definição de interface do usuário para criar uma instância de aplicativo gerenciado
> * Modelo de implantação de autor com o [provedor personalizado do](../custom-providers/overview.md)Azure, a conta de armazenamento do Azure e o Azure function
> * Artefato de definição de exibição de autor com ações e recursos personalizados
> * Implantar uma definição de aplicativo gerenciado
> * Implantar uma instância do aplicativo gerenciado
> * Executar ações personalizadas e criar recursos personalizados

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa saber:

* Como [criar e publicar uma definição de aplicativo gerenciado](publish-service-catalog-app.md).
* Como [implantar o aplicativo de catálogo de serviços por meio de portal do Azure](deploy-service-catalog-quickstart.md).
* Como [criar portal do Azure interface do usuário para seu aplicativo gerenciado](create-uidefinition-overview.md).
* [Exibir](concepts-view-definition.md) recursos de artefato de definição.
* Recursos [do provedor personalizado do Azure](../custom-providers/overview.md) .

## <a name="user-interface-definition"></a>Definição da interface do usuário

Neste tutorial, você criará um aplicativo gerenciado e seu grupo de recursos gerenciado conterá a instância personalizada do provedor, a conta de armazenamento e a função. A função do Azure usada neste exemplo implementa uma API que manipula operações de provedor personalizadas para ações e recursos. A conta de armazenamento do Azure é usada como armazenamento básico para os recursos do provedor personalizado.

A definição da interface do usuário para criar uma instância de aplicativo gerenciado inclui `funcname` e `storagename` elementos de entrada. O nome da conta de armazenamento e o nome da função devem ser globalmente exclusivos. Por padrão, os arquivos de função serão implantados do [pacote de funções de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), mas você pode alterá-los adicionando um elemento de entrada para um link de pacote em *createUIDefinition. JSON*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

e a saída em *createUIDefinition. JSON*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

O exemplo *createUIDefinition. JSON* completo pode ser encontrado em [referência: artefatos de elementos da interface do usuário](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Modelo com provedor personalizado

Para criar uma instância de aplicativo gerenciado com o provedor personalizado, você precisa definir o recurso de provedor personalizado com o nome **público** e digitar **Microsoft. CustomProviders/resourceProviders** em seu **MainTemplate. JSON**. Nesse recurso, você define os tipos de recursos e as ações para o serviço. Para implantar as instâncias do Azure Function e da conta de armazenamento do Azure, defina os recursos do tipo `Microsoft.Web/sites` e `Microsoft.Storage/storageAccounts` respectivamente.

Neste tutorial, você criará um `users` tipo de recurso, `ping` ação personalizada e `users/contextAction` ação personalizada que será executada em um contexto de um recurso personalizado `users`. Para cada tipo de recurso e ação, forneça um ponto de extremidade apontando para a função com o nome fornecido em [createUIDefinition. JSON](#user-interface-definition). Especifique o tipo de **Roteamento** como `Proxy,Cache` para tipos de recursos e `Proxy` para ações:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

O exemplo *MainTemplate. JSON* completo pode ser encontrado na [referência: artefato do modelo de implantação](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Artefacto de definição de vista

Para definir a interface do usuário que inclui ações personalizadas e recursos personalizados em seu aplicativo gerenciado, você precisa criar o artefato **viewDefinition. JSON** . Para obter mais informações sobre o artefato de definição de exibição, consulte [Exibir artefato de definição em aplicativos gerenciados do Azure](concepts-view-definition.md).

Neste tutorial, você definirá:
* Uma página de *visão geral* com botão de barra de ferramentas que representa uma ação personalizada `TestAction` com entrada de texto básica.
* Uma página de *usuários* que representa um tipo de recurso personalizado `users`.
* Uma ação de recurso Personalizada `users/contextAction` na página *usuários* que será executada em um contexto de recurso personalizado do tipo `users`.

O exemplo a seguir mostra a configuração de exibição para uma página de "visão geral":

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

O exemplo a seguir inclui a configuração da página de recursos "usuários" com ação de recurso Personalizada:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

O exemplo *viewDefinition. JSON* completo pode ser encontrado em [referência: Exibir artefato de definição](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definição de aplicativo gerenciado

Empacote os seguintes artefatos de aplicativo gerenciado para Zip Archive e carregue-os no armazenamento:

* createUiDefinition.json
* MainTemplate. JSON
* viewDefinition. JSON

Todos os arquivos devem estar no nível raiz. O pacote com artefatos pode ser armazenado em qualquer armazenamento, por exemplo, BLOB do GitHub ou BLOB da conta de armazenamento do Azure. Aqui está um script para carregar o pacote de aplicativos na conta de armazenamento: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Execute o script de CLI do Azure abaixo ou siga as etapas em portal do Azure para implantar uma definição de aplicativo gerenciado pelo catálogo de serviços:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-clitabazurecli-interactive"></a>[CLI do Azure](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, digite e selecione **centro de aplicativos gerenciados**.
2. No **centro de aplicativos gerenciados**, escolha **definição de aplicativo do catálogo de serviços** e clique em **Adicionar**. 
    
    ![Adicionar Catálogo de serviços](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Forneça valores para criar uma definição de catálogo de serviços:

    * Forneça um **nome** exclusivo para a definição do catálogo de serviços, o **nome de exibição** e a *Descrição*(opcional).
    * Selecione a **assinatura**, o **grupo de recursos**e o **local** em que a definição de aplicativo será criada. Você pode usar o mesmo grupo de recursos que é usado para o pacote zip ou criar um novo grupo de recursos.
    * Para um **URI de arquivo de pacote**, forneça o caminho para o arquivo zip que você criou na etapa anterior.

    ![Fornecer valores](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Quando chegar à seção autenticação e nível de bloqueio, selecione **Adicionar autorização**.

    ![Adicionar autorização](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Selecione um grupo de Azure Active Directory para gerenciar os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Quando você tiver fornecido todos os valores, selecione **criar**.

   ![Criar definição de aplicativo gerenciado](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instância do aplicativo gerenciado

Quando a definição de aplicativo gerenciado for implantada, execute o script abaixo ou siga as etapas em portal do Azure para implantar sua instância do aplicativo gerenciado com o provedor personalizado:

# <a name="azure-clitabazurecli-interactive"></a>[CLI do Azure](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, digite e selecione **centro de aplicativos gerenciados**.
2. No **centro de aplicativos gerenciados**, escolha **aplicativos do catálogo de serviços** e clique em **Adicionar**. 

    ![Adicionar aplicativo gerenciado](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Na página **aplicativos do catálogo de serviços** , digite nome de exibição da definição do catálogo de serviços na caixa de pesquisa. Selecione a definição criada na etapa anterior e clique em **criar**.

    ![Selecionar catálogo de serviços](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Forneça valores para criar uma instância de aplicativo gerenciado a partir da definição do catálogo de serviços:

    * Selecione a **assinatura**, o **grupo de recursos**e o **local** em que a instância do aplicativo será criada.
    * Forneça um nome de função do Azure exclusivo e o nome da conta de armazenamento do Azure.

    ![Definições da aplicação](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Quando a validação for aprovada, clique em **OK** para implantar uma instância de um aplicativo gerenciado. 
    
    ![Implantar aplicativo gerenciado](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Ações e recursos personalizados

Depois que a instância do aplicativo do catálogo de serviços tiver sido implantada, você terá dois novos grupos de recursos. O primeiro grupo de recursos `applicationGroup` contém uma instância do aplicativo gerenciado, o segundo grupo de recursos `managedResourceGroup` contém os recursos para o aplicativo gerenciado, incluindo o **provedor personalizado**.

![Grupos de recursos do aplicativo](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Você pode ir para a instância do aplicativo gerenciado e executar a **ação personalizada** na página "visão geral", criar um recurso personalizado **dos usuários** na página "usuários" e executar a **ação de contexto personalizado** em recurso personalizado.

* Vá para a página "visão geral" e clique no botão "ação de ping":

![Executar ação personalizada](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Vá para a página "usuários" e clique no botão "Adicionar". Forneça entradas para criar um recurso e enviar o formulário:

![Criar recurso personalizado](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Vá para a página "usuários", selecione um recurso "usuários" e clique em "ação de contexto personalizado":

![Criar recurso personalizado](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Procurando ajuda

Se você tiver dúvidas sobre os aplicativos gerenciados do Azure, tente solicitar [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido solicitada e respondida, portanto, verifique primeiro antes de lançar. Adicione a marca `azure-managedapps` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Para publicar a aplicação gerida no Microsoft Azure Marketplace, veja [Aplicações geridas do Azure no Marketplace](publish-marketplace-app.md).

Saiba mais sobre os [provedores personalizados do Azure](../custom-providers/overview.md).
