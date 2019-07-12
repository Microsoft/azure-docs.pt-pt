---
title: Referências de Cofre de chaves - serviço de aplicações do Azure | Documentos da Microsoft
description: Guia de referência e a configuração conceitual de referências de Cofre de chaves do Azure no App Service do Azure e as funções do Azure
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e7a049c8def0a5014aeb8a0e7a16aaa8def28009
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705706"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Utilizar referências de Key Vault para o serviço de aplicações e as funções do Azure (pré-visualização)

> [!NOTE] 
> Referências de Cofre de chaves estão atualmente em pré-visualização.

Este tópico mostra como trabalhar com segredos no Azure Key Vault em seu aplicativo de serviço de aplicações ou funções do Azure, sem a necessidade de alterações de código. [O Azure Key Vault](../key-vault/key-vault-overview.md) é um serviço que fornece gerenciamento centralizado de segredos, com controlo total sobre o histórico de auditoria e políticas de acesso.

## <a name="granting-your-app-access-to-key-vault"></a>Conceder o acesso a aplicações para o Key Vault

Para ler os segredos do Key Vault, terá de ter um cofre que criou e conceder permissão de aplicação para aceder ao mesmo.

1. Criar um cofre de chaves ao seguir a [início rápido do Key Vault](../key-vault/quick-create-cli.md).

1. Criar uma [atribuído ao sistema de identidade gerido](overview-managed-identity.md) para a sua aplicação.

   > [!NOTE] 
   > Cofre de chaves faz referência atualmente atribuído apenas sistema de suporte de identidades geridas. Não não possível utilizar as identidiades atribuídas.

1. Criar uma [política no Cofre de chaves de acesso](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) para a identidade de aplicação que criou anteriormente. Ative a permissão secreta "Get" nesta política. Não configure "aplicações autorizadas" ou `applicationId` definições, como isso não é compatível com uma identidade gerida.

    Conceder acesso a uma aplicação identidade no Cofre de chaves é uma operação única e permanecerá mesmo para todas as subscrições do Azure. Pode usá-lo para implementar certificados tantas quanto quiser. 

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência do Cofre de chaves é o formato `@Microsoft.KeyVault({referenceString})`, onde `{referenceString}` é substituído por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de caracteres de referência                                                            | Descrição                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | O **SecretUri** deve ser o URI de plano de dados completa de um segredo no Cofre de chaves, incluindo uma versão, por exemplo, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | O **VaultName** deve o nome do seu recurso do Key Vault. O **SecretName** deve ser o nome do segredo do destino. O **SecretVersion** deve ser a versão do segredo para utilizar. |

> [!NOTE] 
> Na pré-visualização atual, são necessárias versões. Quando alternar segredos, terá de atualizar a versão na configuração da sua aplicação.

Por exemplo, uma referência completa seria o seguinte aspeto:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Em alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Definições da aplicação de origem do Cofre de chaves

Referências do Key Vault podem ser utilizadas como valores para [as definições da aplicação](configure-common.md#configure-app-settings), permitindo-lhe manter os segredos no Cofre de chaves em vez de configuração do site. Definições da aplicação em segurança são encriptadas em inatividade, mas se precisar de capacidades de gestão de segredos, deve ir para o Cofre de chaves.

Para utilizar uma referência do Cofre de chaves para uma definição de aplicação, defina a referência como o valor da definição. A aplicação pode referenciar o segredo através da respetiva chave como habitualmente. Sem alterações de código são necessárias.

> [!TIP]
> A maioria das definições de aplicação com o Key Vault referências deve ser marcado como definições de ranhura, como deve ter cofres separados para cada ambiente.

### <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

Ao automatizar implementações de recursos através de modelos Azure Resource Manager, poderá sequenciar suas dependências numa ordem específica para tornar esta funcionalidade funcionar. Uma notável, terá de definir as definições da aplicação como seus próprios recursos, em vez de usar um `siteConfig` propriedade na definição do site. Isto acontece porque o site tem de ser definida em primeiro lugar, para que a identidade atribuída de sistema é criada com ele e pode ser utilizada na política de acesso.

Um exemplo psuedo-modelo para uma aplicação de funções poderá ter o seguinte aspeto:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Neste exemplo, a implementação de controlo de origem depende da existência de definições da aplicação. Este é o comportamento normalmente seguro, como a atualização de definição de aplicação se comporta de forma assíncrona. No entanto, uma vez que incluímos o `WEBSITE_ENABLE_SYNC_UPDATE_SITE` aplicação definir, a atualização é síncrona. Isso significa que a implementação de controlo de origem apenas começar assim que as definições da aplicação foi totalmente atualizadas.
