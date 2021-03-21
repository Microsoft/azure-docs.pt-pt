---
title: Ativar a identidade gerida no grupo de contentores
description: Saiba como ativar uma identidade gerida em Instâncias de Contentores Azure que possa autenticar com outros serviços Azure
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: a0d029e39122ca7bb858103f4d7f88e2536850d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198324"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Como utilizar identidades geridas com o Azure Container Instances

Utilize [identidades geridas para que os recursos da Azure](../active-directory/managed-identities-azure-resources/overview.md) executem código em Instâncias de Contentores Azure que interage com outros serviços Azure - sem manter quaisquer segredos ou credenciais em código. A funcionalidade fornece uma implantação de Instâncias de Contentores Azure com uma identidade gerida automaticamente no Azure Ative Directory.

Neste artigo, você aprende mais sobre identidades geridas em Instâncias de Contentores Azure e:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída pelo sistema num grupo de contentores
> * Conceder o acesso à identidade a um cofre de chaves Azure
> * Use a identidade gerida para aceder a um cofre chave a partir de um recipiente de corrida

Adaptar os exemplos para permitir e utilizar identidades em Instâncias de Contentores Azure para aceder a outros serviços Azure. Estes exemplos são interativos. No entanto, na prática, as suas imagens de contentores iriam executar código para aceder aos serviços da Azure.
 
> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA). Atualmente, as identidades geridas em Instâncias de Contentores Azure, são apenas suportadas com contentores Linux e ainda não com recipientes Windows.

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Utilize uma identidade gerida num recipiente em funcionamento para autenticar qualquer serviço que suporte a [autenticação AZure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) sem gerir credenciais no seu código de contentor. Para serviços que não suportam a autenticação AD, você pode armazenar segredos em um cofre chave Azure e usar a identidade gerida para aceder ao cofre chave para obter credenciais. Para obter mais informações sobre a utilização de uma identidade gerida, consulte [o que são identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Ativar uma identidade gerida

 Quando criar um grupo de contentores, ative uma ou mais identidades geridas definindo uma propriedade [ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Também pode ativar ou atualizar identidades geridas após o funcionamento de um grupo de contentores - ou a ação faz com que o grupo de contentores reinicie. Para definir as identidades num grupo de contentores novo ou existente, utilize o Azure CLI, um modelo de Gestor de Recursos, um ficheiro YAML ou outra ferramenta Azure. 

Azure Container Instances suporta ambos os tipos de identidades Azure geridas: atribuídas pelo utilizador e atribuídas pelo sistema. Num grupo de contentores, pode ativar uma identidade atribuída ao sistema, uma ou mais identidades atribuídas pelo utilizador, ou ambos os tipos de identidades. Se não está familiarizado com identidades geridas para recursos Azure, consulte a [visão geral](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

Para utilizar uma identidade gerida, a identidade deve ter acesso a um ou mais recursos de serviço Azure (como uma aplicação web, um cofre-chave ou uma conta de armazenamento) na subscrição. A utilização de uma identidade gerida num recipiente de funcionamento é semelhante à utilização de uma identidade num VM Azure. Consulte a orientação VM para utilizar um [símbolo](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell ou Azure CLI,](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)ou os [Azure SDKs](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### <a name="limitations"></a>Limitações

* Atualmente não é possível utilizar uma identidade gerida num grupo de contentores implantado numa rede virtual.
* Não é possível utilizar uma identidade gerida para retirar uma imagem do Registo de Contentores Azure ao criar um grupo de contentores. A identidade só está disponível dentro de um recipiente de funcionamento.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-an-azure-key-vault"></a>Criar um cofre de chaves Azure

Os exemplos deste artigo usam uma identidade gerida em Instâncias de Contentores Azure para aceder a um segredo de cofre chave Azure. 

Primeiro, crie um grupo de recursos com o nome *myResourceGroup* na localização *eualeste* através do comando seguinte [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o [keyvault az criar](/cli/azure/keyvault#az-keyvault-create) comando para criar um cofre de chaves. Certifique-se de especificar um nome único do cofre. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Guarde um segredo de amostra no cofre da chave usando o comando [secreto az keyvault:](/cli/azure/keyvault/secret#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Continue com os seguintes exemplos para aceder ao cofre de chaves utilizando uma identidade gerida atribuída pelo utilizador ou atribuída pelo sistema em Instâncias de Contentores Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemplo 1: Utilize uma identidade atribuída ao utilizador para aceder ao cofre da chave Azure

### <a name="create-an-identity"></a>Criar uma identidade

Primeiro crie uma identidade na sua subscrição usando o comando [de criação de identidade az.](/cli/azure/identity#az-identity-create) Pode utilizar o mesmo grupo de recursos usado para criar o cofre de chaves, ou usar um outro.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Para utilizar a identidade nos seguintes passos, utilize o comando [de demonstração de identidade az](/cli/azure/identity#az-identity-show) para armazenar o ID principal de serviço da identidade e iD de recursos em variáveis.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Conceder acesso de identidade atribuído ao utilizador ao cofre de chaves

Executar o seguinte comando [de definição de chave-chave az](/cli/azure/keyvault) para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade atribuída ao utilizador obtenha segredos a partir do cofre da chave:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Ativar a identidade atribuída pelo utilizador num grupo de contentores

Executar o [seguinte recipiente az criar](/cli/azure/container#az-container-create) comando para criar uma instância de recipiente com base na imagem da `azure-cli` Microsoft. Este exemplo fornece um grupo de contentores únicos que pode utilizar interativamente para executar o Azure CLI para aceder a outros serviços Azure. Nesta secção, apenas é utilizado o sistema operativo base. Por exemplo, utilize o CLI Azure no recipiente, consulte [Ativar a identidade atribuída ao sistema num grupo de contentores](#enable-system-assigned-identity-on-a-container-group). 

O `--assign-identity` parâmetro transmite a sua identidade gerida atribuída ao utilizador para o grupo. O comando de longa duração mantém o contentor a funcionar. Este exemplo usa o mesmo grupo de recursos usado para criar o cofre de chaves, mas você pode especificar um diferente.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o seu estado com o comando de exibição do [contentor az.](/cli/azure/container#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A `identity` secção na saída é semelhante à seguinte, mostrando que a identidade está definida no grupo de contentores. O `principalID` sub é o principal de serviço da identidade que criou no `userAssignedIdentities` Diretório Ativo Azure:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Use a identidade atribuída ao utilizador para obter segredo do cofre de chaves

Agora pode usar a identidade gerida dentro da instância do contentor em funcionamento para aceder ao cofre da chave. Primeiro lance uma casca de bash no recipiente:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Executar os seguintes comandos no invólucro da bata no recipiente. Para obter um token de acesso para usar o Azure Ative Directory para autenticar para o cofre de chaves, executar o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Resultado:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso numa variável para utilizar em comandos subsequentes para autenticar, executar o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Agora use o token de acesso para autenticar para o cofre e leia um segredo. Certifique-se de substituir o nome do seu cofre chave no URL *(https: \/ /mykeyvault.vault.azure.net/...*

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta parece semelhante à seguinte, mostrando o segredo. No seu código, analisaria esta saída para obter o segredo. Em seguida, use o segredo numa operação subsequente para aceder a outro recurso Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemplo 2: Utilize uma identidade atribuída ao sistema para aceder ao cofre da chave Azure

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Ativar a identidade atribuída pelo sistema num grupo de contentores

Executar o [seguinte recipiente az criar](/cli/azure/container#az-container-create) comando para criar uma instância de recipiente com base na imagem da `azure-cli` Microsoft. Este exemplo fornece um grupo de contentores únicos que pode utilizar interativamente para executar o Azure CLI para aceder a outros serviços Azure. 

O `--assign-identity` parâmetro sem valor adicional permite uma identidade gerida atribuída pelo sistema no grupo. A identidade é de âmbito ao grupo de recursos do grupo de contentores. O comando de longa duração mantém o contentor a funcionar. Este exemplo usa o mesmo grupo de recursos usado para criar o cofre chave, que está no âmbito da identidade.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o seu estado com o comando de exibição do [contentor az.](/cli/azure/container#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A `identity` secção na saída é semelhante à seguinte, mostrando que uma identidade atribuída ao sistema é criada no Diretório Ativo Azure:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Desa esta medida uma variável para o valor do `principalId` (iD principal de serviço) da identidade, para utilizar em etapas posteriores.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Grant container grupo acesso ao cofre chave

Executar o seguinte comando [de definição de chave-chave az](/cli/azure/keyvault) para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade gerida pelo sistema obtenha segredos do cofre chave:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Use a identidade do grupo de contentores para obter segredo do cofre chave

Agora pode usar a identidade gerida para aceder ao cofre da chave dentro da instância do contentor em funcionamento. Primeiro lance uma casca de bash no recipiente:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Executar os seguintes comandos no invólucro da bata no recipiente. Primeiro iniciar sessão no Azure CLI utilizando a identidade gerida:

```bash
az login --identity
```

Do contentor de corrida, recupere o segredo do cofre da chave:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

O valor do segredo é recuperado:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Ativar a identidade gerida usando o modelo de Gestor de Recursos

Para ativar uma identidade gerida num grupo de contentores utilizando um modelo de Gestor de [Recursos,](container-instances-multi-container-group.md)deite a `identity` propriedade do objeto com um `Microsoft.ContainerInstance/containerGroups` `ContainerGroupIdentity` objeto. Os seguintes snippets mostram a `identity` propriedade configurada para diferentes cenários. Consulte a referência do [modelo do Gestor de Recursos](/azure/templates/microsoft.containerinstance/containergroups). Especificar um mínimo `apiVersion` de `2018-10-01` .

### <a name="user-assigned-identity"></a>Identidade atribuída ao utilizador

Uma identidade atribuída ao utilizador é uma identificação de recurso do formulário:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Pode ativar uma ou mais identidades atribuídas pelo utilizador.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identidade atribuída ao sistema

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identidades atribuídas ao sistema e ao utilizador

Num grupo de contentores, pode ativar uma identidade atribuída ao sistema e uma ou mais identidades atribuídas pelo utilizador.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Ativar a identidade gerida utilizando o ficheiro YAML

Para ativar uma identidade gerida num grupo de contentores implantado com um [ficheiro YAML,](container-instances-multi-container-yaml.md)inclua o seguinte YAML.
Especificar um mínimo `apiVersion` de `2018-10-01` .

### <a name="user-assigned-identity"></a>Identidade atribuída ao utilizador

Uma identidade atribuída ao utilizador é um ID de recursos do formulário 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Pode ativar uma ou mais identidades atribuídas pelo utilizador.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identidade atribuída ao sistema

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identidades atribuídas ao sistema e ao utilizador

Num grupo de contentores, pode ativar uma identidade atribuída ao sistema e uma ou mais identidades atribuídas pelo utilizador.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre identidades geridas em Instâncias de Contentores Azure e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída pelo sistema num grupo de contentores
> * Conceder o acesso à identidade a um cofre de chaves Azure
> * Use a identidade gerida para aceder a um cofre chave a partir de um recipiente de corrida

* Saiba mais sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/index.yml)

* Veja um [exemplo do Azure Go SDK](https://medium.com/@samkreter/c98911206328) de usar uma identidade gerida para aceder a um cofre chave a partir de Instâncias de Contentores Azure.
