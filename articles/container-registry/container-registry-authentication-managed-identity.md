---
title: Autenticação de registo de contentor do Azure com uma identidade gerida
description: Fornece acesso a imagens no seu registo de contentor privado, utilizando uma identidade de Azure gerida atribuído ao utilizador ou sistema atribuído.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: b09348e98a0dee85338cc9f20289d83b658eb719
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338467"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Utilização do Azure geridos identidade para autenticar para o Azure container registry 

Utilize um [identidade de recursos do Azure gerida](../active-directory/managed-identities-azure-resources/overview.md) autenticar para o Azure container registry de outro recurso do Azure, sem necessidade de fornecer ou gerir as credenciais do registo. Por exemplo, configure uma identidade gerida atribuído ao utilizador ou sistema atribuído numa VM do Linux para imagens de contentor de acesso do seu registo de contentor, tão facilmente quanto usar um registo público.

Neste artigo, aprender mais sobre identidades geridas e como:

> [!div class="checklist"]
> * Ativar uma identidade de utilizador atribuído ou atribuída de sistema numa VM do Azure
> * Conceder o acesso de identidade para o Azure container registry
> * Utilizar a identidade gerida para aceder ao registo e solicitar uma imagem de contentor 

Para criar os recursos do Azure, este artigo requer que execute a CLI do Azure versão 2.0.55 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Para configurar um registo de contentor e enviar uma imagem de contentor para ele, também tem de ter o Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Uma identidade gerida para recursos do Azure fornece serviços do Azure com uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode configurar [determinados recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), incluindo máquinas virtuais, com uma identidade gerida. Em seguida, utilize a identidade para aceder a outros recursos do Azure, sem passar credenciais no código ou em scripts.

Identidades geridas são de dois tipos:

* *Identidiades atribuídas*, que pode atribuir a vários recursos e persistir durante mais tempo pretendido. Identidiades atribuídas estão atualmente em pré-visualização.

* R *gerenciados pelo sistema de identidade*, que é exclusivo a um recurso específico, como uma única máquina virtual e uma duração de tempo de vida desse recurso.

Depois de configurar um recurso do Azure com uma identidade gerida, dar a identidade do acesso que pretende para outro recurso, tal como qualquer entidade de segurança. Por exemplo, atribua uma identidade gerida uma função com a solicitação, push e pull ou outras permissões para um registo privado no Azure. (Para obter uma lista completa de funções de registo, consulte [permissões e funções do Azure Container Registry](container-registry-roles.md).) Pode conceder um acesso de identidade para um ou mais recursos.

Em seguida, utilize a identidade para autenticar a qualquer [serviço que suporta a autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), sem quaisquer credenciais no seu código. Para utilizar a identidade para aceder a um registo de contentor do Azure a partir de uma máquina virtual, se autenticar com o Azure Resource Manager. Escolha como pretende efetuar a autenticação com a identidade gerida, dependendo do seu cenário:

