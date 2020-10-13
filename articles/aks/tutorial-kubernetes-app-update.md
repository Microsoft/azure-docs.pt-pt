---
title: Tutorial do Kubernetes no Azure - Atualizar uma aplicação
description: Neste tutorial do Azure Kubernetes Service (AKS), saiba como atualizar uma implementação de aplicação existente no AKS com uma nova versão do código da aplicação.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 6619d7c9f7d2b0cd78f3941ce431e80342b6f563
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576443"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Tutorial: Atualizar uma aplicação no Serviço Kubernetes do Azure (AKS)

Depois de a aplicação ser implementada no Kubernetes, pode ser atualizada, ao especificar uma nova imagem de contentor ou uma versão de imagem. Uma atualização é encenada de modo a que apenas uma parte da implementação seja atualizada ao mesmo tempo. Esta atualização testada permite que a aplicação continue a ser executada durante a atualização. Também apresenta um mecanismo de reversão se ocorrer uma falha de implementação.

Neste tutorial, parte seis de sete, a aplicação Azure Vote de exemplo é atualizada. Saiba como:

> [!div class="checklist"]
> * Atualizar o código da aplicação de front-end
> * Criar uma imagem de contentor atualizada
> * Colocar a imagem de contentor no Azure Container Registry
> * Implementar a imagem de contentor atualizada

## <a name="before-you-begin"></a>Antes de começar

Em tutoriais anteriores, uma aplicação foi embalada numa imagem de contentor. Esta imagem foi enviada para o Registo de Contentores Azure, e você criou um cluster AKS. A aplicação foi então implantada no cluster AKS.

Também foi clonado um repositório de aplicações que inclui o código de origem da aplicação e foi utilizado um ficheiro do Docker Compose pré-criado neste tutorial. Verifique se criou um clone do repo, e mudou os diretórios para o diretório clonado. Se ainda não completou estes passos e quiser seguir em frente, comece com [tutorial 1 – Crie imagens de contentores.][aks-tutorial-prepare-app]

Este tutorial requer que esteja a executar a versão 2.0.53 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="update-an-application"></a>Atualizar uma aplicação

Vamos fazer uma alteração à aplicação de exemplo e, em seguida, atualizar a versão já implementada no cluster do AKS. Certifique-se de que está no diretório clonado *de azure-vote-app-redis.* O código-fonte de aplicação da amostra pode então ser encontrado dentro do diretório *de voto azure.* Abra o ficheiro *config_file.cfg* com um editor, como o `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Altere os valores *de VOTE1VALUE* e *VOTE2VALUE* para diferentes valores, como cores. O exemplo a seguir mostra os valores atualizados:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Guarde e feche o ficheiro. Em `vi` , use `:wq` .

## <a name="update-the-container-image"></a>Atualizar a imagem de contentor

Para voltar a criar a imagem de front-end e testar a aplicação atualizada, utilize [docker-compose][docker-compose]. O argumento `--build` serve para instruir o Docker Compose para voltar a criar a imagem de aplicação:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Testar a aplicação localmente

Para verificar se a imagem de contentor atualizada mostra as alterações, abra um browser local em `http://localhost:8080`.

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated.png" alt-text="Screenshot mostrando um exemplo da aplicação de voto azure de imagem de recipiente atualizada abriu com um navegador local e anfitrião local.":::

Os valores atualizados fornecidos no ficheiro *config_file.cfg* são apresentados na sua aplicação de execução.

## <a name="tag-and-push-the-image"></a>Etiquetar e enviar a imagem

Para utilizar corretamente a imagem atualizada, etiquete a imagem *azure-vote-front* com o nome do servidor de início de sessão do registo do ACR. Obtenha o nome do servidor de login com o comando [da lista az acr:](/cli/azure/acr)

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize a [etiqueta do docker][docker-tag] para etiquetar a imagem. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão do ACR ou pelo nome de anfitrião do registo público e atualize a versão da imagem para *:v2* da seguinte forma:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v2
```

Agora, utilize [docker push][docker-push] para carregar a imagem para o registo. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão do ACR.

> [!NOTE]
> Se sentir problemas empurrando para o seu registo ACR, certifique-se de que ainda está iniciado. Executar o comando [de login az acr][az-acr-login] usando o nome do seu Registo de Contentores Azure que criou no passo de Registo de [Contentores Azure.](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) Por exemplo, `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Implementar a aplicação atualizada

Para fornecer o máximo de tempo de funcionamento, várias instâncias da cápsula de aplicação devem estar em execução. Verifique se o número de instâncias de front-end em execução com o comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se não tiver várias cápsulas frontais, dimensione a colocação *da frente de voto azul* da seguinte forma:

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

Agora abra um navegador web local para o endereço IP do seu serviço:

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated-external.png" alt-text="Screenshot mostrando um exemplo da aplicação de voto azure de imagem de recipiente atualizada abriu com um navegador local e anfitrião local.":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, atualizou uma aplicação e lançou esta atualização para o seu cluster AKS. Aprendeu a:

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
