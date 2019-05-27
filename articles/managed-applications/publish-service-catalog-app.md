---
title: Criar e publicar uma aplicação gerida do catálogo de serviço do Azure | Microsoft Docs
description: Mostra como criar uma aplicação gerida do Azure que se destina aos membros da sua organização.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: dc86943924cd0c47c465e9d3bac4ca91b73a3ff5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171561"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Criar e publicar uma definição da aplicação gerida

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode criar e publicar [aplicações geridas](overview.md) do Azure que se destinam aos membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que cumprem normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, e não no Azure marketplace.

Para publicar uma aplicação gerida no catálogo de serviços, tem de:

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

## <a name="create-the-user-interface-definition"></a>Criar a definição de interface de utilizador

O portal do Azure utiliza o ficheiro **createUiDefinition.json** para gerar a interface de utilizador para os utilizadores que criem a aplicação gerida. O utilizador define a forma como os utilizadores concedem entrada para cada parâmetro. Pode utilizar opções como uma lista pendente, caixa de texto, caixa de palavra-passe e outras ferramentas de entrada. Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, veja [Introdução a CreateUiDefinition](create-uidefinition-overview.md).

Crie um ficheiro denominado **createUiDefinition.json**. O nome é sensível a maiúsculas e minúsculas.

Adicione o seguinte JSON ao ficheiro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
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

Guarde o ficheiro createUiDefinition.json.

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

O passo seguinte consiste em selecionar um grupo de utilizadores ou aplicações para gerir os recursos em nome do cliente. Este grupo de utilizadores ou aplicação tem permissões no grupo de recursos gerido, de acordo com a função que é atribuída. A função pode ser qualquer função de Controlo de Acesso Baseado em Funções (RBAC) incorporada, como o Proprietário ou Contribuidor. Também pode dar uma permissão de utilizador individual para gerir os recursos, mas normalmente atribui esta permissão a um grupo de utilizadores. Para criar um novo grupo de utilizadores do Active Directory, veja [Criar um grupo e adicionar membros no Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

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

### <a name="make-sure-users-can-see-your-definition"></a>Confirmar que os utilizadores podem ver a definição

Tem acesso à definição de aplicação gerida, mas deve verificar se outros utilizadores na sua organização podem aceder à mesma. Conceda-lhes, pelo menos, a função de Leitor na definição. Estes podem ter herdado este nível de acesso através da subscrição ou do grupo de recursos. Para verificar quem tem acesso à definição e adicionar utilizadores ou grupos, veja [Utilizar o Controlo de Acesso Baseado em Função para gerir o acesso aos recursos da subscrição do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passos Seguintes

* Para publicar a aplicação gerida no Microsoft Azure Marketplace, veja [Aplicações geridas do Azure no Marketplace](publish-marketplace-app.md).
* Para implementar uma instância da aplicação gerida, veja [Implementar aplicação de serviço de catálogo através do portal do Azure](deploy-service-catalog-quickstart.md).
