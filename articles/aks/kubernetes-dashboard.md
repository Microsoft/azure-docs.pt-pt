---
title: Gerir um cluster de serviço Azure Kubernetes com o painel web
description: Saiba como usar o painel de instrumentos ui web kubernetes incorporado para gerir um cluster azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595353"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Aceda ao painel web da Kubernetes no Serviço Azure Kubernetes (AKS)

Kubernetes inclui um painel web que pode ser usado para operações básicas de gestão. Este dashboard permite-lhe visualizar o estado básico de saúde e as métricas para as suas aplicações, criar e implementar serviços e editar as aplicações existentes. Este artigo mostra-lhe como aceder ao dashboard Kubernetes utilizando o Azure CLI e, em seguida, guia-o através de algumas operações básicas do dashboard.

Para obter mais informações sobre o dashboard Kubernetes, consulte [kubernetes Web UI Dashboard][kubernetes-dashboard].

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste documento assumem que criou `kubectl` um cluster AKS e estabeleceu uma ligação com o cluster. Se precisar de criar um cluster AKS, consulte o arranque rápido da [AKS.][aks-quickstart]

Precisa também da versão 2.0.46 ou posterior da CLI do Azure instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Inicie o dashboard Kubernetes

Para iniciar o dashboard Kubernetes, use o comando de [navegação az aks.][az-aks-browse] O exemplo seguinte abre o painel de instrumentos para o cluster chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando cria um proxy entre o seu sistema de desenvolvimento e a API Kubernetes, e abre um navegador web para o dashboard Kubernetes. Se um navegador web não se abrir ao painel de instrumentos kubernetes, copie e `http://127.0.0.1:8001`cole o endereço URL anotado no Azure CLI, tipicamente .

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Se o seu cluster AKS utilizar o RBAC, deve ser criado um *ClusterRoleBinding* antes de poder aceder corretamente ao painel de instrumentos. Por predefinição, o painel kubernetes é implantado com o mínimo de acesso de leitura e apresenta erros de acesso RBAC. O dashboard Kubernetes não suporta atualmente credenciais fornecidas pelo utilizador para determinar o nível de acesso, mas utiliza as funções concedidas à conta de serviço. Um administrador de cluster pode optar por conceder acesso adicional à conta de serviço *kubernetes-dashboard,* no entanto este pode ser um vetor para a escalada de privilégios. Também pode integrar a autenticação do Diretório Ativo Azure para proporcionar um nível de acesso mais granular.
> 
> Para criar uma ligação, use o [kubectl criar][kubectl-create-clusterrolebinding] um comando de clusterrolebinding. O exemplo seguinte mostra como criar uma ligação da amostra, no entanto, esta encadernação da amostra não aplica componentes adicionais de autenticação e pode levar a uma utilização insegura. O painel kubernetes está aberto a qualquer pessoa com acesso ao URL. Não exponha publicamente o painel kubernetes.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Para obter mais informações sobre a utilização dos diferentes métodos de autenticação, consulte o painel de instrumentos Kubernetes wiki nos [controlos][dashboard-authentication]de acesso .

![A página geral do painel web kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Criar uma aplicação

Para ver como o dashboard Kubernetes pode reduzir a complexidade das tarefas de gestão, vamos criar uma aplicação. Pode criar uma aplicação a partir do dashboard Kubernetes fornecendo entrada de texto, um ficheiro YAML ou através de um assistente gráfico.

Para criar uma aplicação, complete os seguintes passos:

1. Selecione o botão **Criar** na janela superior direita.
1. Para utilizar o assistente gráfico, opte por **Criar uma aplicação**.
1. Fornecer um nome para a implantação, como *o nginx*
1. Introduza o nome para a imagem do recipiente a utilizar, como *nginx:1.15.5*
1. Para expor a porta 80 para tráfego web, cria um serviço Kubernetes. Em **Serviço**, selecione **External,** em seguida, insira **80** para a porta e porta-alvo.
1. Quando estiver pronto, selecione **Implementar** para criar a aplicação.

![Implementar uma aplicação no painel web da Kubernetes](./media/kubernetes-dashboard/create-app.png)

Leva um minuto ou dois para que um endereço IP externo público seja atribuído ao serviço Kubernetes. No tamanho esquerdo, em **Discovery e Load Balanceing** select **Services**. O serviço da sua aplicação está listado, incluindo os *pontos finais externos,* como mostra o seguinte exemplo:

![Ver lista de serviços e pontos finais](./media/kubernetes-dashboard/view-services.png)

Selecione o endereço final para abrir uma janela do navegador web para a página padrão NGINX:

![Ver a página padrão NGINX da aplicação implementada](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Ver informações sobre pod

O painel de instrumentos kubernetes pode fornecer métricas básicas de monitorização e informações de resolução de problemas, tais como registos.

Para ver mais informações sobre as suas cápsulas de aplicação, selecione **Pods** no menu à esquerda. A lista de cápsulas disponíveis é mostrada. Escolha o seu casulo *nginx* para visualizar informações, tais como o consumo de recursos:

![Ver informações sobre pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Editar a aplicação

Além de criar e visualizar aplicações, o dashboard Kubernetes pode ser usado para editar e atualizar implementações de aplicações. Para fornecer redundância adicional para a aplicação, vamos aumentar o número de réplicas NGINX.

Para editar uma implantação:

1. Selecione **Implementações** no menu esquerdo e, em seguida, escolha a sua implantação *nginx.*
1. **Selecione Editar** na barra de navegação superior direita.
1. Localize `spec.replica` o valor, em torno da linha 20. Para aumentar o número de réplicas para a aplicação, altere este valor de *1* para *3*.
1. Selecione **Atualizar** quando estiver pronto.

![Editar a implementação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

São precisos alguns momentos para as novas cápsulas serem criadas dentro de um conjunto de réplicas. No menu à esquerda, escolha **Conjuntos de Réplicas**e, em seguida, escolha o conjunto de *réplicas de nginx.* A lista de cápsulas reflete agora a contagem de réplicas atualizada, como mostra a seguinte saída de exemplo:

![Ver informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o dashboard Kubernetes, consulte o Painel De [UI web kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
