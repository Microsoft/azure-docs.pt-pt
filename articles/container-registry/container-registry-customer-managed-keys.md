---
title: Registo de encriptação com uma chave gerida pelo cliente
description: Saiba mais sobre encriptação no resto do seu registo de contentores Azure e como encriptar o seu registo Premium com uma chave gerida pelo cliente armazenada no Cofre de Chaves Azure
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: aad9419fdb139ff615bfe07075be78a2ca4ee4ac
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489077"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Registo encriptado usando uma chave gerida pelo cliente

Quando armazena imagens e outros artefactos num registo de contentores Azure, o Azure encripta automaticamente o conteúdo do registo em repouso com [as teclas geridas pelo serviço](../security/fundamentals/encryption-models.md). Pode complementar a encriptação padrão com uma camada de encriptação adicional utilizando uma chave que cria e gere no Cofre de Chaves Azure (uma chave gerida pelo cliente). Este artigo percorre os passos utilizando o Azure CLI, o portal Azure ou um modelo de Gestor de Recursos.

A encriptação do lado do servidor com chaves geridas pelo cliente é suportada através da integração com [o Azure Key Vault](../key-vault/general/overview.md): 

* Pode criar as suas próprias chaves de encriptação e armazená-las num cofre de chaves ou utilizar as APIs do Azure Key Vault para gerar chaves. 
* Com o Azure Key Vault, também pode auditar o uso da chave.
* O Registo do Contentor Azure suporta a rotação automática das chaves de encriptação do registo quando uma nova versão chave está disponível no Cofre da Chave Azure. Também pode rodar manualmente as chaves de encriptação do registo.

Esta funcionalidade está disponível no nível de serviço de registo de contentores **Premium.** Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md).


## <a name="things-to-know"></a>Aspetos importantes

