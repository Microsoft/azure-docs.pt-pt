---
title: Gerir um cluster do serviço Kubernetes do Azure com o dashboard de web
description: Saiba como utilizar o dashboard de interface do Usuário de web do Kubernetes incorporado para gerir um cluster do Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: e831979e0a9f9c8b4d812f682403ecf466abbd13
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993878"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Aceder ao dashboard de web do Kubernetes no Azure Kubernetes Service (AKS)

Kubernetes inclui um dashboard da web que pode ser utilizado para operações de gestão básicas. Este dashboard permite-lhe ver o estado de funcionamento básico e métricas para as suas aplicações, criar e implementar serviços e editar as aplicações existentes. Este artigo mostra-lhe como aceder ao dashboard do Kubernetes com a CLI do Azure, em seguida, orienta-o de algumas operações básicas do dashboard.

Para obter mais informações sobre o dashboard do Kubernetes, consulte [Dashboard de interface do Usuário de Web do Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste documento partem do princípio de que já criou um cluster do AKS e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de criar um cluster do AKS, consulte a [início rápido do AKS][aks-quickstart].

Precisa também da versão 2.0.46 ou posterior da CLI do Azure instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Iniciar o dashboard do Kubernetes

Para iniciar o dashboard do Kubernetes, utilize o [procurar az aks] [ az-aks-browse] comando. O exemplo seguinte abre o dashboard para o cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando cria um proxy entre o sistema de desenvolvimento e a API do Kubernetes e abre um browser para o dashboard do Kubernetes. Se um navegador da web não abrir ao dashboard do Kubernetes, copie e cole o endereço de URL indicado na CLI do Azure, normalmente `http://127.0.0.1:8001`.

![A página de descrição geral do dashboard do Kubernetes web](./media/kubernetes-dashboard/dashboard-overview.png)

### <a name="for-rbac-enabled-clusters"></a>Para clusters habilitados em RBAC

Se o seu cluster do AKS utiliza o RBAC, um *ClusterRoleBinding* tem de ser criado antes de poder aceder corretamente o dashboard. Por predefinição, o dashboard do Kubernetes é implementado com um mínimo de acesso de leitura e mostra os erros de acesso RBAC. O dashboard do Kubernetes não suporta atualmente as credenciais fornecidas pelo usuário para determinar o nível de acesso, em vez disso, ele usa as funções concedidas à conta de serviço. Um administrador de cluster pode optar por conceder o acesso adicional para o *dashboard do kubernetes* conta, de serviço, no entanto, isso pode ser um vetor de escalamento de privilégios. Também pode integrar a autenticação do Azure Active Directory para fornecer um nível mais granular de acesso.

Para criar uma ligação, utilize o [kubectl criar clusterrolebinding] [ kubectl-create-clusterrolebinding] comando conforme mostrado no exemplo a seguir. 

> [!WARNING]
> Este enlace de exemplo não se aplica a quaisquer componentes de autenticação adicional e pode levar a utilização insegura. O dashboard do Kubernetes é aberto para qualquer pessoa com acesso ao URL. Não expõem o dashboard do Kubernetes publicamente.
>
> Para obter mais informações sobre como utilizar os métodos de autenticação diferentes, consulte wiki de dashboard do Kubernetes no [controlos de acesso][dashboard-authentication].

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

Agora pode aceder ao dashboard do Kubernetes no seu cluster habilitados no RBAC. Para iniciar o dashboard do Kubernetes, utilize o [procurar az aks] [ az-aks-browse] comando conforme detalhado no passo anterior.

## <a name="create-an-application"></a>Criar uma aplicação

Para ver o dashboard do Kubernetes como reduzir a complexidade das tarefas de gestão, vamos criar uma aplicação. Pode criar uma aplicação a partir do dashboard do Kubernetes ao fornecer o texto de entrada, um ficheiro YAML, ou por meio de um assistente gráfico.

Para criar uma aplicação, conclua os seguintes passos:

1. Selecione o **criar** botão na janela superior direita.
1. Para utilizar o Assistente de gráfico, optar por **criar uma aplicação**.
1. Forneça um nome para a implementação, como *nginx*
1. Introduza o nome da imagem de contentor a utilizar, como *nginx:1.15.5*
1. Para expor a porta 80 para tráfego web, vai criar um serviço de Kubernetes. Sob **serviço**, selecione **externos**, em seguida, introduza **80** para a porta e a porta de destino.
1. Quando estiver pronto, selecione **Deploy** para criar a aplicação.

![Implementar uma aplicação no dashboard do Kubernetes web](./media/kubernetes-dashboard/create-app.png)

Demora um minuto ou dois para que um endereço IP público externo a ser atribuída ao serviço do Kubernetes. O tamanho do lado esquerdo, sob **balanceamento de carga e de deteção** selecionar **serviços**. Serviço do seu aplicativo estiver listado, incluindo o *pontos finais externos*, conforme mostrado no exemplo a seguir:

![Ver a lista de serviços e pontos de extremidade](./media/kubernetes-dashboard/view-services.png)

Selecione o endereço de ponto final para abrir uma janela do browser para a página predefinida do NGINX:

![Ver a página do NGINX predefinida da aplicação implementada](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Ver informações de pod

O dashboard de Kubernetes pode fornecer métricas de monitorização básicas e informações, como registos de resolução de problemas.

Para ver mais informações sobre os seus pods de aplicação, selecione **Pods** no menu da esquerda. É apresentada a lista de pods disponíveis. Escolha sua *nginx* pod para ver informações como, por exemplo, o consumo de recursos:

![Ver informações de pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Editar a aplicação

Além de criar e aplicações de visualização, o dashboard de Kubernetes pode ser utilizado para editar e atualizar as implementações de aplicações. Para fornecer redundância adicional para a aplicação, vamos aumentar o número de réplicas do NGINX.

Para editar uma implementação:

1. Selecione **implementações** no menu da esquerda e, em seguida, escolha sua *nginx* implementação.
1. Selecione **editar** na barra de navegação direita superior.
1. Localize o `spec.replica` valor, em torno de linha 20. Para aumentar o número de réplicas para o aplicativo, altere este valor de *1* ao *3*.
1. Selecione **atualização** quando pronto.

![Edite a implementação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Demora alguns minutos para os novos pods a ser criada dentro de um conjunto de réplica. No menu do lado esquerdo, selecione **conjuntos de réplicas**e, em seguida, escolha sua *nginx* conjunto de réplicas. A lista de pods agora reflete a contagem de réplica atualizada, conforme mostrado no seguinte exemplo:

![Ver informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o dashboard do Kubernetes, consulte a [Dashboard de interface do Usuário de Web do Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
