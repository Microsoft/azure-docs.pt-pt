---
title: Utilizar uma identidade gerida com o Azure Container Instances
description: Saiba como utilizar uma identidade gerida para se autenticar com outros serviços do Azure do Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: ac0a84aa3121c6ebb91860c96c0f6692827c8a3f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336529"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Como utilizar identidades geridas com o Azure Container Instances

Uso [geridos identidades para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para executar código no Azure Container Instances, que interage com outros serviços do Azure - sem manter quaisquer segredos ou as credenciais no código. A funcionalidade fornece uma implementação do Azure Container Instances com uma identidade gerida automaticamente no Azure Active Directory.

Neste artigo, aprender mais sobre identidades geridas no Azure Container Instances e:

> [!div class="checklist"]
> * Ativar uma identidade de utilizador atribuído ou atribuído de sistema num grupo de contentores
> * Conceder o acesso de identidade para um Azure Key Vault
> * Utilizar a identidade gerida para aceder a um cofre de chaves a partir de um contentor em execução

Adapte os exemplos para ativar e utilizar as identidades no Azure Container Instances para aceder a outros serviços do Azure. Esses exemplos são interativos. No entanto, na prática as imagens de contentor iria executar código para acessar serviços do Azure.

> [!NOTE]
> Atualmente não é possível utilizar uma identidade gerida num grupo de contentores implementado numa rede virtual.

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Utilizar uma identidade gerida num contentor em execução para autenticar a qualquer [serviço que suporta a autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) sem ter de gerir as credenciais em seu código de contentor. Para os serviços que não suportam a autenticação do AD, pode armazenar segredos no Azure Key Vault e utilizar a identidade gerida para aceder ao Key Vault para obter as credenciais. Para obter mais informações sobre como utilizar uma identidade gerida, consulte [o que há de identidades geridas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA). Atualmente, as identidades geridas só são suportadas em instâncias de contentor do Linux.
>  

### <a name="enable-a-managed-identity"></a>Ativar uma identidade gerida

 No Azure Container Instances, identidades geridas para recursos do Azure são suportadas a partir de 2018-10-01 de versão de API de REST e ferramentas e os SDKs correspondentes. Quando cria um grupo de contentores, ativar uma ou mais identidades geridas ao definir uma [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) propriedade. Também pode ativar ou atualizar identidades geridas depois de um grupo de contentores está em execução; qualquer uma das ações faz com que o grupo de contentores reiniciar. Para definir as identidades num grupo de contentores de novo ou existente, utilize a CLI do Azure, um modelo do Resource Manager ou um ficheiro YAML. 

O Azure Container Instances suporta ambos os tipos de identidades do Azure geridas: atribuído ao utilizador e sistema atribuídos. Num grupo de contentor, pode ativar uma identidade atribuída de sistema, um ou mais identidiades atribuídas ou ambos os tipos de identidades. 

* R **atribuído ao utilizador** identidade gerida é criada como uma autónoma recursos do Azure no inquilino do Azure AD que é considerado fidedigno pela subscrição em utilização. Depois da identidade é criada, a identidade pode ser atribuída a um ou mais recursos do Azure (no Azure Container Instances ou outros serviços do Azure). O ciclo de vida de uma identidade atribuído ao utilizador gerido separadamente do ciclo de vida de grupos de contentores ou outros recursos de serviço ao qual está atribuída. Este comportamento é especialmente útil no Azure Container Instances. Uma vez que a identidade ultrapasse o tempo de vida de um grupo de contentor, pode reutilizá-lo juntamente com outras definições de padrão para que as implementações do grupo de contentores altamente passível de repetição.

* R **atribuído ao sistema** identidade gerida é ativada diretamente num grupo de contentores no Azure Container Instances. Quando estiver ativada, o Azure cria uma identidade para o grupo no inquilino do Azure AD que seja considerada fidedigna pela subscrição da instância. Depois da identidade é criada, as credenciais são provisionadas em cada contentor no grupo de contentores. O ciclo de vida de uma identidade atribuída de sistema é diretamente associado ao grupo de contentor que está ativada no. Quando o grupo é eliminado, Azure limpa automaticamente as credenciais e a identidade no Azure AD.

### <a name="use-a-managed-identity"></a>Utilizar uma identidade gerida

Para utilizar uma identidade gerida, a identidade deve inicialmente ser concedida acesso a um ou mais recursos de serviço do Azure (por exemplo, uma aplicação Web, um cofre de chaves ou uma conta de armazenamento) na subscrição. Para acessar os recursos do Azure a partir de um contentor em execução, seu código terá de adquirir um *token de acesso* de um ponto final do Azure. Em seguida, o seu código envia o token de acesso numa chamada para um serviço que suporta a autenticação do Azure AD. 

Utilizar uma identidade gerida num contentor em execução é essencialmente o mesmo que usar uma identidade numa VM do Azure. Veja as orientações de VM para utilizar um [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell ou a CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), ou o [SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este artigo requer a execução da versão 2.0.49 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Criar um Azure Key Vault

Os exemplos neste artigo utilizam uma identidade gerida no Azure Container Instances para aceder a um segredo do Cofre de chaves do Azure. 

Em primeiro lugar, crie um grupo de recursos chamado *myResourceGroup* no *eastus* localização com o seguinte procedimento [criar grupo az](/cli/azure/group?view=azure-cli-latest#az-group-create) comando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utilize o [az keyvault criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando para criar um Key Vault. Certifique-se de que especifique um nome exclusivo do Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store um segredo de exemplo no Key Vault com o [conjunto de segredos de keyvault de az](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) comando:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Continue com os exemplos seguintes para aceder ao Cofre de chaves com uma identidade gerida atribuído ao utilizador ou sistema atribuído no Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemplo 1: Utilizar uma identidade de utilizador atribuída para aceder ao Azure Key Vault

### <a name="create-an-identity"></a>Criar uma identidade

Primeiro de criar uma identidade na sua subscrição com o [identidade az criar](/cli/azure/identity?view=azure-cli-latest#az-identity-create) comando. Pode utilizar o mesmo grupo de recursos utilizado para criar o Cofre de chaves ou utilizar um diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Para utilizar a identidade nos passos seguintes, utilize o [show de identidade az](/cli/azure/identity?view=azure-cli-latest#az-identity-show) comando para armazenar o ID de recurso e o ID de principal de serviço a identidade em variáveis.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Ativar uma identidade de utilizador atribuída num grupo de contentores

Execute o seguinte [criar contentor de az](/cli/azure/container?view=azure-cli-latest#az-container-create) comando para criar uma instância de contentor com base no Ubuntu Server. Este exemplo fornece um grupo de contentor único que pode utilizar para aceder interativamente a outros serviços do Azure. O `--assign-identity` parâmetro passa a sua identidade gerida atribuído ao utilizador para o grupo. O comando de execução longa mantém em execução. Este exemplo utiliza o mesmo grupo de recursos utilizado para criar o Cofre de chaves, mas pode especificar um diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o respetivo estado com o [show de contentor az](/cli/azure/container?view=azure-cli-latest#az-container-show) comando.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

O `identity` secção no resultado é semelhante ao seguinte, que mostra a identidade é definido no grupo de contentores. O `principalID` em `userAssignedIdentities` é o principal de serviço de identidade que criou no Azure Active Directory:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Conceder acesso de identidade atribuído ao utilizador para o Key Vault

Execute o seguinte [az keyvault conjunto-policy](/cli/azure/keyvault?view=azure-cli-latest) comando para definir uma política de acesso no Key Vault. O exemplo a seguir permite que a identidade de utilizador atribuída obter segredos a partir do Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Utilizar identidade de utilizador atribuída para obter um segredo do Key Vault

Agora, pode utilizar a identidade gerida para aceder ao Cofre de chave dentro da instância de contentor em execução. Neste exemplo, primeiro, inicie um shell de bash no contentor:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Execute os seguintes comandos na shell de bash no contentor. Para obter um token de acesso para utilizar o Azure Active Directory para autenticar para o Key Vault, execute o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso numa variável para utilizar nos comandos subsequentes para autenticação, execute o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Utilize o token de acesso para autenticar para o Key Vault e ler um segredo. Certifique-se de que substitua o nome do seu Cofre de chaves no URL (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta é semelhante ao seguinte, que mostra o segredo. Em seu código, seria analisar este resultado para obter o segredo. Em seguida, utilize o segredo numa operação subseqüente para aceder a outro recurso do Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemplo 2: Utilizar uma identidade atribuída de sistema para aceder ao Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Ativar uma identidade atribuída de sistema num grupo de contentores

Execute o seguinte [criar contentor de az](/cli/azure/container?view=azure-cli-latest#az-container-create) comando para criar uma instância de contentor com base no Ubuntu Server. Este exemplo fornece um grupo de contentor único que pode utilizar para aceder interativamente a outros serviços do Azure. O `--assign-identity` parâmetro sem valor adicional permite uma identidade gerida atribuído de sistema no grupo. O comando de execução longa mantém em execução. Este exemplo utiliza o mesmo grupo de recursos utilizado para criar o Cofre de chaves, mas pode especificar um diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Dentro de alguns segundos, deverá receber uma resposta da CLI do Azure que indica que a implementação foi concluída. Verifique o respetivo estado com o [show de contentor az](/cli/azure/container?view=azure-cli-latest#az-container-show) comando.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

O `identity` secção no resultado é semelhante ao seguinte, que mostra que uma identidade atribuída de sistema é criada no Azure Active Directory:

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

Definir uma variável para o valor de `principalId` (o ID principal de serviço) da identidade, para utilizar em passos posteriores.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Conceder acesso ao grupo de contentor para o Key Vault

Execute o seguinte [az keyvault conjunto-policy](/cli/azure/keyvault?view=azure-cli-latest) comando para definir uma política de acesso no Key Vault. O exemplo a seguir permite que a identidade gerida pelo sistema obter segredos a partir do Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Utilizar identidade de grupo do contentor para obter um segredo do Key Vault

Agora, pode utilizar a identidade gerida para aceder ao Cofre de chave dentro da instância de contentor em execução. Neste exemplo, primeiro, inicie um shell de bash no contentor:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Execute os seguintes comandos na shell de bash no contentor. Para obter um token de acesso para utilizar o Azure Active Directory para autenticar para o Key Vault, execute o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso numa variável para utilizar nos comandos subsequentes para autenticação, execute o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Utilize o token de acesso para autenticar para o Key Vault e ler um segredo. Certifique-se de que substitua o nome do seu Cofre de chaves no URL (*https:\//mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta é semelhante ao seguinte, que mostra o segredo. Em seu código, seria analisar este resultado para obter o segredo. Em seguida, utilize o segredo numa operação subseqüente para aceder a outro recurso do Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Ativar a identidade gerida utilizando o modelo do Resource Manager

Para ativar uma identidade gerida num grupo de contentor com um [modelo do Resource Manager](container-instances-multi-container-group.md), defina o `identity` propriedade do `Microsoft.ContainerInstance/containerGroups` objeto com um `ContainerGroupIdentity` objeto. Os trechos de código a seguir mostram o `identity` propriedade configurada para diferentes cenários. Consulte a [referência de modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Especifique um `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade de utilizador atribuído

Uma identidade atribuído ao utilizador é um ID de recurso do formulário:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Pode ativar uma ou mais identidiades atribuídas.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identidade atribuída de sistema

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identidiades atribuídas de sistema e de utilizador

Num grupo de contentor, pode ativar uma identidade atribuída de sistema e um ou mais identidiades atribuídas.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Ativar a identidade gerida com o ficheiro YAML

Para ativar uma identidade gerida num grupo de contentores implementados com um [ficheiro YAML](container-instances-multi-container-yaml.md), incluem o YAML seguinte.
Especifique um `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade de utilizador atribuído

Uma identidade atribuído ao utilizador é um ID de recurso do formulário 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Pode ativar uma ou mais identidiades atribuídas.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identidade atribuída de sistema

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identidiades atribuídas de sistema e de utilizador

Num grupo de contentor, pode ativar uma identidade atribuída de sistema e um ou mais identidiades atribuídas.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre identidades geridas no Azure Container Instances e como:

> [!div class="checklist"]
> * Ativar uma identidade de utilizador atribuído ou atribuído de sistema num grupo de contentores
> * Conceder o acesso de identidade para um Azure Key Vault
> * Utilizar a identidade gerida para aceder a um cofre de chaves a partir de um contentor em execução

* Saiba mais sobre [geridos identidades para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).

* Veja uma [exemplo de SDK Go do Azure](https://medium.com/@samkreter/c98911206328) da utilização de uma identidade gerida para aceder a um cofre de chaves do Azure Container Instances.
