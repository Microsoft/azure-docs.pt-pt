---
title: Registo de encriptação com uma chave gerida pelo cliente
description: Saiba mais sobre encriptação no resto do seu registo de contentores Azure e como encriptar o seu registo Premium com uma chave gerida pelo cliente armazenada no Cofre de Chaves Azure
ms.topic: article
ms.date: 09/30/2020
ms.custom: ''
ms.openlocfilehash: ad81a94910cb1ed09634801f8706182e17947225
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842571"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Registo encriptado usando uma chave gerida pelo cliente

Quando armazena imagens e outros artefactos num registo de contentores Azure, o Azure encripta automaticamente o conteúdo do registo em repouso com [as teclas geridas pelo serviço](../security/fundamentals/encryption-models.md). Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando uma chave que cria e gere no Cofre de Chaves Azure (uma chave gerida pelo cliente). Este artigo acompanha-o através dos degraus usando o CLI Azure e o portal Azure.

A encriptação do lado do servidor com as chaves geridas pelo cliente é suportada através da integração com [o Azure Key Vault](../key-vault/general/overview.md). Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves ou utilizar as APIs do Azure Key Vault para gerar chaves. Com o Azure Key Vault, também pode auditar o uso da chave.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md).


## <a name="things-to-know"></a>Aspetos importantes

