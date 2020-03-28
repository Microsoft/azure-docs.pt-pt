---
title: Tutorial - ações personalizadas & recursos
description: Este tutorial descreve como criar uma Aplicação Gerida Azure com um Fornecedor Personalizado Azure.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650080"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Tutorial: Criar aplicação gerida com ações e recursos personalizados

Neste tutorial, você cria a sua própria aplicação gerida com ações e recursos personalizados. A aplicação gerida conterá uma `Overview` ação personalizada na página, um tipo de `Table of Content` recurso personalizado apresentado como um item de menu separado e uma ação de contexto personalizado na página de recursos personalizados.

Este tutorial inclui os seguintes passos:

> [!div class="checklist"]
> * Ficheiro de definição de interface de utilizador autor para criar uma instância de aplicação gerida
> * Modelo de implantação de autor com [fornecedor personalizado Azure,](../custom-providers/overview.md)Conta de Armazenamento Azure e Função Azure
> * Autor ver artefacto de definição com ações e recursos personalizados
> * Implementar uma definição de aplicação gerida
> * Implementar uma instância de aplicação gerida
> * Realizar ações personalizadas e criar recursos personalizados

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa de saber:

* Como [Criar e publicar uma definição de aplicação gerida.](publish-service-catalog-app.md)
* Como implementar a aplicação de Catálogo de [Serviços através do portal Azure.](deploy-service-catalog-quickstart.md)
* Como criar a interface de utilizador do [portal Azure para a sua aplicação gerida.](create-uidefinition-overview.md)
* Ver capacidades de [artefactode definição.](concepts-view-definition.md)
* Capacidades do [Fornecedor Personalizado Azure.](../custom-providers/overview.md)

## <a name="user-interface-definition"></a>Definição de interface de utilizador

Neste tutorial, você cria uma aplicação gerida e seu grupo de recursos geridos conterá instância personalizada de fornecedor, conta de armazenamento e função. A Função Azure utilizada neste exemplo implementa uma API que trata de operações personalizadas de fornecedores para ações e recursos. A Conta de Armazenamento Azure é usada como armazenamento básico para os recursos do seu fornecedor personalizado.

A definição de interface do utilizador `funcname` `storagename` para criar uma instância de aplicação gerida inclui e elementos de entrada. O nome da conta de armazenamento e o nome da função devem ser globalmente únicos. Por predefinição, os ficheiros de função padrão serão implantados a partir do pacote de funções da [amostra,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)mas pode alterá-lo adicionando um elemento de entrada para uma ligação de pacote em *createUIDefinition.json*:

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

e saída em *createUIDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

A amostra *createUIDefinition.json* completa pode ser encontrada na [Referência: Artefactos](reference-createuidefinition-artifact.md)de elementos de interface do utilizador .

## <a name="template-with-custom-provider"></a>Modelo com provedor personalizado

Para criar uma instância de aplicação gerida com fornecedor personalizado, precisa de definir recursos personalizados do fornecedor com nome **público** e tipo **Microsoft.CustomProviders/resourceProviders** no seu **mainTemplate.json**. Nesse recurso, define os tipos e ações de recursos para o seu serviço. Para implementar a Função Azure e a `Microsoft.Web/sites` Conta `Microsoft.Storage/storageAccounts` de Armazenamento Azure, as instâncias definem recursos de tipo e respectivamente.

