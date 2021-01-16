---
title: Tutorial do Kubernetes no Azure – Implementar uma aplicação
description: Neste tutorial do Azure Kubernetes Service (AKS), implemente uma aplicação de vários contentores no seu cluster com uma imagem personalizada armazenada no Azure Container Registry.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a0de097a545a831e39a671fe4cf5eadcd336ce24
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250184"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Tutorial: Executar aplicações no Serviço Kubernetes do Azure (AKS)

O Kubernetes dispõe de uma plataforma distribuída para aplicações em contentores. Cria e implementa as suas próprias aplicações e serviços num cluster do Kubernetes, e permite que o cluster gira a disponibilidade e a conectividade. Neste tutorial, parte quatro de sete, a aplicação de exemplo é implementada num cluster do Kubernetes. Saiba como:

> [!div class="checklist"]
> * Atualizar um ficheiro manifesto de Kubernetes
> * Executar uma aplicação no Kubernetes
> * Testar a aplicação

Em tutoriais posteriores, esta aplicação é dimensionada e atualizada.

Este guia de introdução parte do princípio de que possui conhecimentos básicos dos conceitos do Kubernetes. Para obter mais informações, consulte [os conceitos fundamentais da Kubernetes para o Serviço Azure Kubernetes (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Before you begin

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor, a imagem foi carregada para o Azure Container Registry e foi criado um cluster de Kubernetes.

Para concluir este tutorial, precisa do ficheiro de manifesto previamente criado do Kubernetes `azure-vote-all-in-one-redis.yaml`. Este ficheiro foi transferido com o código de origem da aplicação num tutorial anterior. Verifique se clonou o repo, e que transformou os diretórios no repo clonado. Se não fez estes passos e gostaria de seguir em frente, comece com [Tutorial 1 – Crie imagens de contentores.][aks-tutorial-prepare-app]

Este tutorial requer que esteja a executar a versão 2.0.53 ou mais tarde do Azure CLI. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="update-the-manifest-file"></a>Atualizar o ficheiro de manifesto

Nestes tutoriais, uma instância do Azure Container Registry (ACR) armazena a imagem de contentor no exemplo de aplicação. Para implementar a aplicação, tem de atualizar o nome da imagem no ficheiro de manifesto do Kubernetes para incluir o nome de servidor de início de sessão do ACR.

Obtenha o nome do servidor de início de sessão ACR com o comando [az acr list][az-acr-list], da seguinte forma:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O ficheiro de manifesto de exemplo do repositório git clonado no primeiro tutorial utiliza o nome do servidor de início de sessão da *microsoft*. Certifique-se de que está no diretório clonado *azure-vote-app-redis,* e depois abra o ficheiro manifesto com um editor de texto, tais `vi` como:

```console
vi azure-vote-all-in-one-redis.yaml
```

Substitua *microsoft* pelo seu nome de servidor de início de sessão do ACR. O nome da imagem é encontrado na linha 60 do ficheiro manifesto. O seguinte exemplo mostra o nome da imagem predefinida:

```yaml
containers:
- name: azure-vote-front
  image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
```

Indique o seu próprio nome de servidor de início de sessão do ACR para que o ficheiro de manifesto seja semelhante ao seguinte exemplo:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Guarde e feche o ficheiro. Em `vi` , use `:wq` .

## <a name="deploy-the-application"></a>Implementar a aplicação

Para implementar a sua aplicação, utilize o comando [kubectl apply][kubectl-apply]. Este comando analisa o ficheiro de manifesto e cria os objetos de Kubernetes definidos. Especifique o ficheiro de manifesto de exemplo, conforme mostrado no exemplo a seguir:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A saída de exemplo a seguir mostra os recursos criados com sucesso no cluster AKS:

```console
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Inicialmente, o *IP EXTERNO* para o serviço de frente de *voto azul* é apresentado como *pendente:*

```output
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Quando o endereço *EXTERNAL-IP* mudar de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```output
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Para ver a aplicação em ação, abra um navegador web para o endereço IP externo do seu serviço:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote.png" alt-text="Screenshot mostrando a imagem do recipiente Azure Vote App em execução em um cluster AKS aberto em um navegador web local" lightbox="./media/container-service-kubernetes-tutorials/azure-vote.png":::

Se o pedido não foi carregado, pode ser devido a um problema de autorização com o seu registo de imagem. Para ver o estado dos seus contentores, utilize o comando `kubectl get pods`. Se as imagens do contentor não puderem ser retiradas, consulte [authenticate com registo de contentores Azure do Serviço Azure Kubernetes](cluster-container-registry-integration.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, uma amostra Azure vote application foi implantada para um cluster Kubernetes em AKS. Aprendeu a:

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
