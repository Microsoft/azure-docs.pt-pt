---
title: Tutorial do Kubernetes no Azure - Atualizar uma aplicação
description: Neste tutorial do Azure Kubernetes Service (AKS), saiba como atualizar uma implementação de aplicação existente no AKS com uma nova versão do código da aplicação.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cefbd4f16902b3a6816ebb4e818a81b18ba00d54
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995567"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Tutorial: Atualizar uma aplicação no Azure Kubernetes Service (AKS)

Depois de a aplicação ser implementada no Kubernetes, pode ser atualizada, ao especificar uma nova imagem de contentor ou uma versão de imagem. Uma atualização é testada, para que apenas uma parte da implantação é atualizada ao mesmo tempo. Esta atualização testada permite que a aplicação continue a ser executada durante a atualização. Também apresenta um mecanismo de reversão se ocorrer uma falha de implementação.

Neste tutorial, parte seis de sete, a aplicação Azure Vote de exemplo é atualizada. Saiba como:

> [!div class="checklist"]
> * Atualizar o código da aplicação de front-end
> * Criar uma imagem de contentor atualizada
> * Colocar a imagem de contentor no Azure Container Registry
> * Implementar a imagem de contentor atualizada

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor. A imagem foi carregada para o Azure Container Registry e criado um cluster do AKS. A aplicação foi implementada, em seguida, para o cluster do AKS.

Também foi clonado um repositório de aplicações que inclui o código de origem da aplicação e foi utilizado um ficheiro do Docker Compose pré-criado neste tutorial. Certifique-se de que criou um clone do repositório e que foram alterados diretórios no diretório clonado. Se ainda não concluiu estes passos e pretende acompanhar, inicie com [Tutorial 1 – criar imagens de contentor][aks-tutorial-prepare-app].

Este tutorial requer que está a executar a CLI do Azure versão 2.0.53 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="update-an-application"></a>Atualizar uma aplicação

Vamos fazer uma alteração à aplicação de exemplo e, em seguida, atualizar a versão já implementada no cluster do AKS. Certifique-se de que está no clonado *azure-Vote-aplicação-redis* diretório. O código de origem do aplicativo de exemplo, em seguida, pode ser encontrado no interior da *azure-vote* diretório. Abra o ficheiro *config_file.cfg* com um editor, como o `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Alterar os valores para *VOTE1VALUE* e *VOTE2VALUE* para valores diferentes, como cores. O exemplo seguinte mostra os valores atualizados:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Guarde e feche o ficheiro. Na `vi`, utilize `:wq`.

## <a name="update-the-container-image"></a>Atualizar a imagem de contentor

Para voltar a criar a imagem de front-end e testar a aplicação atualizada, utilize [docker-compose][docker-compose]. O argumento `--build` serve para instruir o Docker Compose para voltar a criar a imagem de aplicação:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Testar a aplicação localmente

Para verificar se a imagem de contentor atualizada mostra as alterações, abra um browser local em `http://localhost:8080`.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

Os valores atualizados fornecidos a *config_file.cfg* arquivo são apresentados na sua aplicação em execução.

## <a name="tag-and-push-the-image"></a>Etiquetar e enviar a imagem

Para utilizar corretamente a imagem atualizada, etiquete a imagem *azure-vote-front* com o nome do servidor de início de sessão do registo do ACR. Obtenha o nome do servidor de início de sessão com o comando [az acr list](/cli/azure/acr):

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize a [etiqueta do docker][docker-tag] para etiquetar a imagem. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão do ACR ou pelo nome de anfitrião do registo público e atualize a versão da imagem para *:v2* da seguinte forma:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v2
```

Agora, utilize [docker push][docker-push] para carregar a imagem para o registo. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão do ACR. Se ocorrerem problemas ao enviar para o registo do ACR, certifique-se de que executou a [início de sessão az acr] [ az-acr-login] comando.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Implementar a aplicação atualizada

Para fornecer o máximo de tempo de atividade, tem de executar várias instâncias do pod da aplicação. Verifique se o número de instâncias de front-end em execução com o comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se não tiver vários pods de front-end, dimensionar o *azure-vote-front* implementação da seguinte forma:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Para atualizar a aplicação, utilize o comando [kubectl set][kubectl-set]. Atualize `<acrLoginServer>` com o nome do anfitrião ou do servidor de início de sessão do registo de contentor e especifique a versão da aplicação *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Para monitorizar a implementação, utilize o comando [kubectl get pod][kubectl-get]. Uma vez que a aplicação atualizada é implementada, os seus pods são terminados e recriados com a nova imagem de contentor.

```console
kubectl get pods
```

O resultado de exemplo seguinte mostra os pods a serem terminados e as novas instâncias em execução à medida que a implementação avança:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Testar a aplicação atualizada

Para ver a aplicação atualizada, obtenha primeiro o endereço IP externo do serviço `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Agora, abra um navegador da local web para o endereço IP do seu serviço:

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, atualizou uma aplicação e implementa esta atualização para o seu cluster do AKS. Aprendeu a:

> [!div class="checklist"]
> * Atualizar o código da aplicação de front-end
> * Criar uma imagem de contentor atualizada
> * Colocar a imagem de contentor no Azure Container Registry
> * Implementar a imagem de contentor atualizada

Avance para o próximo tutorial para saber como atualizar um cluster do AKS para uma nova versão do Kubernetes.

> [!div class="nextstepaction"]
> [Atualizar Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
