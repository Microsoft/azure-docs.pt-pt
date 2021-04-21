---
title: GitHub Actions & Serviço Azure Kubernetes (pré-visualização)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Rever e testar alterações a partir de um pedido de pull diretamente no Serviço Azure Kubernetes usando GitHub Actions e Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, GitHub Actions, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777550"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & Serviço Azure Kubernetes (pré-visualização)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

A Azure Dev Spaces fornece um fluxo de trabalho utilizando as Ações GitHub que permite testar alterações a partir de um pedido de puxar diretamente em AKS antes que o pedido de puxar seja fundido no ramo principal do seu repositório. Ter uma aplicação em execução para rever as alterações de um pedido de pull pode aumentar a confiança tanto do desenvolvedor como dos membros da equipa. Esta aplicação de execução também pode ajudar membros da equipa, tais como gestores de produtos e designers, a tornarem-se parte do processo de revisão durante as fases iniciais do desenvolvimento.

Neste guia, vai aprender a:

* Instale Azure Dev Spaces num cluster gerido de Kubernetes em Azure.
* Implemente uma grande aplicação com múltiplos microserviços para um espaço dev.
* Configurar CI/CD com ações do GitHub.
* Teste um único microserviço num espaço dev isolado no contexto da aplicação completa.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita.](https://azure.microsoft.com/free)
* A [CLI do Azure instalada][azure-cli-installed].
* [Leme 3 instalado][helm-installed].
* Uma conta GitHub com [ações gitHub ativadas][github-actions-beta-signup].
* A [aplicação de amostra de partilha de bicicletas Azure Dev Spaces em](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) execução num cluster AKS.

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

Criar um registo de contentores Azure (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> O nome do seu ACR deve ser único dentro de Azure e conter 5-50 caracteres alfanuméricos. Todas as letras que utilizar devem ser mais baixas.

Guarde o valor do *loginServer* da saída porque é utilizado num passo posterior.

## <a name="create-a-service-principal-for-authentication"></a>Criar um principal de serviço para a autenticação

Use [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar um diretor de serviço. Por exemplo:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Guarde a saída JSON porque é utilizada num passo posterior.

Use [az aks show][az-aks-show] para exibir o *ID* do seu cluster AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Utilize [o az acr show][az-acr-show] para exibir o *ID* do ACR:

```azurecli
az acr show --name <acrName> --query id
```

Utilize [a az role assignment create][az-role-assignment-create] para dar ao *Colaborador* acesso ao seu cluster AKS e acesso *AcrPush* ao seu ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Você deve ser o proprietário do seu cluster AKS e ACR de forma a dar ao seu serviço acesso principal a esses recursos.

## <a name="configure-your-github-action"></a>Configure a sua ação GitHub

> [!IMPORTANT]
> Você deve ter GitHub Actions habilitado para o seu repositório. Para ativar as Ações do GitHub para o seu repositório, navegue até ao seu repositório no GitHub, clique no separador Ações e escolha ativar ações para este repositório.

Navegue para o seu repositório forcado e clique em *Definições*. Clique em *Segredos* na barra lateral esquerda. Clique *Em Adicionar um novo segredo* para adicionar cada novo segredo abaixo:

1. *AZURE_CREDENTIALS:* toda a produção da criação principal do serviço.
1. *RESOURCE_GROUP*: o grupo de recursos para o seu cluster AKS, que neste exemplo é *o MyResourceGroup*.
1. *CLUSTER_NAME:* o nome do seu cluster AKS, que neste exemplo é *MyAKS*.
1. *CONTAINER_REGISTRY:* o *loginServer* para o ACR.
1. *ANFITRIÃO*: o anfitrião do seu Espaço Dev, que assume a forma *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>,* que neste exemplo é *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET:* o nome do segredo que pretende utilizar, por exemplo, *demo-secret*.
1. *MASTER_SPACE:* o nome do seu pai Dev Space, que neste exemplo é *dev*.
1. *REGISTRY_USERNAME:* o *clienteId* da saída JSON da criação principal do serviço.
1. *REGISTRY_PASSWORD:* o *clienteSecret* da saída JSON da criação principal do serviço.

> [!NOTE]
> Todos estes segredos são usados pela ação do GitHub e são configurados em [.github/workflows/bikes.yml][github-action-yaml].

Opcionalmente, se pretender atualizar o espaço principal após a fusão do seu PR, adicione o *segredo GATEWAY_HOST,* que assume a forma *<MASTER_SPACE>.gateway.<HOST_SUFFIX>,* que neste exemplo é *dev.gateway.fedcab0987.eus.azds.io*. Assim que fundir as suas mudanças no ramo principal no seu garfo, outra ação será executada para reconstruir e executar toda a sua aplicação no espaço master dev. Neste exemplo, o espaço principal é *dev.* Esta ação está configurada em [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Além disso, se quiser que as alterações no seu Pr decorram num espaço de neto, atualize os *segredos MASTER_SPACE* e *HOST.* Por exemplo, se a sua aplicação estiver em execução em *dev* com um espaço infantil *dev/azureuser1,* para que o PR seja executado num espaço infantil *de dev/azureuser1*:

* Atualize *MASTER_SPACE* para o espaço infantil que deseja como espaço dos pais, neste exemplo *azureuser1*.
* Atualizar *HOST* para *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, neste exemplo *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Criar um novo ramo para alterações de código

Navegue `BikeSharingApp/` para criar um novo ramo chamado *bike-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Editar [Bicicletas/server.js][bikes-server-js] para remover as linhas 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A secção deve agora parecer:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Guarde o ficheiro e use `git add` e en stage as suas `git commit` alterações.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Empurre as suas alterações

Use `git push` para empurrar o seu novo ramo para o seu repositório forcado:

```cmd
git push origin bike-images
```

Depois de o empurrão estar completo, navegue até ao seu repositório de garfos no GitHub para criar um pedido de puxar com o ramo *principal* no seu repositório de garfos como ramo base em comparação com o ramo *de imagens de bicicleta.*

Após a abertura do seu pedido de puxar, navegue para o *separador Ações.* Verifique se uma nova ação foi iniciada e está a construir o serviço *Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Veja o espaço da criança com as suas alterações

Após a conclusão da ação, verá um comentário com um URL ao seu novo espaço infantil baseado nas alterações no pedido de puxar.

> [!div class="mx-imgBorder"]
> ![Url de ação gitHub](../media/github-actions/github-action-url.png)

Navegue para o serviço *de bikesharingweb* abrindo o URL a partir do comentário. Selecione *Aurelia Briggs (cliente)* como utilizador e, em seguida, selecione uma bicicleta para alugar. Verifique se já não vê a imagem do espaço reservado para a bicicleta.

Se fundir as suas mudanças no ramo *principal* no seu garfo, outra ação será executada para reconstruir e executar toda a sua aplicação no espaço dos pais. Neste exemplo, o espaço dos pais é *dev.* Esta ação está configurada em [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
