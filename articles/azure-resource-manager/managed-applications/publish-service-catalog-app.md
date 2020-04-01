---
title: Publicar app gerida por catálogo de serviços
description: Mostra como criar uma aplicação gerida do Azure que se destina aos membros da sua organização.
author: tfitzmac
ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 13c45bc6e67d9d3d06a70b7cf3326cc112cd7829
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473018"
---
# <a name="tutorial-create-and-publish-a-managed-application-definition"></a>Tutorial: Criar e publicar uma definição de candidatura gerida

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode criar e publicar [aplicações geridas](overview.md) do Azure que se destinam aos membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que cumprem normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, e não no Azure marketplace.

Para publicar uma aplicação gerida no seu Catálogo de Serviços Azure, deve:

* Criar um modelo que define os recursos a implementar com a aplicação gerida.
* Defina os elementos de interface de utilizador para o portal quando implementar a aplicação gerida.
* Crie um pacote .zip que contém os ficheiros de modelo que são precisos.
* Decida que utilizador, grupo ou aplicação precisa de aceder ao grupo de recursos na subscrição do utilizador.
* Crie a definição de aplicação gerida que aponta para o pacote .zip e pedidos de acesso para a identidade.

Para este artigo, a aplicação gerida tem apenas uma conta de armazenamento. Destina-se a ilustrar os passos para publicar uma aplicação gerida. Para obter exemplos completos, veja [Projetos de exemplo para aplicações geridas do Azure](sample-projects.md).

Os exemplos do PowerShell neste artigo requerem o Azure PowerShell 6.2 ou posterior. Se necessário, [atualize a versão](/powershell/azure/install-Az-ps).

## <a name="create-the-resource-template"></a>Criar o modelo de recurso

Cada definição de aplicação gerida inclui um ficheiro denominado **mainTemplate.json**. Nele, defina os recursos do Azure a implementar. O modelo é igual aos modelos normais do Resource Manager.

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
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Guarde o ficheiro mainTemplate.json.

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>Definindo a sua experiência de criação usando CreateUiDefinition.json

Como editor, define a sua experiência de criação utilizando o ficheiro **createUiDefinition.json** que gera a interface para utilizadores que criam aplicações geridas. Define como os utilizadores fornecem entrada para cada parâmetro utilizando elementos de [controlo,](create-uidefinition-elements.md) incluindo drop-downs, caixas de texto e caixas de palavra-passe.

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

Carregue o pacote para uma localização acessível a partir de onde pode ser utilizada. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Criar a definição da aplicação gerida

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Crie uma aplicação ou grupo de utilizador no Azure Active Directory

O passo seguinte consiste em selecionar um grupo de utilizadores ou aplicações para gerir os recursos em nome do cliente. Este grupo de utilizadores ou aplicação tem permissões no grupo de recursos gerido, de acordo com a função que é atribuída. A função pode ser qualquer função de Controlo de Acesso Baseado em Funções (RBAC) incorporada, como o Proprietário ou Contribuidor. Também pode dar uma permissão de utilizador individual para gerir os recursos, mas normalmente atribui esta permissão a um grupo de utilizadores. Para criar um novo grupo de utilizadores do Active Directory, veja [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

É preciso o ID de objeto do grupo de utilizadores a utilizar para gerir os recursos. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Obtenha o ID de definição de função

Em seguida, precisa do ID da definição da função da função incorporada RBAC a que pretende conceder acesso ao utilizador, grupo de utilizadores ou aplicação. Normalmente, utiliza a função de Proprietário ou Contribuidor ou Leitor. O comando seguinte mostra como obter o ID da definição da função da função Proprietário:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Criar a definição da aplicação gerida

Se ainda não tiver um grupo de recursos para armazenar a definição da aplicação gerida, crie uma agora:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Agora, crie o recurso de definição da aplicação gerida.

```powershell
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
1. Em **selecionar** a função de Fornecedor de Recursos de **Aparelhos** e selecioná-la.
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

Adicionámos uma nova propriedade chamada **storageAccountId** às propriedades da sua aplicaçãoDefinição e fornecemos id de conta de armazenamento que pretende armazenar a sua definição como seu valor:

Pode verificar se os ficheiros de definição de aplicação são guardados na sua conta de armazenamento fornecida num recipiente intitulado definições de **aplicações**.

> [!NOTE]
> Para maior segurança, pode criar uma definição de aplicações geridas armazená-lo numa conta de armazenamento Azure onde a [encriptação está ativada](../../storage/common/storage-service-encryption.md). Os conteúdos de definição são encriptados através das opções de encriptação da conta de armazenamento. Apenas os utilizadores com permissões para o ficheiro podem ver a definição no Catálogo de Serviços.

### <a name="make-sure-users-can-see-your-definition"></a>Confirmar que os utilizadores podem ver a definição

Tem acesso à definição de aplicação gerida, mas deve verificar se outros utilizadores na sua organização podem aceder à mesma. Conceda-lhes, pelo menos, a função de Leitor na definição. Estes podem ter herdado este nível de acesso através da subscrição ou do grupo de recursos. Para verificar quem tem acesso à definição e adicionar utilizadores ou grupos, veja [Utilizar o Controlo de Acesso Baseado em Função para gerir o acesso aos recursos da subscrição do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos seguintes

* Para publicar a aplicação gerida no Microsoft Azure Marketplace, veja [Aplicações geridas do Azure no Marketplace](publish-marketplace-app.md).
* Para implementar uma instância da aplicação gerida, veja [Implementar aplicação de serviço de catálogo através do portal do Azure](deploy-service-catalog-quickstart.md).
