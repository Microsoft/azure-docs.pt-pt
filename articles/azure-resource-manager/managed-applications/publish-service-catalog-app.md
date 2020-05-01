---
title: Publicar app gerida por catálogo de serviços
description: Mostra como criar uma aplicação gerida do Azure que se destina aos membros da sua organização.
author: tfitzmac
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: 47eda62810b1098fcaca5b734be4f74edc0db49a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609362"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Quickstart: Criar e publicar uma definição de aplicação gerida

Este quickstart fornece uma introdução ao trabalho com [aplicações geridas azure](overview.md). Pode criar e publicar uma aplicação gerida que se destina a membros da sua organização.

Para publicar uma aplicação gerida no seu catálogo de serviços, deve:

* Criar um modelo que define os recursos a implementar com a aplicação gerida.
* Defina os elementos de interface de utilizador para o portal quando implementar a aplicação gerida.
* Crie um pacote .zip que contém os ficheiros de modelo que são precisos.
* Decida que utilizador, grupo ou aplicação precisa de aceder ao grupo de recursos na subscrição do utilizador.
* Crie a definição de aplicação gerida que aponta para o pacote .zip e pedidos de acesso para a identidade.

## <a name="create-the-arm-template"></a>Criar o modelo ARM

Cada definição de aplicação gerida inclui um ficheiro denominado **mainTemplate.json**. Nele, defina os recursos do Azure a implementar. O modelo não é diferente de um modelo normal do Gestor de Recursos Azure (ARM).

Crie um ficheiro denominado **mainTemplate.json**. O nome é sensível a maiúsculas e minúsculas.

Adicione o seguinte JSON ao ficheiro. Define os parâmetros para criar uma conta de armazenamento e especifica as propriedades da conta de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
        }
    }
}
```

Guarde o ficheiro mainTemplate.json.

## <a name="define-your-create-experience"></a>Defina a sua experiência de criação

Como editora, define a experiência do portal para criar a aplicação gerida. O ficheiro **createUiDefinition.json** gera a interface do portal. Define como os utilizadores fornecem entrada para cada parâmetro utilizando elementos de [controlo,](create-uidefinition-elements.md) incluindo drop-downs, caixas de texto e caixas de palavra-passe.

Crie um ficheiro chamado **createUiDefinition.json** (este nome é sensível a casos)

Adicione o seguinte starter JSON ao ficheiro e guarde-o.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Para saber mais, consulte [Começar com createUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Empacote os ficheiros

Adicione os dois ficheiros num ficheiro .zip com o nome app.zip. Os dois ficheiros têm de estar ao nível da raiz do ficheiro .zip. Se os colocar numa pasta, receberá um erro ao criar a definição da aplicação gerida que indica que os ficheiros necessários não estão presentes.

Carregue o pacote para uma localização acessível a partir de onde pode ser utilizada. Você precisará fornecer um nome único para a conta de armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Criar a definição da aplicação gerida

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Crie uma aplicação ou grupo de utilizador no Azure Active Directory

O próximo passo é selecionar um grupo de utilizadores, utilizador ou aplicação para gerir os recursos para o cliente. Esta identidade tem permissões no grupo de recursos gerido, de acordo com a função que é atribuída. A função pode ser qualquer função de Controlo de Acesso Baseado em Funções (RBAC) incorporada, como o Proprietário ou Contribuidor. Para criar um novo grupo de utilizadores do Active Directory, veja [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

É preciso o ID de objeto do grupo de utilizadores a utilizar para gerir os recursos. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Obtenha o ID de definição de função

Em seguida, precisa do ID da definição da função da função incorporada RBAC a que pretende conceder acesso ao utilizador, grupo de utilizadores ou aplicação. Normalmente, utiliza a função de Proprietário ou Contribuidor ou Leitor. O comando seguinte mostra como obter o ID da definição da função da função Proprietário:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Criar a definição da aplicação gerida

Se ainda não tiver um grupo de recursos para armazenar a definição da aplicação gerida, crie uma agora:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Agora, crie o recurso de definição da aplicação gerida.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Após a conclusão do comando, terá uma definição de aplicação gerida no seu grupo de recursos.

Alguns dos parâmetros utilizados no exemplo anterior são:

* **grupo de recursos**: O nome do grupo de recursos onde é criada a definição de aplicação gerida.
* **nível de bloqueio**: O tipo de bloqueio colocado no grupo de recursos geridos. Impede o cliente de realizar operações não desejadas neste grupo. Atualmente, o único nível de bloqueio suportado é ReadOnly. Quando é especificado ReadOnly, o cliente só consegue ler os recursos presentes no grupo de recursos gerido. As entidades editoras que têm acesso ao grupo de recurso gerido estão isentos do bloqueio.
* **authorizations**: descreve o ID de principal e o ID da definição da função utilizados para conceder permissões ao grupo de recursos gerido. É especificado com formato `<principalId>:<roleDefinitionId>`. Se for necessário mais do que um valor, especifique-os no formato `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. Os valores são separados por uma vírposta.
* **ficheiro de pacote URI**: A localização de um pacote .zip que contém os ficheiros necessários.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Traga o seu próprio armazenamento para a definição de aplicação gerida

