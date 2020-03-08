---
title: Utilize identidades geridas na Gestão da API azure  Microsoft Docs
description: Saiba como usar identidades geridas na Gestão aPI
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
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380843"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Utilizar identidades geridas na Gestão da API Azure

Este artigo mostra-lhe como criar uma identidade gerida para uma instância de serviço de Gestão API e como aceder a outros recursos. Uma identidade gerida gerada pelo Azure Ative Directory (Azure AD) permite que a sua instância de Gestão API aceda de forma fácil e segura a outros recursos protegidos pela Azure AD, como o Azure Key Vault. Esta identidade é gerida pelo Azure e não exige que forneça ou gire quaisquer segredos. Para obter mais informações sobre identidades geridas, consulte [o que é gerida identidades para os recursos Do Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Criar uma identidade gerida para uma instância de Gestão API

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Para configurar uma identidade gerida no portal, primeiro criará uma instância de Gestão API normalmente e, em seguida, ativará a funcionalidade.

1. Crie uma instância de Gestão API no portal como normalmente faria. Navegue até ele no portal.
2. Selecione **identidades de serviço geridas**.
3. Switch Register com Diretório Ativo Azure para On. Clique em Guardar.

![Ativar msi](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Usando o modelo de Gestor de Recursos Azure

Pode criar uma instância de Gestão API com uma identidade, incluindo a seguinte propriedade na definição de recursos:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Isto diz ao Azure para criar e gerir a identidade para a sua instância de Gestão API.

Por exemplo, um modelo completo do Gestor de Recursos Azure pode parecer o seguinte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
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
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Utilize a identidade de serviço gerida para aceder a outros recursos

> [!NOTE]
> Atualmente, identidades geridas podem ser usadas para obter certificados do Azure Key Vault para nomes de domínio personalizados de Gestão API. Mais cenários serão apoiados em breve.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Obtenha um certificado do Cofre de Chaves Azure

#### <a name="prerequisites"></a>Pré-requisitos
1. O Cofre-Chave que contém o certificado pfx deve estar na mesma subscrição azure e no mesmo Grupo de Recursos que o serviço de Gestão API. Este é um requisito do modelo de Gestor de Recursos Azure.
2. O Tipo de Conteúdo do segredo deve ser *aplicação/x-pkcs12*. Pode utilizar o seguinte script para fazer o upload do certificado:

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
> Se a versão do objeto do certificado não for fornecida, a API Management obterá automaticamente a versão mais recente do certificado depois de ser enviado para o Key Vault.

O exemplo seguinte mostra um modelo de Gestor de Recursos Azure que contém os seguintes passos:

1. Criar uma instância de Gestão API com uma identidade gerida.
2. Atualize as políticas de acesso de uma instância do Cofre chave Azure e permita que a instância de Gestão da API obtenha segredos da sua parte.
3. Atualize a instância de Gestão API definindo um nome de domínio personalizado através de um certificado da instância Key Vault.

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre identidades geridas para os recursos Azure:

* [O que é gerida identidades para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Modelos de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates)
* [Autenticar com uma identidade gerida numa política](./api-management-authentication-policies.md#ManagedIdentity)
