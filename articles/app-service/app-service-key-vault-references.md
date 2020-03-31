---
title: Utilizar referências chave vault
description: Saiba como configurar o Azure App Service e as Funções Azure para utilizar referências do Cofre chave Azure. Disponibilize os segredos do Cofre chave para o seu código de aplicação.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7fdb7c980a278e2dcd4b64a4b70de50721d0b72a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280342"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Utilize referências chave vault para serviço de aplicações e funções azure

Este tópico mostra-lhe como trabalhar com segredos do Cofre de Chaves Azure na sua aplicação App Service ou Azure Functions sem exigir alterações de código. [Azure Key Vault](../key-vault/key-vault-overview.md) é um serviço que fornece gestão centralizada de segredos, com total controlo sobre políticas de acesso e histórico de auditoria.

## <a name="granting-your-app-access-to-key-vault"></a>Concedendo o acesso da sua aplicação ao Key Vault

Para ler segredos do Key Vault, precisa de ter um cofre criado e dar permissão à sua aplicação para aceder ao mesmo.

1. Crie um cofre chave seguindo o [quickstart do Key Vault](../key-vault/quick-create-cli.md).

1. Crie uma [identidade gerida atribuída](overview-managed-identity.md) ao sistema para a sua aplicação.

   > [!NOTE] 
   > Atualmente, as referências chave vault apenas suportam identidades geridas atribuídas pelo sistema. As identidades atribuídas ao utilizador não podem ser utilizadas.

1. Crie uma política de [acesso no Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) para a identidade de aplicação que criou anteriormente. Ative a permissão secreta "Obter" nesta política. Não configure a "aplicação `applicationId` autorizada" ou as definições, uma vez que esta não é compatível com uma identidade gerida.

    > [!NOTE]
    > As referências key Vault não são atualmente capazes de resolver segredos armazenados num cofre chave com [restrições](../key-vault/key-vault-overview-vnet-service-endpoints.md)de rede .

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência chave vault `@Microsoft.KeyVault({referenceString})`é `{referenceString}` do formulário, onde é substituído por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de referência                                                            | Descrição                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | O **SecretUri** deve ser o uri de um segredo em Key Vault, incluindo uma versão, por exemplo,https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | Nome do_cofre= nome do cofre;_ SecretName=_SecretName;_ SecretVersion=_secretVersion_ | O **Nome do Cofre** deve ser o nome do seu recurso Key Vault. O **Nome Secreto** deve ser o nome do segredo alvo. A **Versão Secreta** deve ser a versão do segredo a usar. |

Por exemplo, uma referência completa com versão seria como o seguinte:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Em alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Definições de aplicação de origem do cofre de chaves

As referências do Cofre chave podem ser usadas como valores para Definições de [Aplicação,](configure-common.md#configure-app-settings)permitindo-lhe guardar segredos no Cofre chave em vez do config do site. As definições de aplicação são encriptadas de forma segura em repouso, mas se precisar de capacidades de gestão secretas, devem entrar no Cofre chave.

Para utilizar uma referência do Cofre chave para uma definição de aplicação, defina a referência como o valor da definição. A sua aplicação pode fazer referência ao segredo através da sua chave como normal. Não são necessárias alterações de código.

> [!TIP]
> A maioria das definições de aplicação utilizando referências chave vault deve ser marcada como configurações de slot, uma vez que deve ter cofres separados para cada ambiente.

### <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

Ao automatizar as implementações de recursos através dos modelos do Gestor de Recursos Do Azure, poderá ser necessário sequenciar as suas dependências de uma determinada ordem para que esta funcionalidade funcione. De notar que terá de definir as definições da sua `siteConfig` aplicação como recurso próprio, em vez de utilizar uma propriedade na definição do site. Isto porque o site precisa de ser definido primeiro para que a identidade atribuída ao sistema seja criada com ele e possa ser usada na política de acesso.

Um modelo de psuedo exemplo para uma aplicação de função pode parecer o seguinte:

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
> Neste exemplo, a implementação do controlo de origem depende das definições de aplicação. Normalmente, este comportamento é normalmente inseguro, uma vez que a atualização de definição de aplicações se comporta assincronicamente. No entanto, como `WEBSITE_ENABLE_SYNC_UPDATE_SITE` incluímos a definição de aplicação, a atualização é sincronizada. Isto significa que a implementação do controlo de fonte só começará depois de as definições de aplicação terem sido totalmente atualizadas.

## <a name="troubleshooting-key-vault-references"></a>Referências do cofre de resolução de problemas

Se uma referência não for resolvida corretamente, o valor de referência será utilizado. Isto significa que, para as definições de aplicação, seria criada uma variável ambiental cujo valor tem a `@Microsoft.KeyVault(...)` sintaxe. Isto pode fazer com que a aplicação lance erros, uma vez que estava à espera de um segredo de uma determinada estrutura.

Mais comummente, isto deve-se a uma configuração errada da política de acesso do [Cofre Chave.](#granting-your-app-access-to-key-vault) No entanto, também pode ser devido a um segredo que já não existe ou a um erro de sintaxe na própria referência.

Se a sintaxe estiver correta, pode visualizar outras causas de erro verificando o estado de resolução atual no portal. Navegue para Definições de Aplicação e selecione "Editar" para a referência em questão. Abaixo da configuração de definição, deve ver informações de estado, incluindo quaisquer erros. A ausência destes implica que a sintaxe de referência é inválida.

Também pode usar um dos detetores incorporados para obter informações adicionais.

### <a name="using-the-detector-for-app-service"></a>Utilização do detetor para o Serviço de Aplicações

1. No portal, navegue para a sua aplicação.
2. Selecione **Diagnosticar e resolver problemas**.
3. Escolha **disponibilidade e desempenho** e selecione web app para **baixo.**
4. Encontre definições de **aplicação do cofre chave E** clique em mais **informações**.


### <a name="using-the-detector-for-azure-functions"></a>Utilização do detetor para funções azure

1. No portal, navegue para a sua aplicação.
2. Navegue para **as funcionalidades da Plataforma.**
3. Selecione **Diagnosticar e resolver problemas**.
4. Escolha **disponibilidade e desempenho** e selecione app **'Função' para baixo ou erros de reporte.**
5. Clique em Definições de **aplicação do cofre de chaves Diagnósticos.**
