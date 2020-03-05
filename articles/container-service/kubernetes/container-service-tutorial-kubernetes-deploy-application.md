---
title: (DEPRECIADO) Tutorial do Serviço de Contentores Azure - Aplicação de implantação
description: Tutorial do Azure Container Service – Implementar Aplicação
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274060"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(DEPRECIADO) Executar aplicações em Kubernetes

> [!TIP]
> Para a versão atualizada este tutorial que utiliza o Serviço Azure Kubernetes, consulte [Tutorial: Executar aplicações no Serviço Azure Kubernetes (AKS)](../../aks/tutorial-kubernetes-deploy-application.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste tutorial, parte quatro de sete, a aplicação de exemplo é implementada num cluster do Kubernetes. Os passos concluídos incluem:

> [!div class="checklist"]
> * Atualizar ficheiros de manifesto Kubernetes
> * Executar uma aplicação no Kubernetes
> * Testar a aplicação

Nos tutoriais subsequentes, esta aplicação é aumentada horizontalmente, atualizada e o Log Analytics é configurado para monitorizar o cluster de Kubernetes.

Este tutorial pressupõe um conhecimento básico dos conceitos do Kubernetes. Para obter informações detalhadas sobre o Kubernetes, veja a [documentação do Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, foi compactada uma aplicação numa imagem de contentor, a imagem foi carregada para o Azure Container Registry e foi criado um cluster de Kubernetes. 

Para concluir este tutorial, precisa do ficheiro de manifesto previamente criado do Kubernetes `azure-vote-all-in-one-redis.yml`. Este ficheiro foi transferido com o código de origem da aplicação num tutorial anterior. Certifique-se de que tem um clone do repositório e que foram alterados diretórios no repositório clonado.

Se ainda não concluiu estes passos e pretende acompanhar, regresse ao [Tutorial 1 – Criar imagens de contentor](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Atualizar ficheiro de manifesto

Neste tutorial, o Azure Container Registry (ACR) serviu para armazenar uma imagem de contentor. Antes de executar a aplicação, o nome de servidor de início de sessão ACR tem de ser atualizado no ficheiro de manifesto Kubernetes.

Obtenha o nome do servidor de início de sessão ACR com o comando [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

O ficheiro de manifesto foi previamente criado com um nome de servidor de início de sessão de `microsoft`. Abra o ficheiro com qualquer editor de texto. Neste exemplo, o ficheiro está aberto com `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Substitua `microsoft` pelo nome do servidor de início de sessão do ACR. Este valor foi encontrado na linha **47** do ficheiro de manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Guarde e feche o ficheiro.

## <a name="deploy-application"></a>Implementar aplicação

Utilize o comando [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) para executar a aplicação. Este comando analisa o ficheiro de manifesto e cria os objetos de Kubernetes definidos.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

Saída:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testar aplicação

O [serviço do Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) é criado, o que expõe a aplicação na Internet. Este processo pode demorar alguns minutos. 

Para monitorizar o progresso, utilize o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Inicialmente, o **EXTERNAL-IP** do serviço `azure-vote-front` aparece como `pending`. Quando o endereço EXTERNAL-IP mudar de `pending` para `IP address`, utilize `CTRL-C` para parar o processo de observação do kubectl.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Para ver a aplicação, navegue para o endereço IP externo.

![Imagem do cluster do Kubernetes no Azure no Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, a aplicação do Azure vote foi implementada num cluster de Kubernetes do Azure Container Service. As tarefas concluídas incluem:  

> [!div class="checklist"]
> * Transferir ficheiros de manifesto Kubernetes
> * Executar a aplicação no Kubernetes
> * Testou a aplicação

Avance para o próximo tutorial para saber mais sobre o dimensionamento de uma aplicação Kubernetes e a infraestrutura de Kubernetes subjacente. 

> [!div class="nextstepaction"]
> [Dimensionar a aplicação e infraestrutura do Kubernetes](./container-service-tutorial-kubernetes-scale.md)
