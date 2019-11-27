---
title: Autenticar com a identidade gerida
description: Forneça acesso a imagens em seu registro de contêiner privado usando uma identidade do Azure gerenciada atribuída pelo usuário ou pelo sistema.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456490"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Usar uma identidade gerenciada do Azure para autenticar em um registro de contêiner do Azure 

Use uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para autenticar em um registro de contêiner do Azure de outro recurso do Azure, sem a necessidade de fornecer ou gerenciar credenciais de registro. Por exemplo, configure uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma VM do Linux para acessar as imagens de contêiner do registro de contêiner, tão facilmente quanto usar um registro público.

Para este artigo, você aprende mais sobre identidades gerenciadas e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou atribuída pelo sistema em uma VM do Azure
> * Conceder o acesso de identidade a um registro de contêiner do Azure
> * Usar a identidade gerenciada para acessar o registro e efetuar pull de uma imagem de contêiner 

Para criar os recursos do Azure, este artigo requer que você execute o CLI do Azure versão 2.0.55 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

Para configurar um registro de contêiner e enviar uma imagem de contêiner para ele, você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [MacOS][docker-mac], [Windows][docker-windows]ou [Linux][docker-linux] .

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Uma identidade gerenciada para recursos do Azure fornece aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode configurar [determinados recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), incluindo máquinas virtuais, com uma identidade gerenciada. Em seguida, use a identidade para acessar outros recursos do Azure, sem passar credenciais em código ou scripts.

Identidades gerenciadas são de dois tipos:

* *Identidades atribuídas pelo usuário*, que você pode atribuir a vários recursos e manter o tempo que desejar. Atualmente, as identidades atribuídas ao usuário estão em versão prévia.

* Uma *identidade gerenciada pelo sistema*, que é exclusiva de um recurso específico, como uma única máquina virtual, e dura o tempo de vida desse recurso.

Depois de configurar um recurso do Azure com uma identidade gerenciada, conceda à identidade o acesso que você deseja para outro recurso, assim como qualquer entidade de segurança. Por exemplo, atribua uma identidade gerenciada a uma função com pull, push e pull ou outras permissões a um registro privado no Azure. (Para obter uma lista completa de funções de registro, consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md).) Você pode conceder acesso de identidade a um ou mais recursos.

Em seguida, use a identidade para autenticar em qualquer [serviço que ofereça suporte à autenticação do Azure ad](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), sem nenhuma credencial em seu código. Para usar a identidade para acessar um registro de contêiner do Azure de uma máquina virtual, você autentica com Azure Resource Manager. Escolha como autenticar usando a identidade gerenciada, dependendo do seu cenário:

