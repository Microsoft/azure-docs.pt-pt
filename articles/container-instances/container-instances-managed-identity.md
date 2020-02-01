---
title: Ativar identidade gerida em grupo de contentores
description: Saiba como permitir uma identidade gerida em Instâncias de Contentores Azure que possam autenticar com outros serviços Azure
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901940"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Como usar identidades geridas com instâncias de contentores Azure

Utilize [identidades geridas para](../active-directory/managed-identities-azure-resources/overview.md) os recursos do Azure executar código em Casos de Contentores Azure que interagecom outros serviços Azure - sem manter quaisquer segredos ou credenciais em código. A funcionalidade fornece uma implantação de instâncias de contentores Azure com uma identidade gerida automaticamente no Diretório Ativo Azure.

Neste artigo, aprende-se mais sobre identidades geridas em Instâncias de Contentores Azure e:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída ao sistema num grupo de contentores
> * Conceda o acesso de identidade a um cofre chave Azure
> * Use a identidade gerida para aceder a um cofre chave a partir de um contentor de corrida

Adaptar os exemplos para permitir e utilizar identidades em Casos de Contentores Azure para aceder a outros serviços Azure. Estes exemplos são interativos. No entanto, na prática, as suas imagens de contentores seriam executadas código para aceder aos serviços Azure.

> [!NOTE]
> Atualmente não é possível utilizar uma identidade gerida num grupo de contentores implantado numa rede virtual.

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Utilize uma identidade gerida num contentor de corrida para autenticar qualquer serviço que suporte a [autenticação DaD Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) sem gerir credenciais no seu código de contentor. Para serviços que não suportam a autenticação de AD, você pode armazenar segredos em um cofre chave Azure e usar a identidade gerida para aceder ao cofre chave para obter credenciais. Para obter mais informações sobre o uso de uma identidade gerida, consulte [as identidades geridas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA). Atualmente, as identidades geridas em Casos de Contentores Azure, são suportadas apenas com contentores Linux e ainda não com contentores Windows.
>  