Pode optar por armazenar a definição de aplicação gerida dentro de uma conta de armazenamento fornecida por si durante a criação para que a sua localização e acesso possam ser totalmente geridos por si para as suas necessidades regulamentares.

> [!NOTE]
> Traga o seu próprio armazenamento apenas com modelo ARM ou implementações de API REST da definição de aplicação gerida.

### <a name="select-your-storage-account"></a>Selecione a sua conta de armazenamento

Tem de [criar uma conta](../../storage/common/storage-account-create.md) de armazenamento para conter a definição de aplicação gerida para utilização com o Catálogo de Serviços.

Copie a identificação de recursos da conta de armazenamento. Será utilizado mais tarde ao implementar a definição.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Delineie a atribuição de funções para "Fornecedor de Recursos de Eletrodomésticos" na sua conta de armazenamento

Antes de a definição de aplicação gerida poder ser implementada na sua conta de armazenamento, deve dar permissões aos contribuintes para a função de Fornecedor de Recursos de **Aparelhos,** de modo a poder escrever os ficheiros de definição no recipiente da sua conta de armazenamento.

1. No [portal Azure,](https://portal.azure.com)navegue para a sua conta de armazenamento.
1. Selecione **o controlo de acesso (IAM)** para visualizar as definições de controlo de acesso para a conta de armazenamento. Selecione o separador de **atribuições de funções** para ver a lista de atribuições de papéis.
1. Na janela de atribuição de **funções Adicionar,** selecione a função **Contributiva.** 
1. A partir do **acesso atribuído ao** campo, selecione Utilizador, grupo ou diretor de serviço **Azure AD**.
1. Em **Select,** procure a função de Fornecedor de Recursos de **Aparelhos** e selecione-a.
1. Salve a atribuição do papel.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Implementar a definição de aplicação gerida com um modelo ARM 

Utilize o seguinte modelo ARM para implementar a sua aplicação gerida embalada como uma nova definição de aplicação gerida no Catálogo de Serviços cujos ficheiros de definição são armazenados e mantidos na sua própria conta de armazenamento:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Adicionámos uma nova propriedade chamada **storageAccountId** às propriedades da sua aplicaçãoDefinição e fornecemos ID da conta de armazenamento que pretende armazenar a sua definição como seu valor:

Pode verificar se os ficheiros de definição de aplicação são guardados na sua conta de armazenamento fornecida num recipiente intitulado definições de **aplicações**.

> [!NOTE]
> Para maior segurança, pode criar uma definição de aplicações geridas armazená-lo numa conta de armazenamento Azure onde a [encriptação está ativada](../../storage/common/storage-service-encryption.md). Os conteúdos de definição são encriptados através das opções de encriptação da conta de armazenamento. Apenas os utilizadores com permissões para o ficheiro podem ver a definição no Catálogo de Serviços.

## <a name="make-sure-users-can-see-your-definition"></a>Confirmar que os utilizadores podem ver a definição

Tem acesso à definição de aplicação gerida, mas deve verificar se outros utilizadores na sua organização podem aceder à mesma. Conceda-lhes, pelo menos, a função de Leitor na definição. Estes podem ter herdado este nível de acesso através da subscrição ou do grupo de recursos. Para verificar quem tem acesso à definição e adicionar utilizadores ou grupos, veja [Utilizar o Controlo de Acesso Baseado em Função para gerir o acesso aos recursos da subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

Publicou a definição da aplicação gerida. Agora, saiba como implementar uma instância dessa definição.

> [!div class="nextstepaction"]
> [Início Rápido: implementar uma aplicação de catálogo de serviços](deploy-service-catalog-quickstart.md)
