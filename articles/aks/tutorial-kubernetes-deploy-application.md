---
title: Tutorial do Kubernetes no Azure – Implementar uma aplicação
description: Neste tutorial do Azure Kubernetes Service (AKS), implemente uma aplicação de vários contentores no seu cluster com uma imagem personalizada armazenada no Azure Container Registry.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: c579aed1acb555a82d5a04308ab4e68f4bfecb8c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305280"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Tutorial: Executar aplicações no Azure Kubernetes Service (AKS)

O Kubernetes dispõe de uma plataforma distribuída para aplicações em contentores. Cria e implementa as suas próprias aplicações e serviços num cluster do Kubernetes, e permite que o cluster gira a disponibilidade e a conectividade. Neste tutorial, parte quatro de sete, a aplicação de exemplo é implementada num cluster do Kubernetes. Saiba como:

> [!div class="checklist"]
> * Atualizar ficheiros de manifesto Kubernetes
> * Executar uma aplicação no Kubernetes
> * Testar a aplicação

Nos tutoriais adicionais, esta aplicação está aumentada horizontalmente e atualizada.

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [Kubernetes principais conceitos para o Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor, a imagem foi carregada para o Azure Container Registry e foi criado um cluster de Kubernetes.

Para concluir este tutorial, precisa do ficheiro de manifesto previamente criado do Kubernetes `azure-vote-all-in-one-redis.yaml`. Este ficheiro foi transferido com o código de origem da aplicação num tutorial anterior. Certifique-se de que já clonou o repositório e que foram alterados diretórios no repositório clonado. Se ainda não concluiu estes passos e pretende acompanhar, inicie com [Tutorial 1 – criar imagens de contentor][aks-tutorial-prepare-app].

Este tutorial requer que está a executar a CLI do Azure versão 2.0.53 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="update-the-manifest-file"></a>Atualizar o ficheiro de manifesto

Nestes tutoriais, uma instância do Azure Container Registry (ACR) armazena a imagem de contentor no exemplo de aplicação. Para implementar a aplicação, tem de atualizar o nome da imagem no ficheiro de manifesto do Kubernetes para incluir o nome de servidor de início de sessão do ACR.

Obtenha o nome do servidor de início de sessão ACR com o comando [az acr list][az-acr-list], da seguinte forma:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O ficheiro de manifesto de exemplo do repositório git clonado no primeiro tutorial utiliza o nome do servidor de início de sessão da *microsoft*. Certifique-se de que está no clonado *azure-Vote-aplicação-redis* diretório, em seguida, abra o manifesto do ficheiro com um editor de texto, como `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Substitua *microsoft* pelo seu nome de servidor de início de sessão do ACR. O nome da imagem for encontrado na linha 51 do ficheiro de manifesto. O seguinte exemplo mostra o nome da imagem predefinida:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Indique o seu próprio nome de servidor de início de sessão do ACR para que o ficheiro de manifesto seja semelhante ao seguinte exemplo:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Guarde e feche o ficheiro. Na `vi`, utilize `:wq`.

## <a name="deploy-the-application"></a>Implementar a aplicação

Para implementar a sua aplicação, utilize o comando [kubectl apply][kubectl-apply]. Este comando analisa o ficheiro de manifesto e cria os objetos de Kubernetes definidos. Especifique o ficheiro de manifesto de exemplo, conforme mostrado no exemplo a seguir:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

O resultado de exemplo seguinte mostra os recursos criados com êxito o cluster do AKS:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando o aplicativo for executado, um serviço do Kubernetes expõe à aplicação de front-end à internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL-IP* para o *do azure-vote-front* serviço é mostrado como *pendente*:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Quando o *EXTERNAL-IP* é alterado de endereço *pendente* para um endereço IP público real, utilize `CTRL-C` para parar o `kubectl` veja processo. O resultado de exemplo seguinte mostra um endereço IP público válido atribuído ao serviço:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Para ver a aplicação em ação, abra um navegador da web para o endereço IP externo do seu serviço:

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Se o aplicativo não carregou, poderá ser devido a um problema de autorização com o registo de imagem. Para ver o estado dos seus contentores, utilize o comando `kubectl get pods`. Se as imagens de contentor não podem ser obtidas, veja [permitir o acesso ao registo de contentores com Kubernetes secreta](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, um exemplo de aplicação do Azure vote foi implementado num cluster de Kubernetes no AKS. Aprendeu a:

> [!div class="checklist"]
> * Atualizar ficheiros de manifesto Kubernetes
> * Executar uma aplicação no Kubernetes
> * Testar a aplicação

Avance para o próximo tutorial para saber como dimensionar uma aplicação Kubernetes e a infraestrutura de Kubernetes subjacente.

> [!div class="nextstepaction"]
> [Dimensionar a aplicação e infraestrutura do Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
