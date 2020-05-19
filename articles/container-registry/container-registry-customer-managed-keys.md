---
title: Encriptação em repouso com uma chave gerida pelo cliente
description: Saiba sobre encriptação no resto do seu registo de contentores Azure e como encriptar o seu registo com uma chave gerida pelo cliente armazenada no Cofre de Chaves Azure
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: d9cd10401e7f645a8edd269184a56dc27544a8c8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927326"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Criptografe o registo usando uma chave gerida pelo cliente

Quando armazena imagens e outros artefactos num registo de contentores Azure, o Azure encripta automaticamente o conteúdo do registo em repouso com [chaves geridas pelo serviço](../security/fundamentals/encryption-atrest.md#data-encryption-models). Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando uma chave que cria e gere no Cofre de Chaves Azure. Este artigo percorre-o através dos degraus utilizando o Azure CLI e o portal Azure.

A encriptação do lado do servidor com chaves geridas pelo cliente é suportada através da integração com [o Cofre de Chaves Azure](../key-vault/general/overview.md). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves, ou usar as APIs do Azure Key Vault para gerar chaves. Com o Azure Key Vault, também pode auditar o uso da chave.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites de serviço de registo, consulte o [Registo de Contentores Azure SKUs](container-registry-skus.md).

   
## <a name="things-to-know"></a>Aspetos importantes

* Atualmente, só pode ativar uma chave gerida pelo cliente quando criar um registo.
* Depois de ativar uma chave gerida pelo cliente num registo, não pode desativá-la.
* [A confiança](container-registry-content-trust.md) de conteúdo não é suportada atualmente num registo encriptado com uma chave gerida pelo cliente.
* Num registo encriptado com uma chave gerida pelo cliente, os registos de execução para [Tarefas ACR](container-registry-tasks-overview.md) são atualmente retidos por apenas 24 horas. Se precisar de reter registos por um período mais longo, consulte a orientação para [exportar e armazenar registos](container-registry-tasks-logs.md#alternative-log-storage)de execução de tarefas .

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os passos Do ClI Azure neste artigo, precisa da versão Azure CLI 2.2.0 ou posterior. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Ativar a chave gerida pelo cliente - CLI

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

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

Na saída do comando, tome nota dos seguintes valores: `id` e `principalId` . Precisa destes valores em etapas posteriores para configurar o acesso ao registo ao cofre chave.

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

Na saída de comando, tome nota da identificação da chave, `kid` . Usa este ID no próximo passo:

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
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um registo com a chave gerida pelo cliente

Executar o [az acr criar][az-acr-create] comando para criar um registo no nível de serviço Premium e ativar a chave gerida pelo cliente. Passe o ID principal de identidade gerido e o ID chave, armazenado anteriormente em variáveis ambientais:

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

A saída é semelhante a:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Ativar a chave gerida pelo cliente - portal

### <a name="create-a-managed-identity"></a>Criar uma identidade gerida

Crie uma identidade gerida atribuída ao utilizador [para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal Azure. Para passos, consulte [Criar uma identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Usa o nome da identidade em passos posteriores.

![Criar identidade gerida atribuída ao utilizador no portal Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para obter passos para criar um cofre chave, consulte [Quickstart: set and retrieve a secret from Azure Key Vault usando o portal Azure](../key-vault/secrets/quick-create-portal.md).

Ao criar um cofre chave para uma chave gerida pelo cliente, no separador **Basics,** ative as seguintes definições de proteção: **Eliminação suave** e **proteção de purga**. Estas definições ajudam a prevenir a perda de dados causada por eliminações acidentais de chave ou chave do cofre.

![Criar cofre chave no portal Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Adicionar a política de acesso ao cofre chave

Configure uma política para o cofre chave para que a identidade possa acessá-la.

1. Navegue para o seu cofre chave.
1. Selecione **Definições**  >  **As políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **permissões chave**, e selecione **Obter,** **desembrulhar a chave**e embrulhar a **tecla**.
1. **Selecione o principal e** selecione o nome de recurso da sua identidade gerida atribuída pelo utilizador.  
1. **Selecione Adicionar**e, em seguida, selecione **Guardar**.

![Criar a política de acesso ao cofre chave](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Criar chave

1. Navegue para o seu cofre chave.
1. Selecione **Settings**  >  **Definições .**
1. Selecione **+Generate/Import** e introduza um nome único para a chave.
1. Aceite os valores predefinidos restantes e selecione **Criar**.
1. Após a criação, selecione a tecla e tome nota da versão chave atual.

### <a name="create-azure-container-registry"></a>Criar um registo de contentor do Azure

1. Selecione **Criar um**registo de  >  **Containers**  >  **contentores**de recursos .
1. No separador **Basics,** selecione ou crie um grupo de recursos e introduza um nome de registo. No **SKU,** selecione **Premium**.
1. No **separador Encriptação,** na **tecla gerida pelo Cliente,** selecione **Ativado**.
1. Em **Identidade,** selecione a identidade gerida que criou.
1. Na **encriptação,** **selecione selecione a partir do Cofre de Chaves**.
1. Na tecla Select da janela **Azure Key Vault,** selecione o cofre, a chave e a versão que criou na secção anterior.
1. No separador **Encriptação,** selecione **Review + criar**.
1. Selecione **Criar** para implementar a instância de registo.

![Criar registo de contentores no portal Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Para ver o estado de encriptação do seu registo no portal, navegue para o seu registo. Em **Definições,** **selecione Encriptação**.

## <a name="enable-customer-managed-key---template"></a>Ativar a chave gerida pelo cliente - modelo

Também pode usar um modelo de Gestor de Recursos para criar um registo e ativar a encriptação com uma chave gerida pelo cliente. 

O modelo seguinte cria um novo registo de contentores e uma identidade gerida atribuída pelo utilizador. Copie o seguinte conteúdo para um novo ficheiro e guarde-o utilizando um nome de ficheiro como `CMKtemplate.json` .

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

Para mostrar o estado da encriptação do registo, execute o comando de [encriptação az acr:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="use-the-registry"></a>Use o registo

Depois de ativar uma chave gerida pelo cliente num registo, pode realizar as mesmas operações de registo que realiza num registo que não está encriptado com uma chave gerida pelo cliente. Por exemplo, pode autenticar com o registo e empurrar as imagens do Docker. Consulte comandos de exemplo em [Push e puxe uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Chave rotativa

Rode uma chave gerida pelo cliente utilizada para encriptação de registo seletiva para as suas políticas de conformidade. Crie uma nova tecla, ou atualize uma versão chave e, em seguida, atualize o registo para encriptar os dados usando a chave. Pode executar estes passos utilizando o Azure CLI ou no portal.

Ao rodar uma tecla, normalmente especifica a mesma identidade utilizada na criação do registo. Opcionalmente, configure uma nova identidade atribuída ao utilizador para acesso chave, ou ative e especifique a identidade atribuída ao sistema do registo.

> [!NOTE]
> Certifique-se de que a política de acesso ao cofre de [chaves](#add-key-vault-access-policy) necessária está definida para a identidade que configura para o acesso à chave. 

### <a name="azure-cli"></a>CLI do Azure

Utilize comandos [chave az keyvault][az-keyvault-key] para criar ou gerir as chaves do cofre chave. Por exemplo, para criar uma nova versão ou chave chave, executar a [chave az keyvault criar][az-keyvault-key-create] comando:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name> 

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name> 
```

Em seguida, executar o comando rotativo de [encriptação az acr,][az-acr-encryption-rotate-key] passando a nova chave ID e a identidade que pretende configurar:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>Portal

Utilize as definições de **encriptação** do registo para atualizar a versão chave, chave, cofre de chaves ou definições de identidade utilizadas para a chave gerida pelo cliente. 

Por exemplo, para gerar e configurar uma nova versão chave:

1. No portal, navegue para o seu registo. 
1. Em **Definições,** selecione chave de alteração **de encriptação**  >  **Change key**.
1. **Selecione a chave Selecione**
    
    ![Chave rotativa no portal Azure](./media/container-registry-customer-managed-keys/rotate-key.png)
1. Na tecla Select da janela **Azure Key Vault,** selecione o cofre e a chave que configuraprevia anteriormente e, em **Versão,** selecione **Criar novo**.
1. Na **Janela Criar uma janela-chave,** selecione **Generate**, e depois **Criar**.
1. Complete a seleção da chave e selecione **Guardar**.

## <a name="revoke-key"></a>Revogar a chave

Revogar a chave de encriptação gerida pelo cliente alterando a política de acesso no cofre chave ou apagando a chave. Por exemplo, utilize o comando [de az keyvault][az-keyvault-delete-policy] de exclusão de políticas para alterar a política de acesso da identidade gerida utilizada pelo seu registo:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Revogar a chave bloqueia efetivamente o acesso a todos os dados do registo, uma vez que o registo não consegue aceder à chave de encriptação. Se o acesso à chave estiver ativado ou a chave eliminada for restabelecida, o seu registo escolherá a chave para que possa voltar a aceder aos dados de registo encriptados.

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="system-assigned-identity"></a>Identidade atribuída ao sistema

Pode configurar a identidade gerida atribuída pelo sistema de um registo para aceder ao cofre chave para chaves de encriptação. Se não está familiarizado com as diferentes identidades geridas para os recursos Azure, consulte a [visão geral.](../active-directory/managed-identities-azure-resources/overview.md)

Para permitir a identidade atribuída ao sistema do registo no portal:

1. No portal, navegue para o seu registo. 
1. Selecione **Settings**  >   **Definições Identidade**.
1. No **sistema atribuído,** coloque **o Estado** **ligado**. Selecione **Guardar**.
1. Copie a **identificação** do objeto da identidade.

Para conceder o acesso de identidade ao seu cofre chave:

1. Navegue para o seu cofre chave.
1. Selecione **Definições**  >  **As políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **permissões chave**, e selecione **Obter,** **desembrulhar a chave**e embrulhar a **tecla**.
1. **Selecione** o principal e procure o ID do objeto da sua identidade gerida atribuída pelo sistema ou o nome do seu registo.  
1. **Selecione Adicionar**e, em seguida, selecione **Guardar**.

Para atualizar as definições de encriptação do registo para utilizar a identidade:

1. No portal, navegue para o seu registo. 
1. Em **Definições,** selecione chave de alteração **de encriptação**  >  **Change key**.
1. No **Identidade,** selecione **Sistema atribuído**e selecione **Guardar**.

### <a name="key-vault-firewall"></a>Firewall do cofre da chave

Se o seu cofre de chaves Azure for implantado numa rede virtual com uma firewall Key Vault, execute os seguintes passos:

1. Configure a encriptação do registo para usar a identidade atribuída ao sistema do registo. Consulte a secção anterior.
2. Configure o cofre da chave para permitir o acesso por qualquer [serviço de confiança](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). 

Para obter passos detalhados, consulte as firewalls do [Cofre chave Configure Azure e as redes virtuais](../key-vault/general/network-security.md). 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [encriptação em repouso em Azure.](../security/fundamentals/encryption-atrest.md)
* Saiba mais sobre as políticas de acesso e como [garantir o acesso a um cofre chave.](../key-vault/general/secure-your-key-vault.md)


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