* [Adquirir um token de acesso do Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programaticamente usando chamadas HTTP ou REST

* Utilize o [os SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Inicie sessão no CLI do Azure ou o PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) com a identidade. 

## <a name="create-a-container-registry"></a>Criar um registo de contentores

Se ainda não tiver um Azure container registry, criar um registo e enviar uma imagem de contentor de exemplo para ele. Para obter os passos, consulte [início rápido: Criar um registo de contentor privado com a CLI do Azure](container-registry-get-started-azure-cli.md).

Este artigo pressupõe que tenha o `aci-helloworld:v1` armazenada no seu registo de imagem de contentor. Os exemplos utilizam um nome de registo *myContainerRegistry*. Substitua o seu registo e os nomes de imagens em passos posteriores.

## <a name="create-a-docker-enabled-vm"></a>Criar uma VM do Docker-ativada

Crie uma máquina virtual do Ubuntu habilitados no Docker. Também tem de instalar o [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na máquina virtual. Se já tiver uma máquina virtual do Azure, ignore este passo para criar a máquina virtual.

Implementar um padrão máquina virtual do Ubuntu Azure com [az vm crie][az-vm-create]. O exemplo seguinte cria uma VM com o nome *myDockerVM* no grupo de recursos existente denominado *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando for concluído, anote o `publicIpAddress` apresentado pela CLI do Azure. Utilize este endereço para estabeleça ligações SSH à VM.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois da VM está em execução, estabeleça uma ligação SSH à VM. Substitua *publicIpAddress* com o endereço IP público da sua VM.

```bash
ssh azureuser@publicIpAddress
```

Execute o seguinte comando para instalar o Docker na VM:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar que o Docker está a funcionar corretamente na VM:

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

Siga os passos em [instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure na sua máquina virtual do Ubuntu. Neste artigo, certifique-se que instale a versão 2.0.55 ou posterior.

Saia da sessão SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exemplo 1: Acesso com uma identidade de utilizador atribuído

### <a name="create-an-identity"></a>Criar uma identidade

Criar uma identidade na sua subscrição com o [identidade az criar](/cli/azure/identity?view=azure-cli-latest#az-identity-create) comando. Pode utilizar o mesmo grupo de recursos que utilizou anteriormente para criar o registo de contentor ou uma máquina virtual ou num diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Para configurar a identidade nos passos seguintes, utilize o [show de identidade az] [ az-identity-show] comando para armazenar o recurso com a identidade ID e o ID de principal de serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Uma vez que precisa do ID da identidade num passo posterior ao iniciar sessão para a CLI da sua máquina virtual, mostra o valor:

```bash
echo $userID
```

O ID é o formato:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Configurar a VM com a identidade

O seguinte procedimento [atribuir identidade da vm de az] [ az-vm-identity-assign] comando configura a VM do Docker com a identidade de utilizador atribuída:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso de identidade para o registo de contentor

Agora configure a identidade para aceder ao seu registo de contentor. Utilizar pela primeira vez o [show do az acr] [ az-acr-show] comando para obter o ID de recurso do Registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Utilize o [criação da atribuição de função de az] [ az-role-assignment-create] comando para atribuir a função de AcrPull no Registro. Esta função fornece [solicitar permissões](container-registry-roles.md) no Registro. Para fornecer os dois pull e permissões de push, atribua a função de ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Utilizar a identidade para aceder ao registo

SSH para a máquina virtual de Docker que está configurada com a identidade. Execute os seguintes comandos do CLI do Azure, com a CLI do Azure instalado na VM.

Primeiro, autenticar para a CLI do Azure com [início de sessão az][az-login], usando a identidade configurada na VM. Para <userID>, substitua o ID da identidade que obteve no passo anterior. 

```azurecli
az login --identity --username <userID>
```

Em seguida, autenticar para o registo com [início de sessão az acr][az-acr-login]. Quando executar este comando, a CLI utiliza o token do Active Directory que criou quando executou `az login` para autenticar facilmente a sua sessão com o registo de contentor. (Dependendo da configuração da VM, poderá ter de executar este comando e os comandos do docker com `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Deverá ver um `Login succeeded` mensagem. Em seguida, pode executar `docker` comandos sem fornecer credenciais. Por exemplo, execute [pull do docker] [ docker-pull] para solicitação de `aci-helloworld:v1` imagem, especificando o nome do servidor de início de sessão do seu registo. O nome do servidor de início de sessão consiste em seu nome de registo de contentor (letras minúsculas) seguido `.azurecr.io` – por exemplo, `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exemplo 2: Acesso com uma identidade atribuída de sistema

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Configurar a VM com uma identidade gerida pelo sistema

O seguinte procedimento [atribuir identidade da vm de az] [ az-vm-identity-assign] comando configura a VM do Docker com uma identidade atribuída de sistema:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Utilizar o [show de vm de az] [ az-vm-show] comando para definir uma variável para o valor de `principalId` (o ID principal de serviço) da identidade da VM, para utilizar em passos posteriores.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso de identidade para o registo de contentor

Agora configure a identidade para aceder ao seu registo de contentor. Utilizar pela primeira vez o [show do az acr] [ az-acr-show] comando para obter o ID de recurso do Registro:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Utilize o [criação da atribuição de função de az] [ az-role-assignment-create] comando para atribuir a função de AcrPull para a identidade. Esta função fornece [solicitar permissões](container-registry-roles.md) no Registro. Para fornecer os dois pull e permissões de push, atribua a função de ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Utilizar a identidade para aceder ao registo

SSH para a máquina virtual de Docker que está configurada com a identidade. Execute os seguintes comandos do CLI do Azure, com a CLI do Azure instalado na VM.

Autenticar em primeiro lugar, a CLI do Azure com [início de sessão az][az-login], usando a identidade atribuída de sistema na VM.

```azurecli
az login --identity
```

Em seguida, autenticar para o registo com [início de sessão az acr][az-acr-login]. Quando executar este comando, a CLI utiliza o token do Active Directory que criou quando executou `az login` para autenticar facilmente a sua sessão com o registo de contentor. (Dependendo da configuração da VM, poderá ter de executar este comando e os comandos do docker com `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Deverá ver um `Login succeeded` mensagem. Em seguida, pode executar `docker` comandos sem fornecer credenciais. Por exemplo, execute [pull do docker] [ docker-pull] para solicitação de `aci-helloworld:v1` imagem, especificando o nome do servidor de início de sessão do seu registo. O nome do servidor de início de sessão consiste em seu nome de registo de contentor (letras minúsculas) seguido `.azurecr.io` – por exemplo, `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu sobre a utilização de identidades geridas com o Azure Container Registry e como:

> [!div class="checklist"]
> * Ativar uma identidade de atribuído ao utilizador ou sistema atribuído em VM do Azure
> * Conceder o acesso de identidade para o Azure container registry
> * Utilizar a identidade gerida para aceder ao registo e solicitar uma imagem de contentor

* Saiba mais sobre [geridos identidades para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).


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
