---
title: Referências de Key Vault – serviço Azure App | Microsoft Docs
description: Guia de referência conceitual e de configuração para referências de Azure Key Vault no serviço Azure App e Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 30bd7c68ae1c88aba288b515d0ec32581f90b868
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088193"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Usar referências de Key Vault para o serviço de aplicativo e Azure Functions (visualização)

> [!NOTE] 
> Key Vault referências estão atualmente em visualização.

Este tópico mostra como trabalhar com segredos de Azure Key Vault em seu serviço de aplicativo ou Azure Functions aplicativo sem a necessidade de nenhuma alteração de código. [Azure Key Vault](../key-vault/key-vault-overview.md) é um serviço que fornece gerenciamento de segredos centralizado, com controle total sobre políticas de acesso e histórico de auditoria.

## <a name="granting-your-app-access-to-key-vault"></a>Concedendo acesso ao aplicativo para Key Vault

Para ler segredos de Key Vault, você precisa ter um cofre criado e dar permissão ao seu aplicativo para acessá-los.

1. Crie um cofre de chaves seguindo o guia de [início rápido do Key Vault](../key-vault/quick-create-cli.md).

1. Crie uma [identidade gerenciada atribuída pelo sistema](overview-managed-identity.md) para seu aplicativo.

   > [!NOTE] 
   > Key Vault referências atualmente só dão suporte a identidades gerenciadas atribuídas pelo sistema. Não é possível usar identidades atribuídas pelo usuário.

1. Crie uma [política de acesso no Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) para a identidade do aplicativo que você criou anteriormente. Habilite a permissão de segredo "Get" nessa política. Não configure o "aplicativo autorizado" ou `applicationId` as configurações, pois isso não é compatível com uma identidade gerenciada.

    Conceder acesso a uma identidade de aplicativo no Key Vault é uma operação OneTime e permanecerá o mesmo para todas as assinaturas do Azure. Você pode usá-lo para implantar quantos certificados desejar. 

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência de Key Vault é do formulário `@Microsoft.KeyVault({referenceString})`, onde `{referenceString}` é substituído por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de referência                                                            | Descrição                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | O **SecretUri** deve ser o URI completo do plano de dados de um segredo em Key Vault, incluindo uma versão, por exemplo, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | O **vaultname** deve ter o nome de seu Key Vault recurso. O **segredoname** deve ser o nome do segredo de destino. O **SecretVersion** deve ser a versão do segredo a ser usado. |

> [!NOTE] 
> Na visualização atual, as versões são necessárias. Ao girar segredos, você precisará atualizar a versão na configuração do aplicativo.

Por exemplo, uma referência completa seria semelhante ao seguinte:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Como alternativa

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Configurações do aplicativo de origem de Key Vault

Key Vault referências podem ser usadas como valores para [configurações do aplicativo](configure-common.md#configure-app-settings), permitindo que você mantenha os segredos em Key Vault em vez da configuração do site. As configurações do aplicativo são criptografadas com segurança em repouso, mas se você precisar de recursos de gerenciamento secreto, elas deverão entrar em Key Vault.

Para usar uma referência de Key Vault para uma configuração de aplicativo, defina a referência como o valor da configuração. Seu aplicativo pode referenciar o segredo por meio de sua chave normalmente. Nenhuma alteração de código é necessária.

> [!TIP]
> A maioria das configurações de aplicativo usando referências de Key Vault deve ser marcada como configurações de slot, pois você deve ter cofres separados para cada ambiente.

### <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

Ao automatizar implantações de recursos por meio de modelos de Azure Resource Manager, talvez seja necessário sequenciar suas dependências em uma ordem específica para fazer com que esse recurso funcione. De observação, você precisará definir as configurações do aplicativo como seu próprio recurso, em vez de usar `siteConfig` uma propriedade na definição do site. Isso ocorre porque o site precisa ser definido primeiro para que a identidade atribuída pelo sistema seja criada com ela e possa ser usada na política de acesso.

Um exemplo de psuedo-template para um aplicativo de funções pode ser semelhante ao seguinte:

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
> Neste exemplo, a implantação do controle do código-fonte depende das configurações do aplicativo. Esse comportamento normalmente é inseguro, pois a atualização da configuração do aplicativo se comporta de forma assíncrona. No entanto, como incluímos `WEBSITE_ENABLE_SYNC_UPDATE_SITE` a configuração do aplicativo, a atualização é síncrona. Isso significa que a implantação do controle do código-fonte só será iniciada quando as configurações do aplicativo tiverem sido totalmente atualizadas.
