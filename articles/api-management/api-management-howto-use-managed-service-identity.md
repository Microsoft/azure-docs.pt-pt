---
title: Utilizar identidades geridas na gestão de API do Azure | Documentos da Microsoft
description: Saiba como utilizar identidades geridas na gestão de API
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: ebded5d1d58baf501ee5106d622162edc62d46ec
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310561"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Utilizar identidades geridas na gestão de API do Azure

Este artigo mostra-lhe como criar uma identidade gerida para uma instância de serviço de gestão de API e como aceder a outros recursos. Uma identidade gerida gerada pelo Azure Active Directory (Azure AD) permite que a sua instância de gestão de API para outros recursos do Azure protegido por AD, como o Azure Key Vault de aceder de forma fácil e segura. Esta identidade é gerida pelo Azure e não necessita de aprovisionar ou girar quaisquer segredos. Para obter mais informações sobre identidades geridas, consulte [o que há de identidades geridas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Criar uma identidade gerida para uma instância de gestão de API

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, irá criar primeiro uma instância de gestão de API como normal e, em seguida, ativar a funcionalidade.

1. Crie uma instância de gestão de API no portal, tal como faria normalmente. Navegue para o mesmo no portal.
2. Selecione **identidades de serviço geridas**.
3. Alternar Registre-se com o Azure Active Directory no. Clique em Guardar.

![Ativar o MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Usando o modelo Azure Resource Manager

Pode criar uma instância de gestão de API com uma identidade, incluindo a seguinte propriedade na definição do recurso:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Isso informa ao Azure para criar e gerir a identidade para a sua instância de gestão de API.

Por exemplo, um modelo Azure Resource Manager completo pode ser semelhante ao seguinte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
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
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Utilizar a identidade de serviço gerido para aceder a outros recursos

> [!NOTE]
> Atualmente, identidades geridas podem ser utilizadas para obter certificados do Azure Key Vault para nomes de domínio personalizado de gestão de API. Mais cenários serão suportados em breve.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Obter um certificado do Azure Key Vault

#### <a name="prerequisites"></a>Pré-requisitos
1. O Cofre de chaves que contém o certificado de pfx tem de estar na mesma subscrição do Azure e o mesmo grupo de recursos que o serviço de gestão de API. Este é um requisito do modelo do Azure Resource Manager.
2. O tipo de conteúdo do segredo do tem de ser *application/x-pkcs12*. Pode utilizar o seguinte script para carregar o certificado:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Se a versão de objeto do certificado não for fornecida, gestão de API irá obter automaticamente a versão mais recente do certificado após ser carregada para o Key Vault.

O exemplo seguinte mostra um modelo do Azure Resource Manager que contém os seguintes passos:

1. Crie uma instância de gestão de API com uma identidade gerida.
2. Atualizar as políticas de acesso de uma instância do Azure Key Vault e permitir que a instância de gestão de API obter segredos a partir do mesmo.
3. Atualize a instância de gestão de API, definindo um nome de domínio personalizado através de um certificado da instância do Key Vault.

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
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
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
        "apiVersion": "2017-03-01",
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

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre identidades geridas para recursos do Azure:

* [O que é identidades geridas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelos Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
