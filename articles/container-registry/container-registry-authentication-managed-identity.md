---
title: Autenticar com a identidade gerida
description: Fornecer acesso a imagens no seu registo de contentores privados utilizando uma identidade Azure gerida atribuída pelo utilizador ou pelo sistema.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9a144f0e865cfc9bf857752eed65dbe5cda88bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253467"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Utilize uma identidade gerida a Azure para autenticar num registo de contentores Azure 

Utilize uma [identidade gerida para os recursos da Azure](../active-directory/managed-identities-azure-resources/overview.md) para autenticar num registo de contentores Azure a partir de outro recurso Azure, sem necessidade de fornecer ou gerir credenciais de registo. Por exemplo, crie uma identidade gerida atribuída ao utilizador ou atribuída pelo sistema num Linux VM para aceder a imagens de contentores do registo do seu contentor, tão facilmente quanto utiliza um registo público.

Para este artigo, você aprende mais sobre identidades geridas e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída ao sistema num VM Azure
> * Conceder o acesso à identidade a um registo de contentores Azure
> * Use a identidade gerida para aceder ao registo e puxe uma imagem de contentor 

Para criar os recursos Azure, este artigo requer que você execute a versão Azure CLI 2.0.55 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

Para configurar um registo de contentores e empurrar uma imagem de contentor para ele, você também deve ter Docker instalado localmente. O Docker disponibiliza pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Uma identidade gerida para os recursos Azure fornece aos serviços Azure uma identidade gerida automaticamente no Azure Ative Directory (Azure AD). Pode configurar [certos recursos Azure,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)incluindo máquinas virtuais, com uma identidade gerida. Em seguida, use a identidade para aceder a outros recursos Azure, sem passar credenciais em código ou scripts.

As identidades geridas são de dois tipos:

* *Identidades atribuídas ao utilizador*, que pode atribuir a múltiplos recursos e persistir durante o tempo que quiser. As identidades atribuídas ao utilizador estão atualmente em pré-visualização.

* Uma *identidade gerida pelo sistema*, que é única a um recurso específico como uma única máquina virtual e dura a vida útil desse recurso.

Depois de configurar um recurso Azure com uma identidade gerida, dê à identidade o acesso que pretende a outro recurso, tal como qualquer diretor de segurança. Por exemplo, atribuir a uma identidade gerida um papel com puxar, empurrar e puxar, ou outras permissões para um registo privado em Azure. (Para obter uma lista completa das funções de registo, consulte [as funções e permissões do Registo do Contentor de Azure](container-registry-roles.md).) Pode dar acesso identitário a um ou mais recursos.

Em seguida, utilize a identidade para autenticar qualquer serviço que suporte a [autenticação AZURE AD,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)sem quaisquer credenciais no seu código. Para utilizar a identidade para aceder a um registo de contentores Azure a partir de uma máquina virtual, autentica-se com o Gestor de Recursos Azure. Escolha como autenticar usando a identidade gerida, dependendo do seu cenário:

