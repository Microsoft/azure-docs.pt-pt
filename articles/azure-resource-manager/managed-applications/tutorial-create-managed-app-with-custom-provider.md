---
title: Tutorial - ações personalizadas & recursos
description: Este tutorial descreve como criar uma Aplicação Gerida Azure com um Fornecedor Personalizado Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e8824f534f573d97353cc86d2a1b112b1acdb211
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494507"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Tutorial: Criar aplicação gerida com ações e recursos personalizados

Neste tutorial, cria a sua própria aplicação gerida com ações e recursos personalizados. A aplicação gerida conterá uma ação personalizada na `Overview` página, um tipo de recurso personalizado exibido como um item de menu separado `Table of Content` e uma ação de contexto personalizada na página de recursos personalizados.

Este tutorial inclui os seguintes passos:

> [!div class="checklist"]
> * Ficheiro de definição de interface de utilizador do autor para criar uma instância de aplicação gerida
> * Modelo de implementação de autor com [Azure Custom Provider](../custom-providers/overview.md), Conta de Armazenamento Azure e Função Azure
> * Autor vê artefacto de definição com ações e recursos personalizados
> * Implementar uma definição de aplicação gerida
> * Implementar uma instância de aplicação gerida
> * Realizar ações personalizadas e criar recursos personalizados

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de saber:

* Como [criar e publicar uma definição de aplicação gerida.](publish-service-catalog-app.md)
* Como [implementar a aplicação do Catálogo de Serviços através do portal Azure.](deploy-service-catalog-quickstart.md)
* Como criar interface de [utilizador do portal Azure para a sua aplicação gerida.](create-uidefinition-overview.md)
* [Ver capacidades de artefactos de definição.](concepts-view-definition.md)
* [Capacidades do Fornecedor Personalizado Azure.](../custom-providers/overview.md)

## <a name="user-interface-definition"></a>Definição de interface de utilizador

Neste tutorial, cria-se uma aplicação gerida e o seu grupo de recursos geridos conterá a instância personalizada do fornecedor, a conta de armazenamento e a função. A Função Azure utilizada neste exemplo implementa uma API que trata de operações personalizadas de fornecedores para ações e recursos. A Conta de Armazenamento Azure é usada como armazenamento básico para os seus recursos de fornecedor personalizados.

A definição de interface de utilizador para criar uma instância de aplicação gerida inclui `funcname` e `storagename` elementos de entrada. O nome e o nome da função de armazenamento devem ser globalmente únicos. Por predefinição, os ficheiros de função serão implantados a partir do pacote de função da [amostra,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)mas pode alterá-lo adicionando um elemento de entrada para um link de pacotecreateUIDefinition.js* em*:

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

e a produção em *createUIDefinition.jsem:*

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