* Atualmente só pode ativar uma chave gerida pelo cliente quando criar um registo. Ao ativar a chave, configura uma identidade gerida *atribuída pelo utilizador* para aceder ao cofre da chave.
* Depois de ativar a encriptação com uma chave gerida pelo cliente num registo, não é possível desativar a encriptação.  
* [A confiança dos conteúdos](container-registry-content-trust.md) não é suportada num registo encriptado com uma chave gerida pelo cliente.
* Num registo encriptado com uma chave gerida pelo cliente, os registos de execução [de Tarefas ACR](container-registry-tasks-overview.md) são atualmente mantidos por apenas 24 horas. Se precisar de reter registos por um período mais longo, consulte orientações para [exportar e armazenar registos de execução de tarefas](container-registry-tasks-logs.md#alternative-log-storage).


> [!NOTE]
> Se o acesso ao cofre da chave Azure for restringido utilizando uma rede virtual com uma [firewall Key Vault,](../key-vault/general/network-security.md)são necessários passos de configuração extra. Depois de criar o registo e permitir a chave gerida pelo cliente, configure o acesso à chave utilizando a identidade gerida *pelo sistema* do registo e configuure o registo para contornar a firewall do Cofre de Chaves. Siga primeiro os passos deste artigo para permitir a encriptação com uma chave gerida pelo cliente e, em seguida, consulte a orientação para [o cenário Avançado: Firewall Key Vault](#advanced-scenario-key-vault-firewall) mais tarde neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os passos do Azure CLI neste artigo, precisa da versão 2.2.0 ou posterior do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Ativar a chave gerida pelo cliente - CLI

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, executar o [grupo az criar][az-group-create] comando para criar um grupo de recursos para a criação do cofre chave, registo de contentores e outros recursos necessários.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Crie uma identidade gerida atribuída ao utilizador [para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) com o comando [de criação de identidade az.][az-identity-create] Esta identidade será usada pelo seu registo para aceder ao serviço Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Na saída do comando, tome nota dos seguintes valores: `id` e `principalId` . Você precisa destes valores em passos posteriores para configurar o acesso do registo ao cofre chave.

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

Por conveniência, guarde estes valores em variáveis ambientais:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Crie um cofre chave com [chave-chave criar][az-keyvault-create] para armazenar uma chave gerida pelo cliente para encriptação de registo.

Para evitar a perda de dados causada por eliminações acidentais da chave ou do cofre, ative as seguintes definições: **Proteção de eliminação** e **purga** suave . O exemplo a seguir inclui parâmetros para estas definições:

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

> [!NOTE]
> A partir da versão 2.2 do Azure CLI, `az keyvault create` permite a eliminação suave por padrão.

Para ser utilizado em etapas posteriores, obtenha o ID de recursos do cofre chave:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Permitir o acesso ao cofre de chaves

Configure uma política para o cofre chave para que a identidade possa acessá-lo. No seguinte comando [de definição de keyvault az,][az-keyvault-set-policy] você passa o ID principal da identidade gerida que criou, armazenado anteriormente numa variável ambiental. Desembrulhe as permissões de chave para **obter,** **desembrulhar oKey** e **embrulhar oKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Em alternativa, utilize [o Azure RBAC para Key Vault](../key-vault/general/rbac-guide.md) (pré-visualização) para atribuir permissões à identidade para aceder ao cofre da chave. Por exemplo, atribua o papel de encriptação do serviço crypto do cofre de chave à identidade utilizando o comando [de criação de função az:](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption (preview)" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Crie a chave e obtenha iD chave

Executar a [chave az keyvault criar][az-keyvault-key-create] comando para criar uma chave no cofre da chave.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Na saída do comando, tome nota da identificação da `kid` chave, . Usa esta iD no próximo passo:

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
Por conveniência, guarde este valor numa variável ambiental:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um registo com chave gerida pelo cliente

Executar o [comando az acr criar][az-acr-create] para criar um registo no nível de serviço Premium e ativar a chave gerida pelo cliente. Passe o ID principal de identidade gerido e o ID chave, armazenado anteriormente em variáveis ambientais:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostrar estado de encriptação

Para mostrar se a encriptação do registo com uma chave gerida pelo cliente está ativada, execute o comando [do programa de encriptação az acr:][az-acr-encryption-show]

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

Crie uma identidade gerida atribuída ao utilizador [para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal Azure. Para etapas, consulte [Criar uma identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Usa o nome da identidade em passos posteriores.

![Criar identidade gerida atribuída ao utilizador no portal Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para obter passos para criar um cofre de chaves, consulte [Quickstart: Crie um Cofre de Chaves Azure com o portal Azure](../key-vault/general/quick-create-portal.md).

Ao criar um cofre-chave para uma chave gerida pelo cliente, no **separador Básicos,** ative as seguintes definições de proteção: **Proteção de eliminação** suave e **purga**. Estas definições ajudam a prevenir a perda de dados causada por eliminações acidentais da chave ou do cofre.

![Criar cofre chave no portal Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="enable-key-vault-access"></a>Permitir o acesso ao cofre de chaves

Configure uma política para o cofre chave para que a identidade possa acessá-lo.

1. Navegue para o cofre da chave.
1. Selecione **Definições**  >  **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **as permissões de chave**, e selecione **Get**, **Desembrulhar a tecla** e **a tecla wrap**.
1. **Selecione o principal** e selecione o nome de recurso da sua identidade gerida atribuída pelo utilizador.  
1. **Selecione Adicionar** e, em seguida, selecione **Guardar**.

![Criar política de acesso ao cofre chave](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

 Em alternativa, utilize [o Azure RBAC para Key Vault](../key-vault/general/rbac-guide.md) (pré-visualização) para atribuir permissões à identidade para aceder ao cofre da chave. Por exemplo, atribua o papel de encriptação do Serviço Crypto do Cofre de Chaves à identidade.

1. Navegue para o cofre da chave.
1. Selecione **Access control (IAM)**  >  **+Add**  >  **Add role assignment**.
1. Na janela **de atribuição de funções Adicionar:**
    1. Selecione o papel **de encriptação do serviço crypto do cofre de chaves (pré-visualização).** 
    1. Atribuir acesso à **identidade gerida atribuída pelo Utilizador.**
    1. Selecione o nome de recurso da sua identidade gerida atribuída pelo utilizador e selecione **Save**.

### <a name="create-key"></a>Criar chave

1. Navegue para o cofre da chave.
1. Selecione **Settings**  >  **Teclas de definições**.
1. Selecione **+Gerar/Importar** e introduza um nome único para a chave.
1. Aceite os valores predefinidos restantes e **selecione Criar**.
1. Após a criação, selecione a chave e tome nota da versão chave atual.

### <a name="create-azure-container-registry"></a>Criar um registo de contentor do Azure

1. **Selecione Criar um** registo  >  **de contentores de**  >  **Container Registry** recursos.
1. No **separador Básicos,** selecione ou crie um grupo de recursos e introduza um nome de registo. No **SKU,** selecione **Premium**.
1. No **separador Encriptação,** na **tecla gerida pelo Cliente,** selecione **Enabled**.
1. Em **Identidade,** selecione a identidade gerida que criou.
1. Na **encriptação**, **selecione Select from Key Vault**.
1. Na **tecla Select a partir da janela Azure Key Vault,** selecione o cofre, a chave e a versão que criou na secção anterior.
1. No **separador Encriptação,** selecione **'Rever + criar' (Análise+'**
1. Selecione **Criar** para implementar a instância de registo.

![Criar registo de contentores no portal Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Para ver o estado de encriptação do seu registo no portal, navegue para o seu registo. Em **Definições**, selecione  **Encriptação**.

## <a name="enable-customer-managed-key---template"></a>Ativar a chave gerida pelo cliente - modelo

Também pode utilizar um modelo de Gestor de Recursos para criar um registo e ativar a encriptação com uma chave gerida pelo cliente.

O modelo a seguir cria um novo registo de contentores e uma identidade gerida atribuída pelo utilizador. Copie o seguinte conteúdo para um novo ficheiro e guarde-o utilizando um nome de ficheiro como `CMKtemplate.json` .

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

* Cofre de chaves, identificado pelo nome
* Chave do cofre, identificada pela chave ID

Executar a [implementação do grupo az][az-group-deployment-create] seguinte criar comando para criar o registo usando o ficheiro de modelo anterior. Quando indicado, forneça um novo nome de registo e nome de identidade gerido, bem como o nome do cofre chave e iD chave que criou.

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

Para mostrar o estado da encriptação do registo, executar o comando [do programa de encriptação az acr:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Use o registo

Depois de ativar uma chave gerida pelo cliente num registo, pode efetuar as mesmas operações de registo que executa num registo que não está encriptado com uma chave gerida pelo cliente. Por exemplo, pode autenticar com o registo e empurrar as imagens do Docker. Veja os comandos de exemplo em [Push e puxe uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Chave rotativa

Rode uma chave gerida pelo cliente utilizada para encriptação de registo de acordo com as suas políticas de conformidade. Crie uma nova chave, ou atualize uma versão chave e, em seguida, atualize o registo para encriptar dados usando a chave. Pode executar estes passos utilizando o CLI Azure ou no portal.

Ao rodar uma chave, normalmente especifica a mesma identidade utilizada na criação do registo. Opcionalmente, configurar uma nova identidade atribuída ao utilizador para o acesso à chave, ou ativar e especificar a identidade atribuída ao sistema do registo.

> [!NOTE]
> Certifique-se de que o acesso ao cofre de [teclas](#enable-key-vault-access) necessário está definido para a identidade que configura para o acesso à chave.

### <a name="azure-cli"></a>CLI do Azure

Utilize comandos [de chave az keyvault][az-keyvault-key] para criar ou gerir as chaves do cofre. Por exemplo, para criar uma nova versão ou chave chave, executar a [chave az keyvault criar][az-keyvault-key-create] comando:

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

Em seguida, executar o comando de [chave de rotação az acr,][az-acr-encryption-rotate-key] passando o novo ID da chave e a identidade que pretende configurar:

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
1. Em **Definições**, selecione **a**  >  **tecla de alteração de** encriptação .
1. **Selecione a tecla**

    ![Rode a tecla no portal Azure](./media/container-registry-customer-managed-keys/rotate-key.png)
1. Na **tecla Select a partir da janela Azure Key Vault,** selecione o cofre e a chave que configuraste previamente, e em **Versão**, selecione **Criar novo**.
1. Na janela Criar uma janela **de chave,** selecione **Gerar** e, em seguida, **Criar**.
1. Complete a seleção de chaves e **selecione Guardar**.

## <a name="revoke-key"></a>Revogar a chave

Revogue a chave de encriptação gerida pelo cliente alterando a política de acesso ou permissões no cofre da chave ou eliminando a chave. Por exemplo, utilize o comando [az keyvault delete-policy][az-keyvault-delete-policy] para alterar a política de acesso da identidade gerida utilizada pelo seu registo:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Revogar a chave bloqueia eficazmente o acesso a todos os dados do registo, uma vez que o registo não consegue aceder à chave de encriptação. Se o acesso à chave estiver ativado ou a chave eliminada for restaurada, o seu registo escolherá a chave para que possa aceder novamente aos dados de registo encriptados.

## <a name="advanced-scenario-key-vault-firewall"></a>Cenário avançado: Firewall key Vault

Se o seu cofre de chaves Azure for implantado numa rede virtual com uma firewall Key Vault, execute os seguintes passos adicionais depois de permitir a encriptação de chaves gerida pelo cliente no seu registo.

1. Encriptação do registo para utilizar a identidade atribuída ao sistema do registo
1. Habilitar o registo a contornar a firewall do Cofre de Chaves
1. Rode a chave gerida pelo cliente

### <a name="configure-system-assigned-identity"></a>Configure identidade atribuída ao sistema

Pode configurar a identidade gerida atribuída pelo sistema de um registo para aceder ao cofre de chaves para chaves de encriptação. Se não está familiarizado com as diferentes identidades geridas para os recursos da Azure, consulte a [visão geral.](../active-directory/managed-identities-azure-resources/overview.md)

Para ativar a identidade atribuída ao sistema do registo no portal:

1. No portal, navegue para o seu registo.
1. Selecione **Settings**  >   **Definições Identidade**.
1. No **Sistema atribuído**, definir **estado** para **On**. Selecione **Guardar**.
1. Copie o **ID** do objeto da identidade.

Para conceder o acesso à identidade do seu cofre chave:

1. Navegue para o cofre da chave.
1. Selecione **Definições**  >  **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **as permissões de chave**, e selecione **Get**, **Desembrulhar a tecla** e **a tecla wrap**.
1. **Selecione o principal** e procure o ID do objeto da identidade gerida atribuída ao sistema ou o nome do seu registo.  
1. **Selecione Adicionar** e, em seguida, selecione **Guardar**.

Para atualizar as definições de encriptação do registo para utilizar a identidade:

1. No portal, navegue para o seu registo.
1. Em **Definições**, selecione **a**  >  **tecla de alteração de** encriptação .
1. Em **Identidade**, selecione **Sistema atribuído** e selecione **Save**.

### <a name="enable-key-vault-bypass"></a>Ativar o bypass do cofre de chaves

Para aceder a um cofre de chaves configurado com uma firewall key Vault, o registo deve contornar a firewall. Certifique-se de que o cofre da chave está configurado para permitir o acesso por qualquer [serviço de confiança](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). O Registo de Contentores Azure é um dos serviços de confiança.

1. No portal, navegue para o cofre da chave.
1. Selecione **Rede de Definições**  >  **Networking**.
1. Confirme, atualize ou adicione definições de rede virtuais. Para obter etapas detalhadas, consulte [as firewalls Configure Key Vault e redes virtuais](../key-vault/general/network-security.md).
1. Para **permitir que os Serviços Fidedignos da Microsoft contornem esta firewall**, selecione **Sim**. 

### <a name="rotate-the-customer-managed-key"></a>Rode a chave gerida pelo cliente

Depois de completar os passos anteriores, rode a chave para uma nova chave no cofre da chave atrás de uma firewall. Para etapas, consulte [a tecla Rotativa](#rotate-key) neste artigo.

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="removing-user-assigned-identity"></a>Remoção da identidade atribuída ao utilizador

Se tentar remover uma identidade atribuída ao utilizador de um registo que é utilizado para encriptação, poderá ver uma mensagem de erro semelhante a:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Também não poderá alterar (rodar) a chave de encriptação. Se ocorrer este problema, desatribua primeiro a identidade utilizando o GUID visualizado na mensagem de erro. Por exemplo:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Em seguida, depois de alterar a chave e atribuir uma identidade diferente, pode remover a identidade original atribuída ao utilizador.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [encriptação em repouso em Azure](../security/fundamentals/encryption-atrest.md).
* Saiba mais sobre as políticas de acesso e como [garantir o acesso a um cofre de chaves.](../key-vault/general/secure-your-key-vault.md)


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