Neste tutorial, você vai `users` criar `ping` um tipo `users/contextAction` de recurso, ação personalizada e `users` ação personalizada que será realizada em um contexto de um recurso personalizado. Para cada tipo e ação de recursos, forneça um ponto final que indique a função com o nome fornecido na [createUIDefinition.json](#user-interface-definition). Especifique o `Proxy,Cache` tipo de `Proxy` **encaminhamento** como para os tipos de recursos e para as ações:

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

A amostra *principal de modelo.json* completa pode ser encontrada em [Referência: Artefacto](reference-main-template-artifact.md)de modelo de implantação .

## <a name="view-definition-artifact"></a>Artefacto de definição de vista

Para definir a interface do utilizador que inclui ações personalizadas e recursos personalizados na sua aplicação gerida, precisa de ser autor do **artefactoDefini.json.** Para obter mais informações sobre o artefacto de definição de visualização, consulte o artefacto de [definição de visualização em Aplicações Geridas azure](concepts-view-definition.md).

Neste tutorial, define:
* Uma página *de visão geral* com botão `TestAction` de barra de ferramentas que representa uma ação personalizada com entrada de texto básica.
* Uma página *de Utilizadores* que `users`representa um tipo de recurso personalizado .
* Uma ação `users/contextAction` de recurso personalizado na página *Utilizadores* que será `users`realizada num contexto de recurso personalizado de tipo.

O exemplo seguinte mostra a configuração da visualização para uma página "Visão Geral":

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

O exemplo abaixo inclui a configuração da página de recursos "Utilizadores" com ação de recursos personalizados:

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

A amostra de *visão completaDefini.json* pode ser encontrada na [Referência: Ver artefacto](reference-view-definition-artifact.md)de definição .

## <a name="managed-application-definition"></a>Definição de aplicação gerida

Embala os seguintes artefactos de aplicação geridos para arquivar zip e carregá-lo para armazenamento:

* criarUiDefinition.json
* mainTemplate.json
* visãoDefinição.json

Todos os ficheiros devem estar ao nível da raiz. O pacote com artefactos pode ser armazenado em qualquer armazenamento, por exemplo, blob GitHub ou Blob Da Conta de Armazenamento Azure. Aqui está um script para fazer o upload do pacote de aplicação para a conta de armazenamento: 

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

Executar o script Azure CLI abaixo ou seguir os passos no portal Azure para implementar uma definição de aplicação gerida por Catálogo de Serviços:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

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

1. No portal Azure, selecione **Todos os serviços.** Na lista de recursos, digite e selecione Centro de **Aplicações Geridas**.
2. No Centro de **Aplicações Geridas,** escolha a definição de aplicação do Catálogo de **Serviços** e clique em **Adicionar**. 
    
    ![Adicionar catálogo de serviços](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Fornecer valores para a criação de uma definição de Catálogo de Serviços:

    * Forneça um **nome** único para a definição de Catálogo de Serviços, Nome de **Exibição** e *Descrição*(opcional).
    * Selecione a **Subscrição,** **o grupo de recursos**e a **localização** onde será criada a definição de aplicação. Pode utilizar o mesmo grupo de recursos que é utilizado para o pacote zip ou criar um novo grupo de recursos.
    * Para um **Pacote File Uri,** forneça o caminho para o ficheiro zip que criou em passo anterior.

    ![Fornecer valores](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Quando chegar à secção autenticação e bloqueio, **selecione Adicionar Autorização**.

    ![Adicionar autorização](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Selecione um grupo de Diretório Ativo Azure para gerir os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Quando tiver fornecido todos os valores, selecione **Criar**.

   ![Criar definição de aplicação gerida](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Instância de candidatura gerida

Quando a definição de aplicação gerida for implementada, execute o script abaixo ou siga os passos no portal Azure para implementar a sua instância de aplicação gerida com o fornecedor personalizado:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

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

1. No portal Azure, selecione **Todos os serviços.** Na lista de recursos, digite e selecione Centro de **Aplicações Geridas**.
2. No Centro de **Aplicações Geridas,** escolha aplicações de Catálogo de **Serviços** e clique em **Adicionar**. 

    ![Adicionar aplicação gerida](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. No catálogo de aplicações de serviço aplica o nome de exibição de definição de catálogo de catálogo de **serviços** na caixa de pesquisa. Selecione a definição criada em passo anterior e clique em **Criar**.

    ![Selecionar catálogo de serviços](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Fornecer valores para a criação de uma instância de aplicação gerida a partir da definição do Catálogo de Serviços:

    * Selecione a **Subscrição,** **o grupo de recursos**e a **localização** onde será criada a instância de aplicação.
    * Forneça um nome único da Função Azure e nome da Conta de Armazenamento Azure.

    ![Definições da aplicação](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Quando a validação passou, clique em **OK** para implementar uma instância de uma aplicação gerida. 
    
    ![Implementar aplicação gerida](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Ações e recursos personalizados

Depois de implementada a instância de aplicação do catálogo de serviços, tem dois novos grupos de recursos. O primeiro `applicationGroup` grupo de recursos contém uma instância `managedResourceGroup` da aplicação gerida, segundo grupo de recursos detém os recursos para a aplicação gerida, incluindo o **fornecedor personalizado.**

![Grupos de recursos de aplicação](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Pode ir à instância de aplicação gerida e realizar **ações personalizadas** na página "Visão Geral", criar recursos personalizados para **os utilizadores** na página "Utilizadores" e executar uma ação de **contexto personalizado** em recursos personalizados.

* Vá à página "Overview" e clique no botão "Ping Action":

![Realizar ação personalizada](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Vá à página "Utilizadores" e clique no botão "Adicionar". Fornecer inputs para criar um recurso e submeter o formulário:

![Criar recurso personalizado](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Vá à página "Utilizadores", selecione um recurso "utilizadores" e clique em "Custom Context Action":

![Criar recurso personalizado](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>À procura de ajuda

Se tiver dúvidas sobre aplicações geridas pelo Azure, tente perguntar sobre [o Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Uma pergunta semelhante pode já ter sido feita e respondida, por isso verifique primeiro antes de ser postada. Adicione a `azure-managedapps` etiqueta para obter uma resposta rápida!

## <a name="next-steps"></a>Passos seguintes

Para publicar a aplicação gerida no Microsoft Azure Marketplace, veja [Aplicações geridas do Azure no Marketplace](publish-marketplace-app.md).

Saiba mais sobre [os Fornecedores Personalizados Azure.](../custom-providers/overview.md)
