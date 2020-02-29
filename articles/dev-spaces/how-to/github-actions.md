---
title: Serviço GitHub Actions & Azure Kubernetes (pré-visualização)
services: azure-dev-spaces
ms.date: 02/04/2020
ms.topic: conceptual
description: Alterações de revisão e teste de um pedido de pull diretamente no Serviço Azure Kubernetes utilizando gitHub Actions e Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, GitHub Actions, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e2b6f47483d2c7b6c4970dac531bada1cecd279e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196557"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Serviço GitHub Actions & Azure Kubernetes (pré-visualização)

A Azure Dev Spaces fornece um fluxo de trabalho utilizando as Ações GitHub que lhe permite testar alterações a partir de um pedido de pull diretamente no AKS antes que o pedido de pull seja fundido no ramo principal do seu repositório. Ter uma aplicação em execução para rever alterações de um pedido de puxão pode aumentar a confiança tanto do desenvolvedor como dos membros da equipa. Esta aplicação de execução também pode ajudar os membros da equipa, tais como, gestores de produtos e designers, a tornarem-se parte do processo de revisão durante as fases iniciais de desenvolvimento.

Neste guia, vai aprender a:

* Instale o Azure Dev Spaces num aglomerado de Kubernetes gerido em Azure.
* Implemente uma grande aplicação com vários microserviços para um espaço de v.
* Configurar CI/CD com ações GitHub.
* Teste um único microserviço num espaço de v isolado no contexto da aplicação completa.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada][azure-cli-installed].
* [Leme 3 instalado.][helm-installed]
* Uma conta GitHub com [ações GitHub ativadas][github-actions-beta-signup].
* A aplicação de partilha de [bicicletas Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) bike sharing em execução num cluster AKS.

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

Criar um Registo de Contentores Azure (ACR):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> O nome o seu ACR deve ser único dentro do Azure e conter 5-50 caracteres alfanuméricos. Qualquer carta que use deve ser minúscula.

Guarde o valor do *loginServer* da saída porque é utilizado num passo posterior.

## <a name="create-a-service-principal-for-authentication"></a>Criar um serviço principal para autenticação

Use [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar um diretor de serviço. Por exemplo:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Guarde a saída JSON porque é usada num passo posterior.

Use [az aks show][az-aks-show] para mostrar a *identificação* do seu cluster AKS:

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Use [az acr show][az-acr-show] para exibir a *identificação* do ACR:

```cmd
az acr show --name <acrName> --query id
```

Utilize a criação de [atribuição de funções az][az-role-assignment-create] para dar ao *Colaborador* acesso ao seu cluster AKS e acesso *AcrPush* ao seu ACR.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Você deve ser o proprietário do seu cluster AKS e ACR para dar ao seu serviço principal acesso a esses recursos.

## <a name="configure-your-github-action"></a>Configure a sua ação GitHub

> [!IMPORTANT]
> Você deve ter GitHub Actions habilitados para o seu repositório. Para ativar as Ações GitHub para o seu repositório, navegue para o seu repositório no GitHub, clique no separador Ações e opte por ativar ações para este repositório.

Navegue para o seu repositório bifurcado e clique em *Definições*. Clique em *Segredos* na barra lateral esquerda. Clique *em Adicionar um novo segredo* para adicionar cada novo segredo abaixo:

1. *AZURE_CREDENTIALS:* toda a produção da criação principal do serviço.
1. *RESOURCE_GROUP*: o grupo de recursos para o seu cluster AKS, que neste exemplo é *o MyResourceGroup*.
1. *CLUSTER_NAME:* o nome do seu cluster AKS, que neste exemplo é *MyAKS*.
1. *CONTAINER_REGISTRY:* o *loginServer* para o ACR.
1. *HOST*: o anfitrião do seu Dev Space, que toma o formulário *<MASTER_SPACE><APP_NAME>.<HOST_SUFFIX>* que neste exemplo é *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET:* o nome do segredo que deseja utilizar, por exemplo, *demo-secreto*.
1. *MASTER_SPACE:* o nome do seu progenitor Dev Space, que neste exemplo é *dev*.
1. *REGISTRY_USERNAME*: o *clienteId* da saída JSON da criação principal do serviço.
1. *REGISTRY_PASSWORD*: o *clienteSecret* da saída JSON a partir da criação principal do serviço.

> [!NOTE]
> Todos estes segredos são usados pela ação GitHub e estão configurados em [.github/workflows/bikes.yml][github-action-yaml].

Opcionalmente, se quiser atualizar o espaço principal após a fusão do seu PR, adicione o *GATEWAY_HOST* segredo, que assume o formulário *<MASTER_SPACE>.gateway.<HOST_SUFFIX>* que neste exemplo é *dev.gateway.fedcab0987.eus.azds.io*. Assim que fundir as suas alterações no ramo principal do garfo, outra ação correrá para reconstruir e executar toda a sua aplicação no espaço master dev. Neste exemplo, o espaço principal é *dev*. Esta ação está configurada em [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Criar um novo ramo para alterações de código

Navegue para `BikeSharingApp/` e crie um novo ramo chamado *bike-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Editar [Bicicletas/servidor.js][bikes-server-js] para remover as linhas 232 e 233:

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

Guarde o ficheiro e utilize `git add` e `git commit` para encenar as suas alterações.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Empurre as suas alterações

Use `git push` para empurrar o seu novo ramo para o seu repositório bifurcado:

```cmd
git push origin bike-images
```

Depois de o empurrão estar completo, navegue até ao seu repositório bifurcado no GitHub para criar um pedido de puxão com o ramo *principal* no seu repositório bifurcado como o ramo base em comparação com o ramo de *imagens de bicicleta.*

Depois de aberto o seu pedido de puxão, navegue para o separador *Ações.* Verifique se iniciou uma nova ação e está a construir o serviço *Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Veja o espaço da criança com as suas mudanças

Depois de concluída a ação, verá um comentário com uma URL para o seu novo espaço infantil baseado nas alterações no pedido de puxão.

> [!div class="mx-imgBorder"]
> ![Url de Ação GitHub](../media/github-actions/github-action-url.png)

Navegue para o serviço *bikesharingweb* abrindo o URL a partir do comentário. Selecione *Aurelia Briggs (cliente)* como utilizador e, em seguida, selecione uma bicicleta para alugar. Verifique se já não vê a imagem do espaço reservado para a bicicleta.

Se fundir as suas alterações no ramo *principal* do garfo, outra ação irá correr para reconstruir e executar toda a sua aplicação no espaço de dev dos pais. Neste exemplo, o espaço dos pais é *dev*. Esta ação está configurada em [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Limpe os seus recursos Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como o Azure Dev Spaces o ajuda a desenvolver aplicações mais complexas em vários recipientes e como pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou ramos do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipa em Espaços Azure Dev][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
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
[team-quickstart]: ../quickstart-team-development.md
