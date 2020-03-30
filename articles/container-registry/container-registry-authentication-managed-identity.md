---
title: Autenticar com a identidade gerida
description: Forneça acesso a imagens no seu registo de contentores privados utilizando uma identidade Azure gerida atribuída pelo utilizador ou atribuída pelo sistema.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456490"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Utilize uma identidade gerida pelo Azure para autenticar um registo de contentores Azure 

Utilize uma identidade gerida para os [recursos azure](../active-directory/managed-identities-azure-resources/overview.md) autenticar em um registo de contentores Azure a partir de outro recurso Azure, sem necessidade de fornecer ou gerir credenciais de registo. Por exemplo, crie uma identidade gerida atribuída ao utilizador ou atribuída ao sistema num VM Linux para aceder a imagens de contentores a partir do seu registo de contentores, tão facilmente quanto utilizar um registo público.

Para este artigo, aprende-se mais sobre identidades geridas e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída ao sistema num VM Azure
> * Conceder o acesso de identidade a um registo de contentores Azure
> * Use a identidade gerida para aceder ao registo e puxar uma imagem de contentor 

Para criar os recursos Do Azure, este artigo requer que execute a versão Azure CLI 2.0.55 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Para configurar um registo de contentores e empurrar uma imagem de contentor para ele, também deve ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Uma identidade gerida para os recursos Azure fornece aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode configurar [certos recursos Azure,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)incluindo máquinas virtuais, com uma identidade gerida. Em seguida, use a identidade para aceder a outros recursos Azure, sem passar credenciais em código ou scripts.

As identidades geridas são de dois tipos:

* *Identidades atribuídas*ao utilizador, que pode atribuir a vários recursos e persistir durante o tempo que quiser. As identidades atribuídas ao utilizador estão atualmente em pré-visualização.

* Uma *identidade gerida pelo sistema,* que é única para um recurso específico como uma única máquina virtual e dura a vida útil desse recurso.

Depois de criar um recurso Azure com uma identidade gerida, dê à identidade o acesso que deseja a outro recurso, tal como qualquer diretor de segurança. Por exemplo, atribuir uma identidade gerida um papel com puxão, impulso e puxão, ou outras permissões para um registo privado em Azure. (Para obter uma lista completa das funções de registo, consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores de Azure .) Pode dar acesso de identidade a um ou mais recursos.

Em seguida, utilize a identidade para autenticar qualquer serviço que suporte a [autenticação Azure AD,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)sem qualquer credencial no seu código. Para utilizar a identidade para aceder a um registo de contentores Azure a partir de uma máquina virtual, autentica-se com o Gestor de Recursos Azure. Escolha como autenticar usando a identidade gerida, dependendo do seu cenário:

