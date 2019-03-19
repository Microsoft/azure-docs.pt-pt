---
title: (PRETERIDO) Tutorial do Azure Container Service – atualizar aplicação
description: Tutorial do Azure Container Service – Atualizar Aplicação
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 3c0be935a4ffb51c03d2f63b14ab7c0c713dd2ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006259"
---
# <a name="deprecated-update-an-application-in-kubernetes"></a>(PRETERIDO) Atualizar uma aplicação no Kubernetes

> [!TIP]
> Para a versão atualizada neste tutorial que utiliza o Azure Kubernetes Service, consulte [Tutorial: Atualizar uma aplicação no Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-app-update.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Depois de a aplicação ser implementada no Kubernetes, pode ser atualizada, ao especificar uma nova imagem de contentor ou uma versão de imagem. Ao fazê-lo, a atualização é testada, para que apenas uma parte da implementação seja atualizada em simultâneo. Esta atualização testada permite que a aplicação continue a ser executada durante a atualização. Também apresenta um mecanismo de reversão se ocorrer uma falha de implementação. 

Neste tutorial, parte seis de sete, a aplicação Azure Vote de exemplo é atualizada. As tarefas que concluir incluem:

> [!div class="checklist"]
> * Atualizar o código da aplicação de front-end
> * Criar uma imagem de contentor atualizada
> * Enviar a imagem de contentor para o Azure Container Registry
> * Implementar a imagem de contentor atualizada

Nos tutoriais subsequentes, o Log Analytics está configurado para monitorizar o cluster de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor, carregada a imagem para o Azure Container Registry e criado um cluster de Kubernetes. A aplicação foi, em seguida, executada no cluster de Kubernetes. 

Também foi clonado um repositório de aplicações que inclui o código de origem da aplicação e foi utilizado um ficheiro do Docker Compose pré-criado neste tutorial. Certifique-se de que criou um clone do repositório e que foram alterados diretórios no diretório clonado. No interior está um diretório denominado `azure-vote` e um ficheiro denominado `docker-compose.yml`.

Se ainda não concluiu estes passos e pretende acompanhar, regresse ao [Tutorial 1 – Criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Atualizar a aplicação

Para este tutorial, é realizada uma alteração na aplicação e a aplicação atualizada implementada no cluster Kubernetes. 

O código de origem da aplicação pode ser encontrado no interior do diretório `azure-vote`. Abra o ficheiro `config_file.cfg` com qualquer editor de texto ou de código. Neste exemplo é utilizado `vi`.

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Altere os valores de `VOTE1VALUE` e `VOTE2VALUE`, e guarde o ficheiro.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Guarde e feche o ficheiro.

## <a name="update-container-image"></a>Atualizar imagem de contentor

Utilize [docker-compose](https://docs.docker.com/compose/) para voltar a criar a imagem de front-end e execute a aplicação atualizada. O argumento `--build` serve para instruir o Docker Compose para voltar a criar a imagem de aplicação.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Testar a aplicação localmente

Procure o `http://localhost:8080` para ver a aplicação atualizada.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Imagens de etiqueta e push

Assinale a imagem `azure-vote-front` com o loginServer do registo de contentor. 

Obtenha o nome do servidor de início de sessão com o comando [az acr list](/cli/azure/acr#az-acr-list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Utilize a [etiqueta do docker](https://docs.docker.com/engine/reference/commandline/tag/) para assinalar a imagem. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão do Azure Container Registry ou o nome do anfitrião público do registo. Também tenha em atenção que a versão da imagem é atualizada para `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Utilize o [push do docker](https://docs.docker.com/engine/reference/commandline/push/) para carregar a imagem para o seu registo. Substitua `<acrLoginServer>` pelo nome do servidor de início de sessão do Azure Container Registry.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Implementar atualização da aplicação

Para garantir o máximo tempo de atividade, tem de executar várias instâncias do pod da aplicação. Verifique esta configuração com o comando [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get).

```bash
kubectl get pod
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se não tiver vários pods em execução na imagem azure-vote-front, dimensione a implementação `azure-vote-front`.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Para atualizar a aplicação, utilize o comando [kubectl set](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set). Atualize `<acrLoginServer>` com o nome do anfitrião ou o servidor de início de sessão do seu registo de contentor.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Para monitorizar a implementação, utilize o comando [kubectl get pod](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get). Uma vez que a aplicação atualizada é implementada, os seus pods são terminados e recriados com a nova imagem de contentor.

```azurecli-interactive
kubectl get pod
```

Saída:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testar a aplicação atualizada

Obtenha o endereço IP externo do serviço `azure-vote-front`.

```azurecli-interactive
kubectl get service azure-vote-front
```

Procure o endereço IP para ver a aplicação atualizada.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, atualiza uma aplicação e implementa esta atualização num cluster de Kubernetes. Foram realizadas as seguintes tarefas:

> [!div class="checklist"]
> * Atualizou o código da aplicação de front-end
> * Criou uma imagem de contentor atualizada
> * Enviou a imagem de contentor para o Azure Container Registry
> * Implementou a aplicação atualizada

Avance para o próximo tutorial para saber mais sobre como monitorizar o Kubernetes com o Log Analytics.

> [!div class="nextstepaction"]
> [Monitorizar o Kubernetes com o Log Analytics (Monitor Kubernetes with Log Analytics)](./container-service-tutorial-kubernetes-monitor.md)