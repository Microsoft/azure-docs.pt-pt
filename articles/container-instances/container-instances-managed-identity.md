---
title: Habilitar identidade gerenciada no grupo de contêineres
description: Saiba como habilitar uma identidade gerenciada em instâncias de contêiner do Azure que podem ser autenticadas com outros serviços do Azure
ms.topic: article
ms.date: 10/22/2018
ms.openlocfilehash: b5546e8c4b512b584a57e8e4c2ff46c52ab856a0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533683"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Como usar identidades gerenciadas com instâncias de contêiner do Azure

Use [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para executar código em instâncias de contêiner do Azure que interage com outros serviços do Azure, sem manter quaisquer segredos ou credenciais no código. O recurso fornece uma implantação de instâncias de contêiner do Azure com uma identidade gerenciada automaticamente no Azure Active Directory.

Neste artigo, você aprenderá mais sobre identidades gerenciadas em instâncias de contêiner do Azure e:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou pelo sistema em um grupo de contêineres
> * Conceder acesso de identidade a um Azure Key Vault
> * Usar a identidade gerenciada para acessar um Key Vault de um contêiner em execução

Adapte os exemplos para habilitar e usar identidades em instâncias de contêiner do Azure para acessar outros serviços do Azure. Esses exemplos são interativos. No entanto, na prática, as imagens de contêiner executarão o código para acessar os serviços do Azure.

> [!NOTE]
> No momento, você não pode usar uma identidade gerenciada em um grupo de contêineres implantado em uma rede virtual.

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Use uma identidade gerenciada em um contêiner em execução para autenticar em qualquer [serviço que dê suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) sem gerenciar credenciais no código do contêiner. Para serviços que não dão suporte à autenticação do AD, você pode armazenar segredos no Azure Key Vault e usar a identidade gerenciada para acessar Key Vault para recuperar credenciais. Para obter mais informações sobre como usar uma identidade gerenciada, consulte [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA). Atualmente, as identidades gerenciadas só têm suporte em instâncias de contêiner do Linux.
>  

### <a name="enable-a-managed-identity"></a>Habilitar uma identidade gerenciada

 Em instâncias de contêiner do Azure, as identidades gerenciadas para recursos do Azure têm suporte a partir da versão 2018-10-01 da API REST e SDKs e ferramentas correspondentes. Ao criar um grupo de contêineres, habilite uma ou mais identidades gerenciadas definindo uma propriedade [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) . Você também pode habilitar ou atualizar identidades gerenciadas depois que um grupo de contêineres estiver em execução; qualquer uma das ações faz com que o grupo de contêineres seja reiniciado. Para definir as identidades em um grupo de contêiner novo ou existente, use o CLI do Azure, um modelo do Resource Manager ou um arquivo YAML. 

As instâncias de contêiner do Azure dão suporte a ambos os tipos de identidades gerenciadas do Azure: atribuído pelo usuário e atribuído pelo sistema. Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema, uma ou mais identidades atribuídas pelo usuário ou ambos os tipos de identidades. 

* Uma identidade gerenciada **atribuída pelo usuário** é criada como um recurso autônomo do Azure no locatário do Azure AD que é confiável para a assinatura em uso. Depois que a identidade é criada, a identidade pode ser atribuída a um ou mais recursos do Azure (em instâncias de contêiner do Azure ou outros serviços do Azure). O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida dos grupos de contêineres ou outros recursos de serviço aos quais ele está atribuído. Esse comportamento é especialmente útil em instâncias de contêiner do Azure. Como a identidade se estende além do tempo de vida de um grupo de contêineres, você pode reutilizá-lo junto com outras configurações padrão para tornar as implantações de grupo de contêineres altamente reproduzíveis.

* Uma identidade gerenciada **atribuída pelo sistema** é habilitada diretamente em um grupo de contêineres em instâncias de contêiner do Azure. Quando habilitado, o Azure cria uma identidade para o grupo no locatário do Azure AD que é confiável para a assinatura da instância. Depois que a identidade é criada, as credenciais são provisionadas em cada contêiner no grupo de contêineres. O ciclo de vida de uma identidade atribuída pelo sistema está diretamente ligado ao grupo de contêineres em que está habilitado. Quando o grupo é excluído, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.

### <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

Para usar uma identidade gerenciada, inicialmente, a identidade deve receber acesso a um ou mais recursos de serviço do Azure (como um aplicativo Web, um Key Vault ou uma conta de armazenamento) na assinatura. Para acessar os recursos do Azure de um contêiner em execução, seu código deve adquirir um *token de acesso* de um ponto de extremidade do Azure AD. Em seguida, seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD. 

Usar uma identidade gerenciada em um contêiner em execução é essencialmente o mesmo que usar uma identidade em uma VM do Azure. Consulte a orientação da VM para usar um [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell ou CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)ou os [SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.0.49 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Os exemplos neste artigo usam uma identidade gerenciada em instâncias de contêiner do Azure para acessar um segredo de Azure Key Vault. 

Primeiro, crie um grupo de recursos chamado *MyResource* Group no local *eastus* com o seguinte comando [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) para criar um Key Vault. Certifique-se de especificar um nome de Key Vault exclusivo. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Armazene um segredo de exemplo no Key Vault usando o comando [AZ keyvault segredo Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Continue com os exemplos a seguir para acessar o Key Vault usando uma identidade gerenciada atribuída pelo usuário ou pelo sistema em instâncias de contêiner do Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemplo 1: usar uma identidade atribuída pelo usuário para acessar Azure Key Vault

### <a name="create-an-identity"></a>Criar uma identidade

Primeiro, crie uma identidade em sua assinatura usando o comando [AZ Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) . Você pode usar o mesmo grupo de recursos usado para criar o Key Vault ou usar um diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Para usar a identidade nas etapas a seguir, use o comando [AZ Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) para armazenar a ID da entidade de serviço da identidade e a ID do recurso em variáveis.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Habilitar uma identidade atribuída pelo usuário em um grupo de contêineres

Execute o comando [AZ container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) a seguir para criar uma instância de contêiner com base no servidor Ubuntu. Este exemplo fornece um grupo de contêiner único que você pode usar para acessar interativamente outros serviços do Azure. O parâmetro `--assign-identity` passa sua identidade gerenciada atribuída pelo usuário para o grupo. O comando de execução longa mantém o contêiner em execução. Este exemplo usa o mesmo grupo de recursos usado para criar o Key Vault, mas você pode especificar um diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique seu status com o comando [AZ container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A seção `identity` na saída é semelhante ao seguinte, mostrando que a identidade está definida no grupo de contêineres. O `principalID` em `userAssignedIdentities` é a entidade de serviço da identidade que você criou em Azure Active Directory:

```console
...
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
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Conceder acesso de identidade atribuído pelo usuário à Key Vault

Execute o seguinte comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no Key Vault. O exemplo a seguir permite que a identidade atribuída pelo usuário obtenha segredos do Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Use a identidade atribuída pelo usuário para obter o segredo de Key Vault

Agora você pode usar a identidade gerenciada para acessar o Key Vault dentro da instância de contêiner em execução. Para este exemplo, primeiro inicie um shell bash no contêiner:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Execute os comandos a seguir no shell bash no contêiner. Para obter um token de acesso para usar Azure Active Directory para autenticar no Key Vault, execute o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso em uma variável a ser usada em comandos subsequentes para autenticar, execute o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Agora, use o token de acesso para autenticar para Key Vault e ler um segredo. Certifique-se de substituir o nome do cofre de chaves na URL ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta é semelhante à seguinte, mostrando o segredo. Em seu código, você analisaria essa saída para obter o segredo. Em seguida, use o segredo em uma operação subsequente para acessar outro recurso do Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemplo 2: usar uma identidade atribuída pelo sistema para acessar Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Habilitar uma identidade atribuída pelo sistema em um grupo de contêineres

Execute o comando [AZ container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) a seguir para criar uma instância de contêiner com base no servidor Ubuntu. Este exemplo fornece um grupo de contêiner único que você pode usar para acessar interativamente outros serviços do Azure. O parâmetro `--assign-identity` sem valor adicional habilita uma identidade gerenciada atribuída pelo sistema no grupo. O comando de execução longa mantém o contêiner em execução. Este exemplo usa o mesmo grupo de recursos usado para criar o Key Vault, mas você pode especificar um diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique seu status com o comando [AZ container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A seção `identity` na saída é semelhante ao seguinte, mostrando que uma identidade atribuída pelo sistema é criada no Azure Active Directory:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Defina uma variável para o valor de `principalId` (a ID da entidade de serviço) da identidade, a ser usada em etapas posteriores.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Conceder acesso ao grupo de contêineres ao Key Vault

Execute o seguinte comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no Key Vault. O exemplo a seguir permite que a identidade gerenciada pelo sistema obtenha segredos do Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Use a identidade do grupo de contêineres para obter o segredo de Key Vault

Agora você pode usar a identidade gerenciada para acessar o Key Vault dentro da instância de contêiner em execução. Para este exemplo, primeiro inicie um shell bash no contêiner:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Execute os comandos a seguir no shell bash no contêiner. Para obter um token de acesso para usar Azure Active Directory para autenticar no Key Vault, execute o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso em uma variável a ser usada em comandos subsequentes para autenticar, execute o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Agora, use o token de acesso para autenticar para Key Vault e ler um segredo. Certifique-se de substituir o nome do cofre de chaves na URL (*https:\//mykeyvault.Vault.Azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta é semelhante à seguinte, mostrando o segredo. Em seu código, você analisaria essa saída para obter o segredo. Em seguida, use o segredo em uma operação subsequente para acessar outro recurso do Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Habilitar identidade gerenciada usando o modelo do Resource Manager

Para habilitar uma identidade gerenciada em um grupo de contêineres usando um [modelo do Resource Manager](container-instances-multi-container-group.md), defina a propriedade `identity` do objeto `Microsoft.ContainerInstance/containerGroups` com um objeto `ContainerGroupIdentity`. Os trechos de código a seguir mostram a propriedade `identity` configurada para cenários diferentes. Consulte a [referência de modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Especifique um `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Uma identidade atribuída pelo usuário é uma ID de recurso do formulário:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Você pode habilitar uma ou mais identidades atribuídas pelo usuário.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identidades atribuídas pelo sistema e pelo usuário

Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema e uma ou mais identidades atribuídas pelo usuário.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Habilitar identidade gerenciada usando o arquivo YAML

Para habilitar uma identidade gerenciada em um grupo de contêineres implantado usando um [arquivo YAML](container-instances-multi-container-yaml.md), inclua o seguinte YAML.
Especifique um `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Uma identidade atribuída pelo usuário é uma ID de recurso do formulário 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Você pode habilitar uma ou mais identidades atribuídas pelo usuário.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identidades atribuídas pelo sistema e pelo usuário

Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema e uma ou mais identidades atribuídas pelo usuário.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre identidades gerenciadas em instâncias de contêiner do Azure e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou pelo sistema em um grupo de contêineres
> * Conceder acesso de identidade a um Azure Key Vault
> * Usar a identidade gerenciada para acessar um Key Vault de um contêiner em execução

* Saiba mais sobre [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).

* Consulte um [exemplo do SDK do Azure go](https://medium.com/@samkreter/c98911206328) do uso de uma identidade gerenciada para acessar uma Key Vault de instâncias de contêiner do Azure.