* [Adquirir um token de acesso Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programáticamente usando chamadas HTTP ou REST

* Use os [Azure SDKs](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Inscreva-se no Azure CLI ou no PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) com a identidade. 

## <a name="create-a-container-registry"></a>Criar um registo de contentor

Se ainda não tiver um registo de contentores Azure, crie um registo e empurre uma imagem de um recipiente de amostra para ele. Para etapas, consulte [Quickstart: Crie um registo de contentores privados utilizando o Azure CLI](container-registry-get-started-azure-cli.md).

Este artigo pressupõe que tem a imagem do `aci-helloworld:v1` recipiente armazenada no seu registo. Os exemplos usam um nome de registo do *myContainerRegistry*. Substitua-o pelo seu próprio registo e nomes de imagem em etapas posteriores.

## <a name="create-a-docker-enabled-vm"></a>Criar um VM ativado por Docker

Crie uma máquina virtual Ubuntu ativada pelo Docker. Também é necessário instalar o [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) na máquina virtual. Se já tem uma máquina virtual Azure, ignore este passo para criar a máquina virtual.

Implementar uma máquina virtual Ubuntu Azure padrão com [criação az vm][az-vm-create]. O exemplo a seguir cria um VM chamado *myDockerVM* num grupo de recursos existente chamado *myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando terminar, tome nota do `publicIpAddress` mostrado pelo Azure CLI. Utilize este endereço para fazer ligações SSH ao VM.

### <a name="install-docker-on-the-vm"></a>Instale o Docker no VM

Depois de o VM estar em funcionamento, faça uma ligação SSH ao VM. Substitua *o endereço públicoIpAddress* pelo endereço IP público do seu VM.

```bash
ssh azureuser@publicIpAddress
```

Executar o seguinte comando para instalar Docker no VM:

```bash
sudo apt update
sudo apt install docker.io -y
```

Após a instalação, executar o seguinte comando para verificar se o Docker está a funcionar corretamente no VM:

```bash
sudo docker run -it hello-world
```

Resultado:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga os passos na [Instalação Azure CLI com apta](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) a instalar o CLI Azure na sua máquina virtual Ubuntu. Para este artigo, certifique-se de que instala a versão 2.0.55 ou posterior.

Saia da sessão do SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Exemplo 1: Acesso com identidade atribuída ao utilizador

### <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade na sua subscrição utilizando o comando [de criação de identidade az.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Pode utilizar o mesmo grupo de recursos que usou anteriormente para criar o registo do contentor ou máquina virtual, ou um outro.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Para configurar a identidade nos seguintes passos, utilize o comando [de demonstração de identidade az][az-identity-show] para armazenar o ID de recursos da identidade e o ID principal de serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Porque precisa da identificação da identidade num passo posterior quando iniciar súm na CLI a partir da sua máquina virtual, mostre o valor:

```bash
echo $userID
```

O ID é do formulário:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Configure o VM com a identidade

O seguinte comando [de atribuição de identidade az vm][az-vm-identity-assign] configura o seu VM Docker com a identidade atribuída ao utilizador:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso identitário ao registo de contentores

Agora,confige a identidade para aceder ao seu registo de contentores. Primeiro use o comando [az acr show][az-acr-show] para obter o ID de recurso do registo:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Utilize [a atribuição de funções az criar][az-role-assignment-create] comando para atribuir o papel AcrPull ao registo. Esta função fornece [permissões de retirada](container-registry-roles.md) para o registo. Para fornecer permissões de puxar e empurrar, atribua o papel ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Use a identidade para aceder ao registo

SSH na máquina virtual docker que está configurada com a identidade. Executar os seguintes comandos Azure CLI, utilizando o CLI Azure instalado no VM.

Primeiro, autente para o CLI Azure com [login az,][az-login]utilizando a identidade configurada no VM. Para `<userID>` , substituir a identificação da identidade que recuperou num passo anterior. 

```azurecli
az login --identity --username <userID>
```

Em seguida, autentica para o registo com [login az acr][az-acr-login]. Quando utiliza este comando, o CLI utiliza o token Ative Directory criado quando correu `az login` para autenticar perfeitamente a sua sessão com o registo do contentor. (Dependendo da configuração do seu VM, poderá ter de executar este comando e comandos de estivador com `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Devia ver uma `Login succeeded` mensagem. Em seguida, pode executar `docker` comandos sem fornecer credenciais. Por exemplo, executar [o docker puxar][docker-pull] para puxar a `aci-helloworld:v1` imagem, especificando o nome do servidor de login do seu registo. O nome do servidor de login consiste no nome do registo do seu contentor (todos minúsculos) seguido de `.azurecr.io` - por exemplo, `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Exemplo 2: Acesso com identidade atribuída ao sistema

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Configure o VM com uma identidade gerida pelo sistema

O seguinte comando [de atribuição de identidade az vm][az-vm-identity-assign] configura o seu VM Docker com uma identidade atribuída ao sistema:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Utilize o comando [az vm show][az-vm-show] para definir uma variável ao valor `principalId` (o ID principal de serviço) da identidade do VM, para usar em etapas posteriores.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Conceder acesso identitário ao registo de contentores

Agora,confige a identidade para aceder ao seu registo de contentores. Primeiro use o comando [az acr show][az-acr-show] para obter o ID de recurso do registo:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Utilize [a atribuição de funções az criar][az-role-assignment-create] comando para atribuir o papel de AcrPull à identidade. Esta função fornece [permissões de retirada](container-registry-roles.md) para o registo. Para fornecer permissões de puxar e empurrar, atribua o papel ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Use a identidade para aceder ao registo

SSH na máquina virtual docker que está configurada com a identidade. Executar os seguintes comandos Azure CLI, utilizando o CLI Azure instalado no VM.

Em primeiro lugar, autente o CLI Azure com [login az,][az-login]utilizando a identidade atribuída ao sistema no VM.

```azurecli
az login --identity
```

Em seguida, autentica para o registo com [login az acr][az-acr-login]. Quando utiliza este comando, o CLI utiliza o token Ative Directory criado quando correu `az login` para autenticar perfeitamente a sua sessão com o registo do contentor. (Dependendo da configuração do seu VM, poderá ter de executar este comando e comandos de estivador com `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Devia ver uma `Login succeeded` mensagem. Em seguida, pode executar `docker` comandos sem fornecer credenciais. Por exemplo, executar [o docker puxar][docker-pull] para puxar a `aci-helloworld:v1` imagem, especificando o nome do servidor de login do seu registo. O nome do servidor de login consiste no nome do registo do seu contentor (todos minúsculos) seguido de `.azurecr.io` - por exemplo, `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```
> [!NOTE]
> As identidades de serviço geridas atribuídas pelo sistema podem ser usadas para interagir com ACRs e o Serviço de Aplicações pode usar identidades de serviço geridas atribuídas pelo sistema. No entanto, não é possível combiná-los, uma vez que o Serviço de Aplicações não pode utilizar o MSI para falar com um ACR. A única maneira é ativar o administrador no ACR e utilizar o nome de utilizador/palavra-passe de administrador.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu sobre a utilização de identidades geridas com o Registo de Contentores Azure e como:

> [!div class="checklist"]
> * Ativar uma identidade atribuída ao utilizador ou atribuída ao sistema num VM Azure
> * Conceder o acesso à identidade a um registo de contentores Azure
> * Use a identidade gerida para aceder ao registo e puxe uma imagem de contentor

* Saiba mais sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/index.yml)


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
