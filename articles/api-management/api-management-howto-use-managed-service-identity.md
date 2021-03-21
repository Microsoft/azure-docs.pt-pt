---
title: Utilize identidades geridas na Azure API Management | Microsoft Docs
description: Saiba como criar identidades atribuídas ao sistema e atribuídas ao utilizador na Gestão API utilizando o portal Azure, o PowerShell e um modelo de Gestor de Recursos.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 98237efae89e7d88dd23cb7e8fc9f7e9f05bca70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521548"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Utilizar identidades geridas na Azure API Management

Este artigo mostra-lhe como criar uma identidade gerida para um caso de Gestão API Azure e como aceder a outros recursos. Uma identidade gerida gerada pelo Azure Ative Directory (Azure AD) permite que a sua instância de Gestão API aceda de forma fácil e segura a outros recursos protegidos pela Azure, como o Azure Key Vault. O Azure gere esta identidade, para que não tenhas de providenciar ou rodar segredos. Para obter mais informações sobre identidades geridas, veja [quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Pode conceder dois tipos de identidades a uma instância de Gestão da API:

- Uma *identidade atribuída ao sistema* está ligada ao seu serviço e é eliminada se o seu serviço for eliminado. O serviço pode ter apenas uma identidade atribuída ao sistema.
- Uma *identidade atribuída ao utilizador* é um recurso autónomo da Azure que pode ser atribuído ao seu serviço. O serviço pode ter várias identidades atribuídas ao utilizador.

## <a name="create-a-system-assigned-managed-identity"></a>Criar uma identidade gerida atribuída ao sistema

### <a name="azure-portal"></a>Portal do Azure

Para configurar uma identidade gerida no portal Azure, primeiro criará uma instância de Gestão API e depois ativará a funcionalidade.

1. Crie uma instância de Gestão API no portal como normalmente faria. Procure-o no portal.
2. Selecione **identidades geridas**.
3. No separador **System atribuído,** **altere o Estado** para **ligar**. Selecione **Guardar**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Seleções para permitir uma identidade gerida atribuída ao sistema" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os passos seguintes acompanham-no através da criação de uma instância de Gestão da API e atribuindo-lhe uma identidade utilizando o Azure PowerShell. 

1. Se necessário, instale a Azure PowerShell utilizando as instruções no [guia Azure PowerShell](/powershell/azure/install-az-ps). Em seguida, corra `Connect-AzAccount` para criar uma ligação com Azure.

2. Utilize o seguinte código para criar o caso. Para mais exemplos de como utilizar o Azure PowerShell com um exemplo de Gestão da API, consulte [as amostras da API Management PowerShell](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Atualizar um caso existente para criar a identidade:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

Pode criar um caso de Gestão de API com uma identidade, incluindo a seguinte propriedade na definição de recurso:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Esta propriedade diz à Azure para criar e gerir a identidade para o seu caso de Gestão de API.

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

A `tenantId` propriedade identifica a que inquilino da AD AZure a identidade pertence. A `principalId` propriedade é um identificador único para a nova identidade do exemplo. Dentro da Azure AD, o diretor de serviço tem o mesmo nome que deu à sua instância de Gestão de API.

> [!NOTE]
> Uma instância de Gestão da API pode ter identidades atribuídas ao sistema e atribuídas ao mesmo tempo. Neste caso, a `type` propriedade `SystemAssigned,UserAssigned` seria.

## <a name="supported-scenarios-using-system-assigned-identity"></a>Cenários suportados usando identidade atribuída ao sistema

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Obtenha um certificado TLS/SSL personalizado para a instância de Gestão da API a partir do Cofre da Chave Azure
Pode utilizar a identidade atribuída ao sistema de uma instância de Gestão da API para recuperar certificados TLS/SSL personalizados armazenados no Cofre da Chave Azure. Em seguida, pode atribuir estes certificados a domínios personalizados na instância de Gestão da API. Tenha em conta o seguinte:

- O tipo de conteúdo do segredo deve ser *aplicação/x-pkcs12*.
- Use o ponto final secreto do certificado Key Vault, que contém o segredo.

> [!Important]
> Se não fornecer a versão do objeto do certificado, a API Management obterá automaticamente a versão mais recente do certificado no prazo de quatro horas após a sua atualização no Key Vault.

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
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Autenticar até ao fim utilizando uma identidade de Gestão API

Pode utilizar a identidade atribuída ao sistema para autenticar até ao final através da política [de identidade gerida pela autenticação.](api-management-authentication-policies.md#ManagedIdentity)

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Conecte-se aos recursos do Azure por trás do IP Firewall utilizando a identidade gerida atribuída pelo sistema


A API Management é um serviço de confiança da Microsoft para os seguintes recursos. Isto permite que o serviço se conecte aos seguintes recursos por trás de uma firewall. Após atribuir explicitamente o papel azure adequado à [identidade gerida atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md) para esse caso de recurso, o âmbito de acesso, por exemplo, corresponde à função Azure atribuída à identidade gerida.


|Serviço Azure | Ligação|
|---|---|
|Armazenamento do Azure | [Armazenamento fidedigno de acesso a azul](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Service Bus do Azure | [Ônibus de serviço de acesso fidedigno a azul-azure](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Hub de Eventos do Azure | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

> [!NOTE]
> Pode associar uma instância de Gestão da API a até 10 identidades geridas atribuídas pelo utilizador.

### <a name="azure-portal"></a>Portal do Azure

Para configurar uma identidade gerida no portal, primeiro criará uma instância de Gestão API e depois ativará a funcionalidade.

1. Crie uma instância de Gestão API no portal como normalmente faria. Procure-o no portal.
2. Selecione **identidades geridas**.
3. No **separador Utilizador atribuído,** selecione **Adicionar**.
4. Procure a identidade que criou anteriormente e selecione-a. Selecione **Adicionar**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Seleções para permitir uma identidade gerida atribuída pelo utilizador" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os passos seguintes acompanham-no através da criação de uma instância de Gestão da API e atribuindo-lhe uma identidade utilizando o Azure PowerShell. 

1. Se necessário, instale o Azure PowerShell utilizando as instruções no [guia Azure PowerShell](/powershell/azure/install-az-ps). Em seguida, corra `Connect-AzAccount` para criar uma ligação com Azure.

2. Utilize o seguinte código para criar o caso. Para mais exemplos de como utilizar o Azure PowerShell com um exemplo de Gestão da API, consulte [as amostras da API Management PowerShell](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Atualizar um serviço existente para atribuir uma identidade ao serviço:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Modelo Azure Resource Manager

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

A `principalId` propriedade é um identificador único para a identidade que é usada para a administração da AD Azure. A `clientId` propriedade é um identificador único para a nova identidade da aplicação que é usada para especificar qual identidade usar durante chamadas de tempo de execução.

> [!NOTE]
> Uma instância de Gestão da API pode ter identidades atribuídas ao sistema e atribuídas ao mesmo tempo. Neste caso, a `type` propriedade `SystemAssigned,UserAssigned` seria.

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Cenários suportados utilizando identidade gerida atribuída ao utilizador

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Obtenha um certificado TLS/SSL personalizado para a instância de Gestão da API a partir do Cofre da Chave Azure
Pode utilizar qualquer identidade atribuída ao utilizador para estabelecer confiança entre uma instância de Gestão da API e KeyVault. Esta confiança pode então ser usada para recuperar certificados TLS/SSL personalizados armazenados no Cofre da Chave Azure. Em seguida, pode atribuir estes certificados a domínios personalizados na instância de Gestão da API. 

Tenha em conta o seguinte:

- O tipo de conteúdo do segredo deve ser *aplicação/x-pkcs12*.
- Use o ponto final secreto do certificado Key Vault, que contém o segredo.

> [!Important]
> Se não fornecer a versão do objeto do certificado, a API Management obterá automaticamente a versão mais recente do certificado no prazo de quatro horas após a sua atualização no Key Vault.

Para o modelo completo, consulte [a Gestão da API com O SSL baseado em KeyVault utilizando a identidade atribuída pelo utilizador.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)

Neste modelo, irá implementar:

* API Management do Azure
* Identidade atribuída ao utilizador gerido Azure
* Azure KeyVault para armazenar o certificado SSL/TLS

Para executar automaticamente a implementação, clique no seguinte botão:

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Autenticar até ao fim utilizando uma identidade atribuída ao utilizador

Pode utilizar a identidade atribuída ao utilizador para autenticar até ao final através da política [de identidade gerida pela autenticação.](api-management-authentication-policies.md#ManagedIdentity)

## <a name="remove-an-identity"></a><a name="remove"></a>Remover uma identidade

Pode remover uma identidade atribuída ao sistema desativando a funcionalidade através do portal ou do modelo Azure Resource Manager da mesma forma que foi criada. As identidades atribuídas ao utilizador podem ser removidas individualmente. Para remover todas as identidades, desa um tipo de identidade para `"None"` .

A remoção de uma identidade atribuída ao sistema desta forma também a eliminará do Azure AD. As identidades atribuídas ao sistema também são automaticamente removidas da Azure AD quando a instância de Gestão da API é eliminada.

Para remover todas as identidades utilizando o modelo Azure Resource Manager, atualize esta secção:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Se uma instância de Gestão da API estiver configurada com um certificado SSL personalizado da Key Vault e tentar desativar uma identidade gerida, o pedido falhará.
>
> Pode desbloquear-se mudando de um certificado Azure Key Vault para um certificado codificado inline e, em seguida, desativando a identidade gerida. Para obter mais informações, consulte [configurar um nome de domínio personalizado.](configure-custom-domain.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre identidades geridas para recursos Azure:

* [Quais são as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Autenticar com identidade gerida numa política](./api-management-authentication-policies.md#ManagedIdentity)