A *createUIDefinition.js* completa na amostra pode ser encontrada em [Referência: Artefactos de elementos de interface do utilizador](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Modelo com provedor personalizado

Para criar uma instância de aplicação gerida com fornecedor personalizado, é necessário definir o recurso personalizado do fornecedor com o nome **público** e escrever **Microsoft.CustomProviders/resourceProviders** no seu **mainTemplate.jsem**. Nesse recurso, define os tipos de recursos e ações para o seu serviço. Para implementar a Função Azure e as instâncias da Conta de Armazenamento Azure definem recursos de tipo `Microsoft.Web/sites` `Microsoft.Storage/storageAccounts` e, respectivamente.

Neste tutorial, irá criar um `users` tipo de recurso, `ping` ação personalizada e `users/contextAction` ação personalizada que será realizada num contexto de `users` recurso personalizado. Para cada tipo de recurso e ação fornecem um ponto final que aponta para a função com o nome fornecido em [createUIDefinition.jsem](#user-interface-definition). Especificar o **RoteamentoType** como `Proxy,Cache` para tipos de recursos e para `Proxy` ações:

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

A *mainTemplate.js* completa na amostra pode ser encontrada em [Referência: Artefacto do modelo de implantação](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Artefacto de definição de vista

Para definir a interface do utilizador que inclua ações personalizadas e recursos personalizados na sua aplicação gerida, precisa de **viewDefinition.jsde** autor em artefactos. Para obter mais informações sobre o artefacto de definição de visualização, consulte [o artefacto de definição de ver em Aplicações Geridas Azure](concepts-view-definition.md).

Neste tutorial, define:
* Uma *página de visão geral* com botão de barra de ferramentas que representa uma ação personalizada com entrada de texto `TestAction` básico.
* Uma página *de Utilizadores* que representa um tipo de recurso `users` personalizado.
* Uma ação de recurso personalizada `users/contextAction` na página *dos Utilizadores* que será realizada num contexto de recurso personalizado do tipo `users` .

O exemplo a seguir mostra a configuração de visualização para uma página "Visão geral":

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

O exemplo abaixo inclui a configuração da página de recursos "Utilizadores" com ação de recurso personalizado:

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

Os *viewDefinition.jscompletos na* amostra podem ser encontrados em [Referência: Ver artefacto de definição](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definição de aplicação gerida

Embale os seguintes artefactos de aplicação geridos para arquivar zip e carregá-lo para armazenamento:

* createUiDefinition.jsem
* mainTemplate.jsem
* viewDefinition.jsem

Todos os ficheiros devem estar ao nível da raiz. O pacote com artefactos pode ser armazenado em qualquer armazenamento, por exemplo, blob GitHub ou blob de conta de armazenamento Azure. Aqui está um script para carregar o pacote de aplicações para a conta de armazenamento: 

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

Execute o script Azure CLI abaixo ou siga os passos no portal Azure para implementar uma definição de aplicação gerida pelo Catálogo de Serviços:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli-interactive)

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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva e selecione Centro de **Aplicações Geridas.**
2. No **Centro de Aplicações Geridas,** escolha **a definição de aplicação do Catálogo** de Serviços e clique em **Adicionar**. 
    
    ![Adicionar Catálogo de Serviços](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Fornecer valores para a criação de uma definição de Catálogo de Serviços:

    * Forneça um **nome** único para a definição de Catálogo de Serviço, **Nome de Exibição** e *Descrição*(opcional).
    * Selecione a **Subscrição,** **Grupo de Recursos**e **Localização** onde será criada a definição de aplicação. Pode utilizar o mesmo grupo de recursos que é utilizado para o pacote zip ou criar um novo grupo de recursos.
    * Para um **Pacote File Uri,** forneça o caminho para o ficheiro zip que criou no passo anterior.

    ![Fornecer valores](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Quando chegar à secção de Autenticação e Nível de Bloqueio, **selecione Adicionar Autorização**.

    ![Adicionar autorização](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Selecione um grupo de Diretório Ativo Azure para gerir os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Quando tiver fornecido todos os valores, **selecione Criar**.

   ![Criar definição de aplicação gerida](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instância de aplicação gerida

Quando a definição de aplicação gerida for implementada, execute o script abaixo ou siga os passos no portal Azure para implementar a sua instância de aplicação gerida com o fornecedor personalizado:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azurecli-interactive)

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

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, escreva e selecione Centro de **Aplicações Geridas.**
2. No **Centro de Aplicações Geridas,** escolha **aplicações de Catálogo de Serviços** e clique em **Adicionar**. 

    ![Adicionar aplicação gerida](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. No Catálogo de **Serviços, as aplicações** do Catálogo de Serviços de definição de catálogo de serviços apresentam o nome na caixa de pesquisa. Selecione a definição criada no passo anterior e clique em **Criar.**

    ![Selecionar catálogo de serviços](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Fornecer valores para criar uma instância de aplicação gerida a partir da definição de Catálogo de Serviços:

    * Selecione a **Subscrição,** **Grupo de Recursos**e **Localização** onde será criada a instância de aplicação.
    * Forneça um nome exclusivo da Função Azure e nome da Conta de Armazenamento Azure.

    ![Definições da aplicação](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Quando a validação passou, clique **em OK** para implementar uma instância de uma aplicação gerida. 
    
    ![Implementar aplicação gerida](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Ações e recursos personalizados

Depois de implementada a aplicação do catálogo de serviços, tem dois novos grupos de recursos. O primeiro grupo de recursos `applicationGroup` contém uma instância da aplicação gerida, o segundo grupo de recursos `managedResourceGroup` detém os recursos para a aplicação gerida, incluindo o **fornecedor personalizado.**

![Grupos de recursos de aplicação](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Pode ir a uma instância de aplicação gerida e realizar **ações personalizadas** na página "Overview", criar recursos **personalizados dos utilizadores** na página "Utilizadores" e executar **ação de contexto personalizado** em recursos personalizados.

* Aceda à página "Visão Geral" e clique no botão "Ping Action":

![Realizar ação personalizada](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Aceda à página "Utilizadores" e clique no botão "Adicionar". Fornecer entradas para criar um recurso e submeter o formulário:

![Criar recurso personalizado](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Aceda à página "Utilizadores", selecione um recurso de "utilizadores" e clique em "Ação de Contexto Personalizado":

![Criar recurso personalizado](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>À procura de ajuda

Se tiver dúvidas sobre aplicações geridas azure, tente perguntar no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida, por isso verifique primeiro antes de publicar. Adicione a etiqueta `azure-managedapps` para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Para publicar a aplicação gerida no Microsoft Azure Marketplace, veja [Aplicações geridas do Azure no Marketplace](publish-marketplace-app.md).

Saiba mais sobre [a Azure Custom Providers.](../custom-providers/overview.md)
