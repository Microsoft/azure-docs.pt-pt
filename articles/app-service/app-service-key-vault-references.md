---
title: Utilizar as referências do Key Vault
description: Saiba como configurar o Azure App Service e as Funções Azure para utilizar referências Azure Key Vault. Disponibilize os segredos do Cofre chave para o seu código de aplicação.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 69fc0d6f3c4e18b34555a099f4e28e278ca3bdad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635392"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Utilize referências de Cofre de Chaves para Serviço de Aplicações e Funções Azure

Este tópico mostra-lhe como trabalhar com segredos do Azure Key Vault na sua aplicação App Service ou Azure Functions sem exigir alterações de código. [Azure Key Vault](../key-vault/general/overview.md) é um serviço que fornece gestão de segredos centralizados, com controlo total sobre políticas de acesso e histórico de auditoria.

## <a name="granting-your-app-access-to-key-vault"></a>Concedendo à sua aplicação acesso ao Key Vault

Para ler segredos do Key Vault, precisa de ter um cofre criado e dar permissão à sua aplicação para aceder ao mesmo.

1. Crie um cofre de chaves seguindo o [quickstart do Key Vault](../key-vault/secrets/quick-create-cli.md).

1. Crie uma [identidade gerida atribuída ao sistema](overview-managed-identity.md) para a sua aplicação.

   > [!NOTE] 
   > Atualmente, as referências do Cofre-Chave apenas suportam identidades geridas atribuídas pelo sistema. As identidades atribuídas ao utilizador não podem ser utilizadas.

1. Crie uma política de [acesso no Key Vault](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) para a identidade da aplicação que criou anteriormente. Ativar a permissão secreta "Obter" sobre esta política. Não configufique a "aplicação autorizada" ou `applicationId` as definições, uma vez que tal não é compatível com uma identidade gerida.

   > [!IMPORTANT]
   > As referências key Vault não são atualmente capazes de resolver segredos armazenados num cofre chave com restrições de [rede,](../key-vault/general/overview-vnet-service-endpoints.md) a menos que a aplicação esteja hospedada dentro de um [Ambiente de Serviço de Aplicações](./environment/intro.md).

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência do Cofre-Chave é do `@Microsoft.KeyVault({referenceString})` formulário, onde `{referenceString}` é substituída por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de referência                                                            | Description                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | O **SecretUri** deve ser o plano de dados completo URI de um segredo em Key Vault, opcionalmente incluindo uma versão, por exemplo, `https://myvault.vault.azure.net/secrets/mysecret/` ou `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | Nome do cofre=_nome do cofre;_ Nome secreto=_nome secreto;_ SecretVersion=_secretversion_ | O **Nome do Cofre** é necessário e se o nome do seu recurso Key Vault. O **Nome Secreto** é necessário e deve ser o nome do segredo-alvo. A **Versão Secreta** é opcional, mas se a presente indicar a versão do segredo a utilizar. |

Por exemplo, uma referência completa seria semelhante:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

Em alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Rotação

Se uma versão não for especificada na referência, então a aplicação utilizará a versão mais recente que existe no Key Vault. Quando as versões mais recentes estiverem disponíveis, como é o caso de um evento de rotação, a aplicação irá atualizar-se automaticamente e começar a utilizar a versão mais recente dentro de um dia. Quaisquer alterações de configuração feitas na aplicação causarão uma atualização imediata às versões mais recentes de todos os segredos referenciados.

## <a name="source-application-settings-from-key-vault"></a>Definições de aplicação de origem a partir do cofre de chaves

As referências do Cofre de Chaves podem ser usadas como valores para [configurações de aplicações,](configure-common.md#configure-app-settings)permitindo-lhe guardar segredos no Cofre de Chaves em vez do site config. As definições de aplicação são encriptadas de forma segura em repouso, mas se precisar de capacidades de gestão secretas, devem entrar no Key Vault.

Para utilizar uma referência do Cofre de Chaves para uma definição de aplicação, defina a referência como o valor da definição. A sua aplicação pode fazer referência ao segredo através da sua chave como normal. Não são necessárias alterações de código.

> [!TIP]
> A maioria das definições de aplicação que utilizam referências do Key Vault devem ser marcadas como definições de ranhuras, uma vez que deverá ter abóbadas separadas para cada ambiente.

### <a name="azure-resource-manager-deployment"></a>Implementação do Azure Resource Manager

Ao automatizar as implementações de recursos através dos modelos Azure Resource Manager, poderá necessitar de sequenciar as suas dependências de uma determinada forma de fazer com que esta funcionalidade funcione. De notar que terá de definir as definições da sua aplicação como recurso próprio, em vez de utilizar uma `siteConfig` propriedade na definição do site. Isto porque o site precisa de ser definido primeiro para que a identidade atribuída ao sistema seja criada com ele e possa ser usada na política de acesso.

Um modelo de pseudo-modelo de exemplo para uma aplicação de função pode parecer o seguinte:

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
> Neste exemplo, a implementação do controlo de fonte depende das definições da aplicação. Este comportamento é normalmente inseguro, uma vez que a atualização de definição de aplicações comporta-se de forma assíncronea. No entanto, como incluímos a definição da `WEBSITE_ENABLE_SYNC_UPDATE_SITE` aplicação, a atualização é sincronizada. Isto significa que a implementação do controlo de fonte só começará quando as definições da aplicação tiverem sido totalmente atualizadas.

## <a name="troubleshooting-key-vault-references"></a>Referências de cofre de chaves de resolução de problemas

Se uma referência não for resolvida corretamente, o valor de referência será utilizado. Isto significa que, para as definições de aplicações, seria criada uma variável ambiental cujo valor tem a `@Microsoft.KeyVault(...)` sintaxe. Isto pode fazer com que a aplicação lance erros, uma vez que esperava um segredo de uma determinada estrutura.

Mais comummente, isto deve-se a uma configuração errada da política de acesso ao [Cofre-Chave.](#granting-your-app-access-to-key-vault) No entanto, também pode ser devido a um segredo que já não existe ou a um erro de sintaxe na própria referência.

Se a sintaxe estiver correta, pode ver outras causas de erro verificando o estado de resolução atual no portal. Navegue para Definições de Aplicação e selecione "Editar" para a referência em questão. Abaixo da configuração de definição, deve ver informações de estado, incluindo quaisquer erros. A ausência destes implica que a sintaxe de referência é inválida.

Também pode usar um dos detetores incorporados para obter informações adicionais.

### <a name="using-the-detector-for-app-service"></a>Utilização do detetor para o Serviço de Aplicações

1. No portal, navegue para a sua aplicação.
2. Selecione **Diagnosticar e resolver problemas**.
3. Escolha **Disponibilidade e Desempenho** e selecione aplicativo Web para **baixo.**
4. Encontre configurações de **configurações de aplicação do cofre de chave e** clique em Mais **informações**.


### <a name="using-the-detector-for-azure-functions"></a>Utilização do detetor para funções Azure

1. No portal, navegue para a sua aplicação.
2. Navegue para **as funcionalidades da Plataforma.**
3. Selecione **Diagnosticar e resolver problemas**.
4. Escolha **Disponibilidade e Desempenho e** selecione a **aplicação 'Função' para baixo ou para reportar erros.**
5. Clique em **Diagnósticos de Definições de Aplicação do Cofre de Teclas.**
