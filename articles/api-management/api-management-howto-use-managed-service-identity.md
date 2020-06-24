---
title: Utilizar identidades geridas na Azure API Management Microsoft Docs
description: Saiba como utilizar identidades geridas na Gestão da API
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 028b26537c9fe8a976dbc68a4776b2ea4101d811
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789441"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Utilizar identidades geridas na Azure API Management

Este artigo mostra-lhe como criar uma identidade gerida para um caso de Gestão de API e como aceder a outros recursos. Uma identidade gerida gerada pelo Azure Ative Directory (Azure AD) permite que a sua instância de Gestão API aceda de forma fácil e segura a outros recursos protegidos pela Azure, como o Azure Key Vault. Esta identidade é gerida pela Azure e não requer que forneça ou rode quaisquer segredos. Para obter mais informações sobre identidades geridas, consulte [o que é gerido para os recursos da Azure.](../active-directory/managed-identities-azure-resources/overview.md)

Um caso de Gestão da API pode ser concedido dois tipos de identidades:

- Uma **identidade atribuída ao sistema** está ligada ao seu serviço e é eliminada se o seu serviço for eliminado. O serviço só pode ter uma identidade atribuída ao sistema.
- Uma **identidade atribuída ao utilizador** é um recurso autónomo da Azure que pode ser atribuído ao seu serviço. O serviço pode ter várias identidades atribuídas ao utilizador(*).

## <a name="create-a-system-assigned-managed-identity-for-an-api-management-instance"></a>Criar uma identidade gerida atribuída pelo sistema para uma instância de Gestão da API

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, irá primeiro criar uma instância de Gestão API normal e, em seguida, ativar a funcionalidade.

1. Crie uma instância de Gestão API no portal como normalmente faria. Navegue até ele no portal.
2. Selecione **identidades geridas**.
3. Dentro do separador **Designado sistema,** **altere o Estado** para **ligar**. Clique em **Guardar**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Ativar a identidade gerida atribuída pelo sistema." border="true":::


### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os seguintes passos irão acompanhá-lo através da criação de uma instância de Gestão da API e atribuindo-lhe uma identidade usando a Azure PowerShell. 

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/install-az-ps)e, em seguida, corra `Connect-AzAccount` para criar uma ligação com a Azure.