* [Adquirir um acesso a AD Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programática usando chamadas HTTP ou REST

* Use os [SDKs Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Assine no Azure CLI ou](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) na PowerShell com a identidade. 

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Se ainda não tiver um registo de contentores Azure, crie um registo e empurre-lhe uma imagem de recipiente de amostra. Para os passos, consulte [Quickstart: Crie um registo de contentores privado sintetizador com o Azure CLI](container-registry-get-started-azure-cli.md).

Este artigo assume que `aci-helloworld:v1` tem a imagem do recipiente armazenada no seu registo. Os exemplos utilizam um nome de registo do *myContainerRegistry*. Substitua-o pelo seu próprio registo e nomes de imagem em etapas posteriores.

## <a name="create-a-docker-enabled-vm"></a>Criar um VM ativado por Docker

Crie uma máquina virtual Ubuntu ativada pelo Docker. Também precisa de instalar o [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) na máquina virtual. Se já tem uma máquina virtual Azure, ignore este passo para criar a máquina virtual.

Implementar uma máquina virtual Ubuntu Azure padrão com [az vm criar][az-vm-create]. O exemplo seguinte cria um VM chamado *myDockerVM* num grupo de recursos existente chamado *myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando estiver concluído, `publicIpAddress` tome nota do mostrado pelo Azure CLI. Utilize este endereço para efazer ligações SSH ao VM.

### <a name="install-docker-on-the-vm"></a>Instale docker no VM

Depois de o VM estar em execução, faça uma ligação SSH ao VM. Substitua *publicamente IpAddress* pelo endereço IP público do seu VM.

```bash
ssh azureuser@publicIpAddress
```

Executar o seguinte comando para instalar o Docker no VM:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está a funcionar corretamente no VM:

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

Siga os passos em [Instalação Azure CLI com aptidão](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar o Azure CLI na sua máquina virtual Ubuntu. Para este artigo, certifique-se de que instala a versão 2.0.55 ou posterior.

Saia da sessão ssh.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exemplo 1: Acesso com identidade atribuída ao utilizador

### <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade na sua subscrição usando a [identidade az criar](/cli/azure/identity?view=azure-cli-latest#az-identity-create) comando. Pode utilizar o mesmo grupo de recursos que usou anteriormente para criar o registo de contentores ou a máquina virtual, ou outra.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Para configurar a identidade nos seguintes passos, utilize o comando de mostrar identidade [az][az-identity-show] para armazenar o ID de recursos da identidade e o principal id do serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Porque precisa da identidade num passo posterior quando iniciar sessão no CLI da sua máquina virtual, mostre o valor:

```bash
echo $userID
```

O ID é do formulário:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Configure o VM com a identidade

O seguinte [az vm design de design][az-vm-identity-assign] de comando confunde o seu Docker VM com a identidade atribuída ao utilizador:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso de identidade ao registo de contentores

Agora configure a identidade para aceder ao seu registo de contentores. Primeiro use o comando do [az acr show][az-acr-show] para obter a identificação de recursos do registo:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Utilize a atribuição de [funções az criar][az-role-assignment-create] comando para atribuir a função AcrPull ao registo. Esta função fornece [permissões](container-registry-roles.md) de retirada para o registo. Para fornecer permissões de puxar e empurrar, atribua a função ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Use a identidade para aceder ao registo

SSH na máquina virtual do Docker que está configurada com a identidade. Executar os seguintes comandos Azure CLI, utilizando o CLI Azure instalado no VM.

Primeiro, autenticar o Azure CLI com [login az,][az-login]utilizando a identidade configurada no VM. Para `<userID>`substituir a identificação da identidade que recuperou num passo anterior. 

```azurecli
az login --identity --username <userID>
```

Em seguida, autenticar o registo com [login az acr][az-acr-login]. Quando utiliza este comando, o CLI utiliza o token `az login` Ative Directory criado quando correu para autenticar perfeitamente a sua sessão com o registo do contentor. (Dependendo da configuração do seu VM, poderá ser necessário executar `sudo`este comando de comando e estivador com .)

```azurecli
az acr login --name myContainerRegistry
```

Devia ver `Login succeeded` uma mensagem. Em seguida, `docker` pode executar comandos sem fornecer credenciais. Por exemplo, executar o `aci-helloworld:v1` [estivador puxe][docker-pull] para puxar a imagem, especificando o nome do servidor de login do seu registo. O nome do servidor de login é constituído pelo seu `.azurecr.io` nome de `mycontainerregistry.azurecr.io`registo de contentores (toda a minúscula) seguido por - por exemplo, .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exemplo 2: Acesso com identidade atribuída ao sistema

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Configure o VM com uma identidade gerida pelo sistema

O seguinte [az vm design de design][az-vm-identity-assign] de comando confunde o seu Docker VM com uma identidade atribuída ao sistema:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Utilize o comando [az vm show][az-vm-show] para `principalId` definir uma variável ao valor de (o principal de serviço ID) da identidade do VM, para usar em etapas posteriores.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso de identidade ao registo de contentores

Agora configure a identidade para aceder ao seu registo de contentores. Primeiro use o comando do [az acr show][az-acr-show] para obter a identificação de recursos do registo:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use a atribuição de [funções az criar][az-role-assignment-create] comando para atribuir a função AcrPull à identidade. Esta função fornece [permissões](container-registry-roles.md) de retirada para o registo. Para fornecer permissões de puxar e empurrar, atribua a função ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Use a identidade para aceder ao registo

SSH na máquina virtual do Docker que está configurada com a identidade. Executar os seguintes comandos Azure CLI, utilizando o CLI Azure instalado no VM.

Primeiro, autenticar o Azure CLI com [login az,][az-login]utilizando a identidade atribuída ao sistema no VM.

```azurecli
az login --identity
```

Em seguida, autenticar o registo com [login az acr][az-acr-login]. Quando utiliza este comando, o CLI utiliza o token `az login` Ative Directory criado quando correu para autenticar perfeitamente a sua sessão com o registo do contentor. (Dependendo da configuração do seu VM, poderá ser necessário executar `sudo`este comando de comando e estivador com .)

```azurecli
az acr login --name myContainerRegistry
```

Devia ver `Login succeeded` uma mensagem. Em seguida, `docker` pode executar comandos sem fornecer credenciais. Por exemplo, executar o `aci-helloworld:v1` [estivador puxe][docker-pull] para puxar a imagem, especificando o nome do servidor de login do seu registo. O nome do servidor de login é constituído pelo seu `.azurecr.io` nome de `mycontainerregistry.azurecr.io`registo de contentores (toda a minúscula) seguido por - por exemplo, .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a usar identidades geridas com o Registo de Contentores Azure e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída ao sistema num VM Azure
> * Conceder o acesso de identidade a um registo de contentores Azure
> * Use a identidade gerida para aceder ao registo e puxar uma imagem de contentor

* Saiba mais sobre [identidades geridas para os recursos Azure.](/azure/active-directory/managed-identities-azure-resources/)


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