* [Adquirir um token de acesso do Azure ad](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programaticamente usando chamadas http ou REST

* Usar os [SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Entre no CLI do Azure ou no PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) com a identidade. 

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Se você ainda não tiver um registro de contêiner do Azure, crie um registro e envie por push uma imagem de contêiner de exemplo para ele. Para obter as etapas, consulte [início rápido: criar um registro de contêiner privado usando o CLI do Azure](container-registry-get-started-azure-cli.md).

Este artigo pressupõe que você tenha o `aci-helloworld:v1` a imagem de contêiner armazenada no registro. Os exemplos usam um nome de registro de *myContainerRegistry*. Substitua pelos seus próprios nomes de registro e imagem em etapas posteriores.

## <a name="create-a-docker-enabled-vm"></a>Criar uma VM habilitada para Docker

Crie uma máquina virtual Ubuntu habilitada para Docker. Você também precisa instalar o [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na máquina virtual. Se você já tiver uma máquina virtual do Azure, ignore esta etapa para criar a máquina virtual.

Implante uma máquina virtual do Ubuntu do Azure padrão com [AZ VM Create][az-vm-create]. O exemplo a seguir cria uma VM chamada *myDockerVM* em um grupo de recursos existente chamado *MyResource*Group:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando for concluído, anote as `publicIpAddress` exibidas pela CLI do Azure. Use esse endereço para fazer conexões SSH com a VM.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois que a VM estiver em execução, faça uma conexão SSH com a VM. Substitua *publicIpAddress* pelo endereço IP público da sua VM.

```bash
ssh azureuser@publicIpAddress
```

Execute o seguinte comando para instalar o Docker na VM:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está sendo executado corretamente na VM:

```bash
sudo docker run -it hello-world
```

Saída:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga as etapas em [instalar CLI do Azure com a apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar o CLI do Azure em sua máquina virtual do Ubuntu. Para este artigo, certifique-se de instalar a versão 2.0.55 ou posterior.

Saia da sessão SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exemplo 1: acesso com uma identidade atribuída pelo usuário

### <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade em sua assinatura usando o comando [AZ Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) . Você pode usar o mesmo grupo de recursos que usou anteriormente para criar o registro de contêiner ou a máquina virtual, ou outro.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Para configurar a identidade nas etapas a seguir, use o comando [AZ Identity show][az-identity-show] para armazenar a ID de recurso da identidade e a ID da entidade de serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Como você precisa da ID da identidade em uma etapa posterior quando entrar na CLI de sua máquina virtual, mostre o valor:

```bash
echo $userID
```

A ID está no formato:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Configurar a VM com a identidade

O comando [AZ VM Identity Assign][az-vm-identity-assign] a seguir configura a VM do Docker com a identidade atribuída pelo usuário:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso de identidade ao registro de contêiner

Agora, configure a identidade para acessar o registro de contêiner. Primeiro, use o comando [AZ ACR show][az-acr-show] para obter a ID de recurso do registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a função AcrPull ao registro. Essa função fornece [permissões de pull](container-registry-roles.md) para o registro. Para fornecer as permissões pull e Push, atribua a função ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Usar a identidade para acessar o registro

SSH na máquina virtual do Docker configurada com a identidade. Execute os seguintes comandos de CLI do Azure, usando o CLI do Azure instalado na VM.

Primeiro, autentique-se no CLI do Azure com [AZ login][az-login], usando a identidade configurada na VM. Para `<userID>`, substitua a ID da identidade que você recuperou em uma etapa anterior. 

```azurecli
az login --identity --username <userID>
```

Em seguida, autentique no registro com [AZ ACR login][az-acr-login]. Quando você usa esse comando, a CLI usa o token Active Directory criado quando você executou `az login` para autenticar a sessão diretamente com o registro de contêiner. (Dependendo da configuração da VM, talvez seja necessário executar esse comando e comandos do Docker com o `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Você deverá ver uma mensagem de `Login succeeded`. Em seguida, você pode executar `docker` comandos sem fornecer credenciais. Por exemplo, execute [Docker pull][docker-pull] para efetuar pull da imagem de `aci-helloworld:v1`, especificando o nome do servidor de logon do registro. O nome do servidor de logon consiste em seu nome de registro de contêiner (todas as letras minúsculas) seguido por `.azurecr.io`, por exemplo, `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exemplo 2: acesso com uma identidade atribuída pelo sistema

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Configurar a VM com uma identidade gerenciada pelo sistema

O comando [AZ VM Identity Assign][az-vm-identity-assign] a seguir configura sua VM do Docker com uma identidade atribuída pelo sistema:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Use o comando [AZ VM show][az-vm-show] para definir uma variável para o valor de `principalId` (a ID da entidade de serviço) da identidade da VM, a ser usada em etapas posteriores.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso de identidade ao registro de contêiner

Agora, configure a identidade para acessar o registro de contêiner. Primeiro, use o comando [AZ ACR show][az-acr-show] para obter a ID de recurso do registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a função AcrPull à identidade. Essa função fornece [permissões de pull](container-registry-roles.md) para o registro. Para fornecer as permissões pull e Push, atribua a função ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Usar a identidade para acessar o registro

SSH na máquina virtual do Docker configurada com a identidade. Execute os seguintes comandos de CLI do Azure, usando o CLI do Azure instalado na VM.

Primeiro, autentique o CLI do Azure com [AZ login][az-login], usando a identidade atribuída pelo sistema na VM.

```azurecli
az login --identity
```

Em seguida, autentique no registro com [AZ ACR login][az-acr-login]. Quando você usa esse comando, a CLI usa o token Active Directory criado quando você executou `az login` para autenticar a sessão diretamente com o registro de contêiner. (Dependendo da configuração da VM, talvez seja necessário executar esse comando e comandos do Docker com o `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Você deverá ver uma mensagem de `Login succeeded`. Em seguida, você pode executar `docker` comandos sem fornecer credenciais. Por exemplo, execute [Docker pull][docker-pull] para efetuar pull da imagem de `aci-helloworld:v1`, especificando o nome do servidor de logon do registro. O nome do servidor de logon consiste em seu nome de registro de contêiner (todas as letras minúsculas) seguido por `.azurecr.io`, por exemplo, `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o uso de identidades gerenciadas com o registro de contêiner do Azure e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou pelo sistema em uma VM do Azure
> * Conceder o acesso de identidade a um registro de contêiner do Azure
> * Usar a identidade gerenciada para acessar o registro e efetuar pull de uma imagem de contêiner

* Saiba mais sobre [identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