### <a name="enable-a-managed-identity"></a>Ativar uma identidade gerida

 Em Casos de Contentores Azure, as identidades geridas para os recursos Azure são suportadas a partir da versão REST API 2018-10-01 e sDKs e ferramentas correspondentes. Quando criar um grupo de contentores, ative uma ou mais identidades geridas através da definição de uma propriedade [ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Também pode ativar ou atualizar identidades geridas após o funcionamento de um grupo de contentores - qualquer uma das ações faz com que o grupo de contentores reinicie. Para definir as identidades num grupo de contentores novo ou existente, utilize o Azure CLI, um modelo de Gestor de Recursos ou um ficheiro YAML. 

As Instâncias de Contentores Azure suportam ambos os tipos de identidades Azure geridas: atribuídas ao utilizador e atribuídas ao sistema. Num grupo de contentores, pode ativar uma identidade atribuída ao sistema, uma ou mais identidades atribuídas ao utilizador ou ambos os tipos de identidades. 

* Uma identidade gerida atribuída ao **utilizador** é criada como um recurso Azure autónomo no inquilino Azure AD que é confiável pela subscrição em uso. Após a criação da identidade, a identidade pode ser atribuída a um ou mais recursos Azure (em Casos de Contentores Azure ou outros serviços Azure). O ciclo de vida de uma identidade atribuída ao utilizador é gerido separadamente do ciclo de vida dos grupos de contentores ou de outros recursos de serviço a que é atribuído. Este comportamento é especialmente útil em Instâncias de Contentores Azure. Uma vez que a identidade se estende para além da vida útil de um grupo de contentores, pode reutilizá-la juntamente com outras configurações padrão para tornar as implementações do seu grupo de contentores altamente repetíveis.

* Uma identidade gerida atribuída ao **sistema** é ativada diretamente num grupo de contentores em Instâncias de Contentores Azure. Quando está ativado, o Azure cria uma identidade para o grupo no inquilino da AD Azure que é confiada pela subscrição da instância. Após a criação da identidade, as credenciais são aprovisionadas em cada contentor do grupo de contentores. O ciclo de vida de uma identidade atribuída ao sistema está diretamente ligado ao grupo de contentores em que está ativado. Quando o grupo é eliminado, o Azure limpa automaticamente as credenciais e a identidade em Azure AD.

### <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

Para utilizar uma identidade gerida, a identidade deve inicialmente ter acesso a um ou mais recursos de serviço Azure (como uma aplicação web, um cofre chave ou uma conta de armazenamento) na subscrição. Para aceder aos recursos azure a partir de um contentor em funcionamento, o seu código deve adquirir um sinal de *acesso* a partir de um ponto final da AD Azure. Em seguida, o seu código envia o sinal de acesso numa chamada para um serviço que suporta a autenticação Azure AD. 

A utilização de uma identidade gerida num contentor de corrida é essencialmente a mesma que usar uma identidade num VM Azure. Consulte a orientação VM para utilizar um [símbolo,](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) [Azure PowerShell ou Azure CLI,](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)ou os [SDKs Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este artigo requer que esteja a executar a versão Azure CLI 2.0.49 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Criar um cofre chave Azure

Os exemplos deste artigo usam uma identidade gerida em Casos de Contentores Azure para aceder a um segredo de cofre chave Azure. 

Primeiro, crie um grupo de recursos chamado *myResourceGroup* no local *leste* com o seguinte [grupo az criar](/cli/azure/group?view=azure-cli-latest#az-group-create) comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando para criar um cofre chave. Certifique-se de especificar um nome único do cofre chave. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Guarde um segredo de amostra no cofre da chave usando o comando secreto do [cofre az:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Continue com os seguintes exemplos para aceder ao cofre chave utilizando uma identidade gerida atribuída pelo utilizador ou atribuída ao sistema em Instâncias de Contentores Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemplo 1: Utilize uma identidade atribuída ao utilizador para aceder ao cofre chave Azure

### <a name="create-an-identity"></a>Criar uma identidade

Primeiro crie uma identidade na sua subscrição usando a [identidade az criar](/cli/azure/identity?view=azure-cli-latest#az-identity-create) comando. Pode usar o mesmo grupo de recursos usado para criar o cofre de chaves, ou usar um outro.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Para utilizar a identidade nos seguintes passos, utilize o comando de [mostrar identidade az](/cli/azure/identity?view=azure-cli-latest#az-identity-show) para armazenar o ID principal de serviço da identidade e o ID de recursos em variáveis.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Ativar uma identidade atribuída ao utilizador num grupo de contentores

Executar o [seguinte recipiente az criar](/cli/azure/container?view=azure-cli-latest#az-container-create) comando para criar uma instância de recipiente com base na imagem `azure-cli` da Microsoft. Este exemplo fornece um grupo de contentores únicos que você pode usar interativamente para executar o Azure CLI para aceder a outros serviços Azure. Nesta secção, apenas é utilizado o sistema operativo base Ubuntu. 

O parâmetro `--assign-identity` passa a sua identidade gerida atribuída ao grupo. O comando de longa duração mantém o contentor a funcionar. Este exemplo usa o mesmo grupo de recursos usado para criar o cofre chave, mas você pode especificar um diferente.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o seu estado com o comando de exibição de [contentores az.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A secção `identity` na saída é semelhante à seguinte, mostrando que a identidade está definida no grupo de contentores. O `principalID` sob `userAssignedIdentities` é o principal de serviço da identidade que criou no Diretório Ativo azure:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Conceder acesso de identidade atribuído ao cofre chave

Execute o seguinte comando [de definição de teclado az keyvault](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no cofre chave. O exemplo que se segue permite que a identidade atribuída ao utilizador obtenha segredos do cofre chave:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Use a identidade atribuída ao utilizador para obter segredo do cofre da chave

Agora pode usar a identidade gerida dentro da instância do contentor de corrida para aceder ao cofre chave. Primeiro lance uma concha de festa no recipiente:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Executar os seguintes comandos na concha do recipiente. Para obter um sinal de acesso para usar o Diretório Ativo Azure para autenticar o cofre chave, executar o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o sinal de acesso numa variável para utilizar em comandos subsequentes para autenticar, executar o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Agora use o sinal de acesso para autenticar o cofre e leia um segredo. Certifique-se de substituir o nome do seu cofre chave no URL *(https://mykeyvault.vault.azure.net/...*

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta parece semelhante à seguinte, mostrando o segredo. No seu código, analisaria esta saída para obter o segredo. Em seguida, use o segredo numa operação subsequente para aceder a outro recurso Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemplo 2: Utilize uma identidade atribuída ao sistema para aceder ao cofre chave Azure

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Permitir uma identidade atribuída ao sistema num grupo de contentores

Executar o [seguinte recipiente az criar](/cli/azure/container?view=azure-cli-latest#az-container-create) comando para criar uma instância de recipiente com base na imagem `azure-cli` da Microsoft. Este exemplo fornece um grupo de contentores únicos que você pode usar interativamente para executar o Azure CLI para aceder a outros serviços Azure. 

O parâmetro `--assign-identity` sem valor adicional permite uma identidade gerida atribuída pelo sistema no grupo. A identidade é remeto para o grupo de recursos do grupo de contentores. O comando de longa duração mantém o contentor a funcionar. Este exemplo usa o mesmo grupo de recursos usado para criar o cofre chave, mas você pode especificar um diferente.

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

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o seu estado com o comando de exibição de [contentores az.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A secção `identity` na saída é semelhante à seguinte, mostrando que uma identidade atribuída ao sistema é criada no Diretório Ativo Azure:

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

Detete uma variável ao valor de `principalId` (o principal de serviço ID) da identidade, para utilizar em etapas posteriores.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Conceder acesso ao grupo de contentores para o cofre chave

Execute o seguinte comando [de definição de teclado az keyvault](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no cofre chave. O exemplo que se segue permite que a identidade gerida pelo sistema obtenha segredos do cofre chave:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Use a identidade do grupo de contentores para obter segredo do cofre chave

Agora pode usar a identidade gerida para aceder ao cofre chave dentro da instância do contentor de corrida. Primeiro lance uma concha de festa no recipiente:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Executar os seguintes comandos na concha do recipiente. Primeiro faça login no Azure CLI utilizando a identidade gerida:

```bash
az login --identity
```

Do recipiente de corrida, recupere o segredo do cofre da chave:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

O valor do segredo é recuperado:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Ativar identidade gerida usando modelo de Gestor de Recursos

Para permitir uma identidade gerida num grupo de contentores utilizando um modelo de Gestor de [Recursos,](container-instances-multi-container-group.md)detete a propriedade `identity` do objeto `Microsoft.ContainerInstance/containerGroups` com um objeto `ContainerGroupIdentity`. Os seguintes cortes mostram a propriedade `identity` configurada para diferentes cenários. Consulte a referência do [modelo do Gestor](/azure/templates/microsoft.containerinstance/containergroups)de Recursos . Especifique um mínimo `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade atribuída ao utilizador

Uma identidade atribuída ao utilizador é uma identificação de recurso do formulário:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Pode ativar uma ou mais identidades atribuídas ao utilizador.

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

Num grupo de contentores, pode ativar uma identidade atribuída ao sistema e uma ou mais identidades atribuídas ao utilizador.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Ativar identidade gerida utilizando ficheiro YAML

Para permitir uma identidade gerida num grupo de contentores implantado utilizando um [ficheiro YAML,](container-instances-multi-container-yaml.md)inclua o seguinte YAML.
Especifique um mínimo `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade atribuída ao utilizador

Uma identidade atribuída ao utilizador é uma identificação de recurso do formulário 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Pode ativar uma ou mais identidades atribuídas ao utilizador.

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

Num grupo de contentores, pode ativar uma identidade atribuída ao sistema e uma ou mais identidades atribuídas ao utilizador.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre identidades geridas em Casos de Contentores Azure e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída ao sistema num grupo de contentores
> * Conceda o acesso de identidade a um cofre chave Azure
> * Use a identidade gerida para aceder a um cofre chave a partir de um contentor de corrida

* Saiba mais sobre [identidades geridas para os recursos Azure.](/azure/active-directory/managed-identities-azure-resources/)

* Veja um [exemplo de Azure Go SDK](https://medium.com/@samkreter/c98911206328) de usar uma identidade gerida para aceder a um cofre chave a partir de Instâncias de Contentores Azure.