* Atualmente só pode ativar uma chave gerida pelo cliente quando criar um registo. Ao ativar a chave, configura uma identidade gerida *atribuída pelo utilizador* para aceder ao cofre da chave.
* Depois de ativar a encriptação com uma chave gerida pelo cliente num registo, não é possível desativar a encriptação.  
* O Registo do Contentor Azure suporta apenas as teclas RSA ou RSA-HSM. As chaves da curva elíptica não estão suportadas.
* [A confiança dos conteúdos](container-registry-content-trust.md) não é suportada num registo encriptado com uma chave gerida pelo cliente.
* Num registo encriptado com uma chave gerida pelo cliente, os registos de execução [de Tarefas ACR](container-registry-tasks-overview.md) são atualmente mantidos por apenas 24 horas. Se precisar de reter registos por um período mais longo, consulte orientações para [exportar e armazenar registos de execução de tarefas](container-registry-tasks-logs.md#alternative-log-storage).


> [!IMPORTANT]
> Se planeia armazenar a chave de encriptação do registo num cofre de chave Azure existente que nega o acesso do público e permite apenas o ponto final privado ou redes virtuais selecionadas, são necessários passos de configuração extra. Ver [cenário avançado: Firewall Key Vault](#advanced-scenario-key-vault-firewall) neste artigo.

## <a name="automatic-or-manual-update-of-key-versions"></a>Atualização automática ou manual das versões-chave

Uma consideração importante para a segurança de um registo encriptado com uma chave gerida pelo cliente é a frequência com que atualiza (roda) a chave de encriptação. A sua organização pode ter políticas de conformidade que requerem atualização regular das [versões-chave armazenadas](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) no Cofre da Chave Azure quando utilizadas como chaves geridas pelo cliente. 

Ao configurar a encriptação do registo com uma chave gerida pelo cliente, tem duas opções para atualizar a versão chave utilizada para encriptação:

* **Atualize automaticamente a versão chave** - Para atualizar automaticamente uma chave gerida pelo cliente quando uma nova versão estiver disponível no Azure Key Vault, omita a versão chave quando ativar a encriptação do registo com uma chave gerida pelo cliente. Quando um registo é encriptado com uma chave não versão, o Registo do Contentor Azure verifica regularmente o cofre-chave para uma nova versão chave e atualiza a chave gerida pelo cliente dentro de 1 hora. O Registo do Contentor Azure utiliza automaticamente a versão mais recente da chave.

* **Atualize manualmente a versão chave** - Para utilizar uma versão específica de uma chave para encriptação de registo, especifique essa versão chave quando ativar a encriptação do registo com uma chave gerida pelo cliente. Quando um registo é encriptado com uma versão chave específica, o Registo do Contentor Azure utiliza essa versão para encriptação até que rode manualmente a chave gerida pelo cliente.

Para mais detalhes, consulte [Escolha o ID da chave com ou sem versão chave](#choose-key-id-with-or-without-key-version) e versão chave de [Atualização](#update-key-version), mais tarde neste artigo.

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

Por predefinição, a **definição de eliminação suave** é automaticamente ativada num novo cofre de teclas. Para evitar a perda de dados causada por eliminações acidentais da chave ou do cofre, também ative a definição **de proteção da purga.**

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Para ser utilizado em etapas posteriores, obtenha a identidade de recurso do cofre da chave:

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

Em alternativa, utilize [o Azure RBAC para o Key Vault](../key-vault/general/rbac-guide.md) para atribuir permissões à identidade para aceder ao cofre da chave. Por exemplo, atribua o papel de encriptação do serviço crypto do cofre de chave à identidade utilizando o comando [de criação de função az:](/cli/azure/role/assignment#az-role-assignment-create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
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
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Escolha o ID da chave com ou sem versão chave

Para conveniência, guarde o formato que escolher para o ID chave na variável ambiente $keyID. Pode utilizar um ID de chave com uma versão ou uma chave sem uma versão.

#### <a name="manual-key-rotation---key-id-with-version"></a>Rotação manual da chave - ID chave com versão

Quando usada para encriptar um registo com uma chave gerida pelo cliente, esta chave permite apenas uma rotação manual da chave no Registo do Contentor Azure.

Este exemplo armazena a propriedade da `kid` chave:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Rotação automática da chave - versão de omissão de ID chave 

Quando usada para encriptar um registo com uma chave gerida pelo cliente, esta chave permite a rotação automática da chave quando uma nova versão chave é detetada no Cofre da Chave Azure.

Este exemplo remove a versão da propriedade da `kid` chave:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um registo com chave gerida pelo cliente

Executar o [comando az acr criar][az-acr-create] para criar um registo no nível de serviço Premium e ativar a chave gerida pelo cliente. Passe o ID de identidade gerido e o ID chave, armazenado anteriormente em variáveis ambientais:

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

Dependendo da chave utilizada para encriptar o registo, a saída é semelhante a:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Ativar a chave gerida pelo cliente - portal

### <a name="create-a-managed-identity"></a>Criar uma identidade gerida

Crie uma identidade gerida atribuída ao utilizador [para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal Azure. Para etapas, consulte [Criar uma identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Usa o nome da identidade em passos posteriores.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Criar identidade atribuída ao utilizador no portal Azure":::

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para obter passos para criar um cofre de chaves, consulte [Quickstart: Crie um cofre de chaves utilizando o portal Azure](../key-vault/general/quick-create-portal.md).

Ao criar um cofre-chave para uma chave gerida pelo cliente, no **separador Básicos,** ative a definição **de proteção de purga.** Esta definição ajuda a prevenir a perda de dados causada por eliminações acidentais da chave ou do cofre.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Criar cofre chave no portal Azure":::

### <a name="enable-key-vault-access"></a>Permitir o acesso ao cofre de chaves

Configure uma política para o cofre chave para que a identidade possa acessá-lo.

1. Navegue para o cofre da chave.
1. Selecione **Definições**  >  **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **as permissões de chave**, e selecione **Get**, **Desembrulhar a tecla** e **a tecla wrap**.
1. Em **Select principal,** selecione o nome de recurso da sua identidade gerida atribuída pelo utilizador.  
1. **Selecione Adicionar** e, em seguida, selecione **Guardar**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Criar política de acesso ao cofre chave":::

Em alternativa, utilize [o Azure RBAC para o Key Vault](../key-vault/general/rbac-guide.md) para atribuir permissões à identidade para aceder ao cofre da chave. Por exemplo, atribua o papel de encriptação do Serviço Crypto do Cofre de Chaves à identidade.

1. Navegue para o cofre da chave.
1. Selecione **Access control (IAM)**  >  **+Add**  >  **Add role assignment**.
1. Na janela **de atribuição de funções Adicionar:**
    1. Selecione a função de utilizador do **serviço de encriptação do serviço crypto do cofre** de chaves. 
    1. Atribuir acesso à **identidade gerida atribuída pelo Utilizador.**
    1. Selecione o nome de recurso da sua identidade gerida atribuída pelo utilizador e selecione **Save**.

### <a name="create-key-optional"></a>Criar chave (opcional)

Opcionalmente criar uma chave no cofre chave para usar para encriptar o registo. Siga estes passos se quiser selecionar uma versão chave específica como uma chave gerida pelo cliente. 

1. Navegue para o cofre da chave.
1. Selecione   >  **Teclas de definições**.
1. Selecione **+Gerar/Importar** e introduza um nome único para a chave.
1. Aceite os valores predefinidos restantes e **selecione Criar**.
1. Após a criação, selecione a chave e, em seguida, selecione a versão atual. Copie o **identificador chave** para a versão chave.

### <a name="create-azure-container-registry"></a>Criar um registo de contentor do Azure

1. **Selecione Criar um** registo  >  **de contentores de**  >  recursos.
1. No **separador Básicos,** selecione ou crie um grupo de recursos e introduza um nome de registo. No **SKU,** selecione **Premium**.
1. No **separador Encriptação,** na **tecla gerida pelo Cliente,** selecione **Enabled**.
1. Em **Identidade,** selecione a identidade gerida que criou.
1. Na **Encriptação,** escolha qualquer um dos seguintes:
    * **Selecione o Cofre de Chaves** e selecione um cofre e chave existentes ou crie **novos**. A chave selecionada não é versão e permite a rotação automática da chave.
    * Selecione **Introduzir a tecla URI** e fornecer um identificador de chave diretamente. Pode fornecer um URI de teclas em versão (para uma chave que deve ser rodada manualmente) ou um URI de tecla não versado (que permite a rotação automática da chave). 
1. No **separador Encriptação,** selecione **'Rever + criar' (Análise+'**
1. Selecione **Criar** para implementar a instância de registo.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Criar registo encriptado no portal Azure":::

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

Executar o [grupo de implementação az][az-deployment-group-create] seguinte criar comando para criar o registo usando o ficheiro de modelo anterior. Quando indicado, forneça um novo nome de registo e nome de identidade gerido, bem como o nome do cofre chave e iD chave que criou.

```bash
az deployment group create \
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

Atualize a versão chave no Cofre da Chave Azure ou crie uma nova chave e, em seguida, atualize o registo para encriptar dados usando a chave. Pode executar estes passos utilizando o CLI Azure ou no portal.

Ao rodar uma chave, normalmente especifica a mesma identidade utilizada na criação do registo. Opcionalmente, configurar uma nova identidade atribuída ao utilizador para o acesso à chave, ou ativar e especificar a identidade atribuída ao sistema do registo.

> [!NOTE]
> Certifique-se de que o acesso ao cofre de [teclas](#enable-key-vault-access) necessário está definido para a identidade que configura para o acesso à chave.

### <a name="update-key-version"></a>Atualizar versão chave

Um cenário comum é atualizar a versão da chave utilizada como chave gerida pelo cliente. Dependendo da configuração da encriptação do registo, a chave gerida pelo cliente no Registo do Contentor Azure é automaticamente atualizada ou deve ser atualizada manualmente.

### <a name="azure-cli"></a>CLI do Azure

Utilize comandos [de chave az keyvault][az-keyvault-key] para criar ou gerir as chaves do cofre. Para criar uma nova versão chave, executar a [chave az keyvault criar][az-keyvault-key-create] comando:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

O próximo passo depende de como a encriptação do registo é configurada:

* Se o registo estiver configurado para detetar atualizações de versões chave, a chave gerida pelo cliente é atualizada automaticamente dentro de 1 hora.

* Se o registo estiver configurado para exigir uma atualização manual para uma nova versão chave, execute o comando de [chave de chave de encriptação az acr,][az-acr-encryption-rotate-key] passando o novo ID da chave e a identidade que pretende configurar:

Para atualizar manualmente a versão chave gerida pelo cliente:

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

> [!TIP]
> Quando `az acr encryption rotate-key` correr, pode passar um ID de chave versão ou um ID de chave não versado. Se utilizar um ID de chave não versado, o registo é então configurado para detetar automaticamente atualizações posteriores da versão chave.

### <a name="portal"></a>Portal

Utilize as definições de **encriptação** do registo para atualizar o cofre, chave ou definições de identidade utilizadas para a chave gerida pelo cliente.

Por exemplo, para configurar uma nova chave:

1. No portal, navegue para o seu registo.
1. Em **Definições**, selecione **a**  >  **tecla de alteração de** encriptação .

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Rode a tecla no portal Azure":::
1. Na **Encriptação,** escolha um dos seguintes:
    * **Selecione o Cofre de Chaves** e selecione um cofre e chave existentes ou crie **novos**. A chave selecionada não é versão e permite a rotação automática da chave.
    * Selecione **Introduzir a tecla URI** e fornecer um identificador de chave diretamente. Pode fornecer um URI de teclas em versão (para uma chave que deve ser rodada manualmente) ou um URI de tecla não versado (que permite a rotação automática da chave).
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

É melhor armazenar a chave de encriptação utilizando um cofre de chave Azure existente configurado com uma [firewall Key Vault](../key-vault/general/network-security.md), que nega o acesso público e permite apenas o ponto final privado ou redes virtuais selecionadas. 

Para este cenário, primeiro crie uma nova identidade atribuída ao utilizador, cofre de chaves e registo de contentores encriptado com uma chave gerida pelo cliente, utilizando o [Azure CLI](#enable-customer-managed-key---cli), [portal](#enable-customer-managed-key---portal), ou [modelo](#enable-customer-managed-key---template). Passos detalhados estão nas secções anteriores deste artigo.
   > [!NOTE]
   > O novo cofre está implantado fora da firewall. Só é usado temporariamente para armazenar a chave gerida pelo cliente.

Após a criação do registo, continue com os seguintes passos. Os detalhes estão nas seguintes secções.

1. Ativar a identidade atribuída ao sistema do registo.
1. Conceda as permissões de identidade atribuídas ao sistema para aceder às chaves no cofre que é restrito com a firewall do Cofre de Chaves.
1. Certifique-se de que a firewall Key Vault permite o bypass por serviços fidedignos. Atualmente, um registo de contentores Azure só pode contornar a firewall quando se utiliza a sua identidade gerida pelo sistema. 
1. Rode a chave gerida pelo cliente selecionando uma no cofre de chaves que é restrita com a firewall key Vault.
1. Quando já não for necessário, poderá apagar o cofre chave que foi criado fora da firewall.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Passo 1 - Permitir a identidade atribuída ao sistema do registo

1. No portal, navegue para o seu registo.
1. Selecione   >   **Definições Identidade**.
1. No **Sistema atribuído**, definir **estado** para **On**. Selecione **Guardar**.
1. Copie o **ID** do objeto da identidade.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Passo 2 - Conceder acesso de identidade atribuído ao sistema ao seu cofre de chaves

1. No portal, navegue para o cofre da chave.
1. Selecione **Definições**  >  **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **as permissões de chave**, e selecione **Get**, **Desembrulhar a tecla** e **a tecla wrap**.
1. Escolha **Select principal** e procure o ID do objeto da identidade gerida atribuída ao sistema ou o nome do seu registo.  
1. **Selecione Adicionar** e, em seguida, selecione **Guardar**.

### <a name="step-3---enable-key-vault-bypass"></a>Passo 3 - Ativar o bypass do cofre da chave

Para aceder a um cofre de chaves configurado com uma firewall key Vault, o registo deve contornar a firewall. Certifique-se de que o cofre da chave está configurado para permitir o acesso por qualquer [serviço de confiança](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). O Registo de Contentores Azure é um dos serviços de confiança.

1. No portal, navegue para o cofre da chave.
1. Selecione **Rede de Definições**  >  .
1. Confirme, atualize ou adicione definições de rede virtuais. Para obter etapas detalhadas, consulte [as firewalls Configure Key Vault e redes virtuais](../key-vault/general/network-security.md).
1. Para **permitir que os Serviços Fidedignos da Microsoft contornem esta firewall**, selecione **Sim**. 

### <a name="step-4---rotate-the-customer-managed-key"></a>Passo 4 - Rode a chave gerida pelo cliente

Depois de completar os passos anteriores, rode para uma chave que está guardada no cofre de chaves atrás de uma firewall.

1. No portal, navegue para o seu registo.
1. Em **Definições**, selecione **a**  >  **tecla de alteração de** encriptação .
1. Em **Identidade**, selecione **Sistema Atribuído**.
1. **Selecione o Cofre de Chaves** e selecione o nome do cofre chave que está por trás de uma firewall.
1. Selecione uma chave existente ou **Crie nova**. A chave selecionada não é versão e permite a rotação automática da chave.
1. Complete a seleção de chaves e **selecione Guardar**.

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="removing-managed-identity"></a>Remoção da identidade gerida


Se tentar remover uma identidade gerida atribuída pelo utilizador ou pelo sistema a partir de um registo que seja utilizado para configurar a encriptação, poderá ver uma mensagem de erro semelhante a:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Também não poderá alterar (rodar) a chave de encriptação. Os passos de resolução dependem do tipo de identidade utilizado para a encriptação.

**Identidade atribuída ao utilizador**

Se este problema ocorrer com uma identidade atribuída ao utilizador, desatribua primeiro a identidade utilizando o GUID apresentado na mensagem de erro. Por exemplo:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Em seguida, depois de alterar a chave e atribuir uma identidade diferente, pode remover a identidade original atribuída ao utilizador.

**Identidade atribuída ao sistema**

Se este problema ocorrer com uma identidade atribuída ao sistema, por favor [crie um bilhete de apoio Azure](https://azure.microsoft.com/support/create-ticket/) para assistência para restaurar a identidade.


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
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
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