2. Crie um caso de Gestão API utilizando a Azure PowerShell. Para obter mais exemplos de como utilizar a Azure PowerShell com instância de Gestão da API, consulte [as amostras da API Management PowerShell](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Atualizar e caso existente para criar a identidade:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="using-the-azure-resource-manager-template"></a>Usando o modelo de gestor de recursos Azure

Pode criar um caso de Gestão de API com uma identidade, incluindo a seguinte propriedade na definição de recurso:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Isto diz à Azure para criar e gerir a identidade para o seu caso de Gestão de API.

Por exemplo, um modelo completo do Gestor de Recursos Azure pode parecer o seguinte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Quando o caso é criado, tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A propriedade do inquilinoId identifica a que Azure AD inquilino a identidade pertence. O directorid é um identificador único para a identidade nova, por exemplo. Dentro da Azure AD, o diretor de serviço tem o mesmo nome que deu à sua instância de Gestão de API.


> [!NOTE]
> Uma instância de Gestão da API pode ter identidades atribuídas ao sistema e atribuídas ao mesmo tempo. Neste caso, a `type` propriedade seria`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Cenários Suportados

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Obtenha um certificado TLS/SSL personalizado para a instância de Gestão da API a partir do Cofre da Chave Azure
A identidade atribuída ao sistema de um serviço de Gestão API pode ser usada para recuperar certificados TLS/SSL personalizados armazenados no Cofre da Chave Azure. Estes certificados podem ser atribuídos a domínios personalizados em instância de Gestão da API.

1. O tipo de conteúdo do segredo deve ser *aplicação/x-pkcs12*.
2. O ponto final secreto do certificado Key Vault deve ser usado, que contém o segredo real.

> [!Important]
> Se a versão objeto do certificado não for fornecida, a API Management obterá automaticamente a versão mais recente do certificado depois de ser enviado para o Cofre-Chave dentro de 4 horas.

O exemplo a seguir mostra um modelo do Gestor de Recursos Azure que contém os seguintes passos:

1. Criar um caso de Gestão API com uma identidade gerida.
2. Atualize as políticas de acesso de uma instância do Azure Key Vault e permita que a instância de Gestão da API obtenha segredos a partir do mesmo.
3. Atualize a instância de Gestão da API definindo um nome de domínio personalizado através de um certificado a partir da instância Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

#### <a name="authenticate-using-api-management-identity-to-the-backend"></a>Autenticar usando identidade de gestão da API para o backend

A identidade atribuída ao sistema pode ser usada para autenticar o seu backend utilizando a política [de identidade gerida pela autenticação.](api-management-authentication-policies.md#ManagedIdentity)


## <a name="create-a-user-assigned-managed-identity-for-an-api-management-instance"></a>Criar uma identidade gerida atribuída ao utilizador para uma instância de Gestão da API

> [!NOTE]
> Um caso de Gestão API pode ser associado a até 10 identidade gerida atribuída pelo utilizador.

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, irá primeiro criar uma instância de Gestão API normal e, em seguida, ativar a funcionalidade.

1. Crie uma instância de Gestão API no portal como normalmente faria. Navegue até ele no portal.
2. Selecione **identidades geridas**.
3. Dentro do **separador Utilizador atribuído,** clique em **Adicionar**.
4. Procure a identidade que criou anteriormente e selecione-a. Clique em **Adicionar**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Ativar a identidade gerida atribuída pelo utilizador." border="true":::

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os seguintes passos irão acompanhá-lo através da criação de uma instância de Gestão da API e atribuindo-lhe uma identidade usando a Azure PowerShell. 

1. Se necessário, instale o Azure PowerShell utilizando as instruções encontradas no [guia Azure PowerShell](/powershell/azure/install-az-ps)e, em seguida, corra `Connect-AzAccount` para criar uma ligação com a Azure.

2. Crie um caso de Gestão API utilizando a Azure PowerShell. Para obter mais exemplos de como utilizar a Azure PowerShell com instância de Gestão da API, consulte [as amostras da API Management PowerShell](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Atualizar e serviço existente para atribuir uma identidade ao serviço.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="using-the-azure-resource-manager-template"></a>Usando o modelo de gestor de recursos Azure

Pode criar um caso de Gestão de API com uma identidade, incluindo a seguinte propriedade na definição de recurso:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

A adição do tipo atribuído ao utilizador diz ao Azure para utilizar a identidade atribuída ao utilizador especificada para o seu exemplo.

Por exemplo, um modelo completo do Gestor de Recursos Azure pode parecer o seguinte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Quando o serviço é criado, tem as seguintes propriedades adicionais:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

O directorid é um identificador único para a identidade que é usada para a administração da AD Azure. O clientId é um identificador único para a nova identidade da aplicação que é usada para especificar qual identidade usar durante chamadas de tempo de execução.

> [!NOTE]
> Uma instância de Gestão da API pode ter identidades atribuídas ao sistema e atribuídas ao mesmo tempo. Neste caso, a `type` propriedade seria`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Cenários Suportados

#### <a name="authenticate-using-user-assigned-identity-to-the-backend"></a>Autenticar usando a identidade atribuída pelo utilizador ao Backend

A identidade atribuída ao utilizador pode ser utilizada para autenticar o seu backend utilizando a política [de identidade gerida pela autenticação.](api-management-authentication-policies.md#ManagedIdentity)


## <a name="remove-an-identity"></a><a name="remove"></a>Remover uma identidade

Uma identidade atribuída ao sistema pode ser removida desativando a funcionalidade utilizando o portal ou o Modelo de Gestor de Recursos Azure da mesma forma que foi criado. As identidades atribuídas ao utilizador podem ser removidas individualmente. Para remover todas as identidades, desa um tipo de identidade para "Nenhum".

A remoção de uma identidade atribuída ao sistema desta forma também a eliminará do Azure AD. As identidades atribuídas ao sistema também são automaticamente removidas da Azure AD quando a instância de Gestão da API é eliminada.

Para remover todas as identidades usando o Modelo do Gestor de Recursos Azure, atualize esta secção:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Se uma instância de Gestão da API for configurada com certificado ssl personalizado da KeyVault e se tentar desativar a identidade gerida, o pedido é falhado.
> O cliente pode desbloquear-se mudando do certificado Azure Key Vault para fornecer certificado codificado inline e, em seguida, desativando a identidade gerida. Consulte o [Configure Custom Domain](configure-custom-domain.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre identidades geridas para recursos Azure:

* [O que é gerido identidades para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Autenticar com identidade gerida numa política](./api-management-authentication-policies.md#ManagedIdentity)
