---
title: Ações do GitHub & serviço kubernetes do Azure
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Revise e teste alterações de uma solicitação pull diretamente no serviço kubernetes do Azure usando ações e Azure Dev Spaces do GitHub.
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, ações do GitHub, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 09dc9440628ac5d808f90d086bd88e4f90765c28
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889739"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Ações do GitHub & serviço kubernetes do Azure (versão prévia)

Azure Dev Spaces fornece um fluxo de trabalho usando ações do GitHub que permitem testar alterações de uma solicitação pull diretamente no AKS antes que a solicitação pull seja mesclada no Branch principal do repositório. Ter um aplicativo em execução para examinar as alterações de uma solicitação de pull pode aumentar a confiança do desenvolvedor, bem como de membros da equipe. Esse aplicativo em execução também pode ajudar os membros da equipe, como gerentes de produto e designers, a se tornarem parte do processo de revisão durante os primeiros estágios de desenvolvimento.

Neste guia, vai aprender a:

* Configure Azure Dev Spaces em um cluster kubernetes gerenciado no Azure.
* Implante um aplicativo grande com vários microserviços em um espaço de desenvolvimento.
* Configure CI/CD com ações do GitHub.
* Teste um único microserviço em um espaço de desenvolvimento isolado dentro do contexto do aplicativo completo.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure instalada][azure-cli-installed].
* [Helm 2,13 ou superior instalado][helm-installed].
* Uma conta do GitHub com [ações do GitHub habilitadas][github-actions-beta-signup].
* O [aplicativo de exemplo de compartilhamento de bicicletas de Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) em execução em um cluster AKs.

## <a name="create-an-azure-container-registry"></a>Criar um Azure Container Registry

Criar um ACR (registro de contêiner do Azure):

```cmd
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> O nome que seu ACR deve ser exclusivo no Azure e contém 5-50 caracteres alfanuméricos. Qualquer letra que você usar deve estar em letras minúsculas.

Salve o valor de *loginServer* da saída porque ele é usado em uma etapa posterior.

## <a name="create-a-service-principal-for-authentication"></a>Criar uma entidade de serviço para autenticação

Use [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] para criar uma entidade de serviço. Por exemplo:

```cmd
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Salve a saída JSON porque ela é usada em uma etapa posterior.


Use [AZ AKs show][az-aks-show] para exibir a *ID* do seu cluster AKs:

```cmd
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Use [AZ ACR show][az-acr-show] para exibir a *ID* do ACR:

```cmd
az acr show --name <acrName> --query id
```

Use [AZ role Assignment Create][az-role-assignment-create] para fornecer acesso de *colaborador* ao seu cluster AKs e acesso *AcrPush* ao seu ACR.

```cmd
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Você deve ser o proprietário do cluster AKS e do ACR para dar acesso à entidade de serviço a esses recursos.

## <a name="configure-your-github-action"></a>Configurar sua ação do GitHub

> [!IMPORTANT]
> Você deve ter as ações do GitHub habilitadas para seu repositório. Para habilitar as ações do GitHub para seu repositório, navegue até o repositório no GitHub, clique na guia ações e escolha habilitar ações para este repositório.

Navegue até o repositório bifurcado e clique em *configurações*. Clique em *segredos* na barra lateral esquerda. Clique em *Adicionar um novo segredo* para adicionar cada novo segredo abaixo:

1. *AZURE_CREDENTIALS*: toda a saída da criação da entidade de serviço.
1. *RESOURCE_GROUP*: o grupo de recursos para o cluster AKs, que neste exemplo é *MyResource*Group.
1. *CLUSTER_NAME*: o nome do seu cluster AKs, que neste exemplo é *MyAKS*.
1. *CONTAINER_REGISTRY*: o *LOGINSERVER* para o ACR.
1. *Host*: o host do seu espaço de desenvolvimento, que tem o formato *< MASTER_SPACE >. < APP_NAME >. < HOST_SUFFIX*>, que neste exemplo é *dev.bikesharingweb.fedcab0987.eus.azds.Io*.
1. *HOST_SUFFIX*: o sufixo de host para seu espaço de desenvolvimento, que neste exemplo é *fedcab0987.eus.azds.Io*.
1. *IMAGE_PULL_SECRET*: o nome do segredo que você deseja usar, por exemplo, o *segredo de demonstração*.
1. *MASTER_SPACE*: o nome do seu espaço de desenvolvimento pai, que neste exemplo é um *desenvolvedor*.
1. *REGISTRY_USERNAME*: o *CLIENTID* da saída JSON da criação da entidade de serviço.
1. *REGISTRY_PASSWORD*: o *CLIENTSECRET* da saída JSON da criação da entidade de serviço.

> [!NOTE]
> Todos esses segredos são usados pela ação do GitHub e são configurados em [. github/workflows/bikes. yml][github-action-yaml].

## <a name="create-a-new-branch-for-code-changes"></a>Criar uma nova ramificação para alterações de código

Navegue até `BikeSharingApp/` e crie uma nova ramificação chamada *imagens de bicicleta*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Editar [bicicletas/Server. js][bikes-server-js] para remover as linhas 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A seção agora deve ser semelhante a:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salve o arquivo e, em seguida, use `git add` e `git commit` para preparar suas alterações.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Enviar por push suas alterações

Use `git push` para enviar por push seu novo Branch para o repositório bifurcado:

```cmd
git push origin bike-images
```

Depois que o envio por push for concluído, navegue até o repositório bifurcado no GitHub para criar uma solicitação de pull com a Branch *mestre* no repositório bifurcado como a ramificação de base em comparação com a ramificação de *imagens de bicicletas* .

Depois que sua solicitação de pull for aberta, navegue até a guia *ações* . Verifique se uma nova ação foi iniciada e se está criando o serviço *bicicletas* .

## <a name="view-the-child-space-with-your-changes"></a>Exibir o espaço filho com suas alterações

Depois que a ação for concluída, você verá um comentário com uma URL para o novo espaço filho com base nas alterações na solicitação de pull.

> [!div class="mx-imgBorder"]
> ![URL de ação do GitHub](../media/github-actions/github-action-url.png)

Navegue até o serviço *bikesharingweb* abrindo a URL do comentário. Selecione *Aurelia Briggs (cliente)* como o usuário e, em seguida, selecione uma bicicleta para alugar. Verifique se você não vê mais a imagem de espaço reservado para a bicicleta.

Se você mesclar suas alterações no Branch *mestre* na bifurcação, outra ação será executada para recompilar e executar todo o aplicativo no espaço de desenvolvimento pai. Neste exemplo, o espaço pai é *dev*. Essa ação está configurada em [. github/workflows/bikesharing. yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Saiba como Azure Dev Spaces ajuda a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com diferentes versões ou branches do seu código em espaços diferentes.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe no Azure Dev Spaces][team-quickstart]

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
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[tiller-rbac]: https://helm.sh/docs/using_helm/#role-based-access-control
[supported-regions]: ../about.md#supported-regions-and-configurations
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
