---
title: Gerenciar um cluster do serviço kubernetes do Azure com o painel da Web
description: Saiba como usar o painel de interface do usuário da Web do kubernetes interno para gerenciar um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: f150103c8e9534bfd1bb93d20e3d65d715767184
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996968"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Acessar o painel da Web do kubernetes no serviço kubernetes do Azure (AKS)

O kubernetes inclui um painel da Web que pode ser usado para operações básicas de gerenciamento. Esse painel permite exibir o status e as métricas de integridade básicos para seus aplicativos, criar e implantar serviços e editar aplicativos existentes. Este artigo mostra como acessar o painel do kubernetes usando o CLI do Azure, em seguida, orienta você em algumas operações básicas do painel.

Para obter mais informações sobre o painel do kubernetes, consulte [painel de interface do usuário da Web do amKubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você criou um cluster AKS e estabeleceu uma conexão de `kubectl` com o cluster. Se você precisar criar um cluster AKS, consulte o guia de [início rápido do AKS][aks-quickstart].

Precisa também da versão 2.0.46 ou posterior da CLI do Azure instalada e configurada. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Iniciar o painel do kubernetes

Para iniciar o painel do kubernetes, use o comando [AZ AKs Browse][az-aks-browse] . O exemplo a seguir abre o painel para o cluster chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Esse comando cria um proxy entre o sistema de desenvolvimento e a API kubernetes e abre um navegador da Web para o painel do kubernetes. Se um navegador da Web não abrir no painel do kubernetes, copie e cole o endereço da URL anotado na CLI do Azure, geralmente `http://127.0.0.1:8001`.

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
> Se o cluster AKS usar o RBAC, um *ClusterRoleBinding* deverá ser criado antes que você possa acessar o painel corretamente. Por padrão, o painel do kubernetes é implantado com acesso de leitura mínimo e exibe erros de acesso RBAC. O painel do kubernetes atualmente não dá suporte a credenciais fornecidas pelo usuário para determinar o nível de acesso, em vez disso, usa as funções concedidas à conta de serviço. Um administrador de cluster pode optar por conceder acesso adicional à conta de serviço do *kubernetes-Dashboard* , no entanto, isso pode ser um vetor para o escalonamento de privilégios. Você também pode integrar Azure Active Directory autenticação para fornecer um nível mais granular de acesso.
> 
> Para criar uma associação, use o comando [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] . O exemplo a seguir mostra como criar uma associação de exemplo, no entanto, essa associação de exemplo não aplica nenhum componente de autenticação adicional e pode levar ao uso inseguro. O painel do kubernetes está aberto para qualquer pessoa com acesso à URL. Não exponha o painel do kubernetes publicamente.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Para obter mais informações sobre como usar os diferentes métodos de autenticação, consulte o wiki do kubernetes Dashboard em [controles de acesso][dashboard-authentication].

![A página de visão geral do painel da Web do kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Criar uma aplicação

Para ver como o painel do kubernetes pode reduzir a complexidade das tarefas de gerenciamento, vamos criar um aplicativo. Você pode criar um aplicativo no painel do kubernetes fornecendo entrada de texto, um arquivo YAML ou um assistente gráfico.

Para criar um aplicativo, conclua as seguintes etapas:

1. Selecione o botão **criar** na janela superior direita.
1. Para usar o assistente gráfico, escolha **criar um aplicativo**.
1. Forneça um nome para a implantação, como *Nginx*
1. Insira o nome da imagem de contêiner a ser usada, como *Nginx: 1.15.5*
1. Para expor a porta 80 para o tráfego da Web, você cria um serviço kubernetes. Em **serviço**, selecione **externo**e, em seguida, insira **80** para a porta e a porta de destino.
1. Quando estiver pronto, selecione **implantar** para criar o aplicativo.

![Implantar um aplicativo no painel da Web do kubernetes](./media/kubernetes-dashboard/create-app.png)

Demora um ou dois minutos para que um endereço IP externo público seja atribuído ao serviço kubernetes. No tamanho do lado esquerdo, em **descoberta e balanceamento de carga** , selecione **Serviços**. O serviço do aplicativo está listado, incluindo os *pontos de extremidade externos*, conforme mostrado no exemplo a seguir:

![Exibir lista de serviços e pontos de extremidade](./media/kubernetes-dashboard/view-services.png)

Selecione o endereço do ponto de extremidade para abrir uma janela do navegador da Web na página padrão do NGINX:

![Exibir a página padrão do NGINX do aplicativo implantado](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Exibir informações de Pod

O painel do kubernetes pode fornecer métricas de monitoramento básicas e informações de solução de problemas, como logs.

Para ver mais informações sobre seu pods de aplicativo, selecione **pods** no menu à esquerda. A lista de pods disponíveis é mostrada. Escolha seu Pod *Nginx* para exibir informações, como consumo de recursos:

![Exibir informações de Pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Editar o aplicativo

Além de criar e exibir aplicativos, o painel do kubernetes pode ser usado para editar e atualizar implantações de aplicativo. Para fornecer redundância adicional para o aplicativo, vamos aumentar o número de réplicas NGINX.

Para editar uma implantação:

1. Selecione **implantações** no menu esquerdo e escolha sua implantação do *Nginx* .
1. Selecione **Editar** na barra de navegação superior direita.
1. Localize o valor `spec.replica`, em torno da linha 20. Para aumentar o número de réplicas para o aplicativo, altere esse valor de *1* para *3*.
1. Selecione **Atualizar** quando estiver pronto.

![Editar a implantação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Leva alguns minutos para que o novo pods seja criado dentro de um conjunto de réplicas. No menu à esquerda, escolha conjuntos de **réplicas**e, em seguida, escolha o conjunto de réplicas *Nginx* . A lista de pods agora reflete a contagem de réplicas atualizada, conforme mostrado na seguinte saída de exemplo:

![Exibir informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o painel do kubernetes, consulte o [painel de interface do usuário da Web do amKubernetes][kubernetes-dashboard].

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
