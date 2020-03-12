---
title: Encriptação em repouso com chaves geridas pelo cliente
description: Saiba sobre encriptação no resto do seu registo de contentores Azure e como encriptar o seu registo com uma chave gerida pelo cliente armazenada no Cofre de Chaves Azure
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 8bce77c776fe088e5c317f02cd2757738a287069
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096560"
---
# <a name="encryption-using-customer-managed-keys"></a>Encriptação usando chaves geridas pelo cliente

Quando armazena imagens e outros artefactos num registo de contentores Azure, o Azure encripta automaticamente o conteúdo do registo em repouso com [chaves geridas pelo serviço](../security/fundamentals/encryption-atrest.md#data-encryption-models). Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando uma chave que cria e gere no Cofre de Chaves Azure. Este artigo percorre-o através dos degraus utilizando o Azure CLI e o portal Azure.

A encriptação do lado do servidor com chaves geridas pelo cliente é suportada através da integração com [o Cofre de Chaves Azure](../key-vault/key-vault-overview.md). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves de encriptação. Com o Azure Key Vault, também pode auditar o uso da chave.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte o [Registo de Contentores Azure SKUs](container-registry-skus.md).

> [!IMPORTANT]
> Esta funcionalidade encontra-se atualmente em pré-visualização e aplicam-se [algumas limitações.](#preview-limitations) As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).
>
   
## <a name="preview-limitations"></a>Limitações de pré-visualização 

* Atualmente só é possível ativar esta funcionalidade quando criar um registo.
* Depois de ativar uma chave gerida pelo cliente num registo, não pode desativá-la.
* Num registo encriptado com uma chave gerida pelo cliente, os registos de execução para [Tarefas ACR](container-registry-tasks-overview.md) são atualmente retidos por apenas 24 horas. Se precisar de reter registos por um período mais longo, consulte a orientação para [exportar e armazenar registos](container-registry-tasks-logs.md#alternative-log-storage)de execução de tarefas .

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os passos Do ClI Azure neste artigo, precisa da versão Azure CLI 2.2.0 ou posterior. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Ativar a chave gerida pelo cliente - CLI

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Se necessário, o [grupo AZ crie][az-group-create] um comando para criar um grupo de recursos para criar o cofre chave, o registo de contentores e outros recursos necessários.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Criar uma identidade gerida atribuída ao utilizador [para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) com a [identidade az criar][az-identity-create] comando. Esta identidade será usada pelo seu registo para aceder ao serviço Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Na saída do comando, tome nota dos seguintes valores: `id` e `principalId`. Precisa destes valores em etapas posteriores para configurar o acesso ao registo ao cofre chave.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Para conveniência, guarde estes valores em variáveis ambientais:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Crie um cofre chave com [o cofre az criar][az-keyvault-create] para armazenar uma chave gerida pelo cliente para encriptação de registo. 

Para evitar a perda de dados causada por eliminações acidentais de chave ou chave do cofre, deve ativar as seguintes definições: **Eliminação suave** e **proteção de purga**. O exemplo seguinte inclui parâmetros para estas definições: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Adicionar a política de acesso ao cofre chave

Configure uma política para o cofre chave para que a identidade possa acessá-la. No seguinte comando de definição de [teclado az keyvault,][az-keyvault-set-policy] você passa a identificação principal da identidade gerida que criou, armazenada anteriormente numa variável ambiental. Detete as permissões de chave para **obter**, **desembrulharKey**, e **embrulharKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Criar chave e obter ID chave

Executar a [chave az keyvault criar][az-keyvault-key-create] comando para criar uma chave no cofre chave.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Na saída de comando, tome nota da identificação da chave, `kid`. Usa este ID no próximo passo:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Para conveniência, guarde este valor numa variável ambiental:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um registo com a chave gerida pelo cliente

Executar o [az acr criar][az-acr-create] comando para criar um registo e ativar a chave gerida pelo cliente. Passe o ID principal de identidade gerido e o ID chave, armazenado anteriormente em variáveis ambientais:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostrar estado de encriptação

Para mostrar se a encriptação do registo com uma chave gerida pelo cliente está ativada, execute o comando de [encriptação az acr:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Ativar a chave gerida pelo cliente - portal

### <a name="create-a-managed-identity"></a>Criar uma identidade gerida

Crie uma identidade gerida atribuída ao utilizador [para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal Azure. Para passos, consulte [Criar uma identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Tome nota do Nome de **Recurso** da identidade gerida. Precisa deste nome em passos posteriores.

![Criar identidade gerida atribuída ao utilizador no portal Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para obter passos para criar um cofre chave, consulte [Quickstart: set and retrieve a secret from Azure Key Vault usando o portal Azure](../key-vault/quick-create-portal.md).

Ao criar um cofre chave para uma chave gerida pelo cliente, no separador **Basics,** deve ativar as seguintes definições de proteção: **Eliminação suave** e **proteção de purga**. Estas definições ajudam a prevenir a perda de dados causada por eliminações acidentais de chave ou chave do cofre.

![Criar cofre chave no portal Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Adicionar a política de acesso ao cofre chave

Configure uma política para o cofre chave para que a identidade possa acessá-la.

1. Navegue para o seu cofre chave.
1. Selecione **Definições** > Políticas de **acesso > +Adicionar Política de Acesso**.
1. Selecione **permissões chave**, e selecione **Obter,** **desembrulhar a chave**e embrulhar a **tecla**.
1. **Selecione o principal e** selecione o nome de recurso da sua identidade gerida atribuída pelo utilizador.  
1. **Selecione Adicionar**e, em seguida, selecione **Guardar**.

![Criar a política de acesso ao cofre chave](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Criar chave

1. Navegue para o seu cofre chave.
1. Selecione **Definições** > **Teclas**.
1. Selecione **+Generate/Import** e introduza um nome único para a chave.
1. Aceite os valores predefinidos restantes e selecione **Criar**.
1. Após a criação, selecione a tecla e tome nota da versão chave atual.

### <a name="create-azure-container-registry"></a>Criar um registo de contentor do Azure

1. Selecione **Criar um recurso** > **Contentores** > **Container Registry**.
1. No separador **Basics,** selecione ou crie um grupo de recursos e introduza um nome de registo. No **SKU,** selecione **Premium**.
1. No **separador Encriptação,** na **tecla gerida pelo Cliente,** selecione **Ativado**.
1. Em **Identidade,** selecione a identidade gerida que criou.
1. Na **tecla de encriptação,** **selecione selecione a partir do Cofre de Chaves**.
1. Na tecla Select da janela **Azure Key Vault,** selecione o cofre, a chave e a versão que criou na secção anterior.
1. No separador **Encriptação,** selecione **Review + criar**.
1. Selecione **Criar** para implementar a instância de registo.

![Criar registo de contentores no portal Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Para ver o estado de encriptação do seu registo no portal, navegue para o seu registo. Em **Definições,** **selecione Encriptação (Pré-visualização)** .

## <a name="enable-customer-managed-key---template"></a>Ativar a chave gerida pelo cliente - modelo

Também pode usar um modelo de Gestor de Recursos para criar um registo e ativar a encriptação com uma chave gerida pelo cliente. 

O modelo seguinte cria um novo registo de contentores e uma identidade gerida atribuída pelo utilizador. Copie o seguinte conteúdo para um novo ficheiro e guarde-o utilizando um nome de ficheiro como `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Siga os passos nas secções anteriores para criar os seguintes recursos:

* Cofre chave, identificado pelo nome
* Chave do cofre chave, identificada por id chave

Executar a seguinte implementação do [grupo Az criar][az-group-deployment-create] comando para criar o registo usando o ficheiro de modelo anterior. Quando indicado, forneça um novo nome de registo e nome de identidade gerido, bem como o nome chave do cofre e id chave que criou. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Mostrar estado de encriptação

Para mostrar o estado da encriptação do registo, execute o comando [az acr encryption show-status][az-acr-encryption-show-status]:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Use o registo

Depois de ativar um registo para encriptar dados utilizando uma chave gerida pelo cliente, pode realizar as mesmas operações de registo que realiza num registo que não está encriptado com uma chave gerida pelo cliente. Por exemplo, pode autenticar com o registo e empurrar as imagens do Docker. Consulte comandos de exemplo em [Push e puxe uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Chave rotativa

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Crie uma nova tecla e, em seguida, atualize o registo para encriptar os dados utilizando a nova chave. Pode executar estes passos utilizando o Azure CLI ou no portal.

Por exemplo, executar a [chave az keyvault criar][az-keyvault-key-create] comando para criar uma nova chave:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Em seguida, executar o comando rotativo de [encriptação az acr,][az-acr-encryption-rotate-key] passando o novo ID chave e o ID principal da identidade gerida que configurava anteriormente:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Revogar a chave

Revogar a chave de encriptação gerida pelo cliente alterando a política de acesso no cofre chave ou apagando a chave. Por exemplo, utilize o comando [de az keyvault][az-keyvault-delete-policy] de exclusão de políticas para alterar a política de acesso da identidade gerida utilizada pelo seu registo. Por exemplo:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Revogar a chave bloqueia efetivamente o acesso a todos os dados do registo, uma vez que o registo não consegue aceder à chave de encriptação. Se o acesso à chave estiver ativado ou a chave eliminada for restabelecida, o seu registo escolherá a chave para que possa voltar a aceder aos dados de registo encriptados.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [encriptação em repouso em Azure.](../security/fundamentals/encryption-atrest.md)
* Saiba mais sobre as políticas de acesso e como [garantir o acesso a um cofre chave.](../key-vault/key-vault-secure-your-key-vault.md)
* Para fornecer feedback sobre as chaves geridas pelo cliente para o Registo de Contentores Azure, visite o [site aCR GitHub](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/keyt#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault/keyt#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault/keyt#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
