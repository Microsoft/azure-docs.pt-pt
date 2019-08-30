---
title: Gerenciar um cluster do serviço kubernetes do Azure com o painel da Web
description: Saiba como usar o painel de interface do usuário da Web do kubernetes interno para gerenciar um cluster do AKS (serviço kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: mlearned
ms.openlocfilehash: 5aa8268fee7d43ad13ea8710760ba493683f502e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126859"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Acessar o painel da Web do kubernetes no serviço kubernetes do Azure (AKS)

O kubernetes inclui um painel da Web que pode ser usado para operações básicas de gerenciamento. Esse painel permite exibir o status e as métricas de integridade básicos para seus aplicativos, criar e implantar serviços e editar aplicativos existentes. Este artigo mostra como acessar o painel do kubernetes usando o CLI do Azure, em seguida, orienta você em algumas operações básicas do painel.

Para obter mais informações sobre o painel do kubernetes, consulte [painel de interface do usuário da Web][kubernetes-dashboard]do amKubernetes.

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você criou um cluster AKs e estabeleceu uma `kubectl` conexão com o cluster. Se você precisar criar um cluster AKS, consulte o guia de [início rápido do AKS][aks-quickstart].

Precisa também da versão 2.0.46 ou posterior da CLI do Azure instalada e configurada. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Iniciar o painel do kubernetes

Para iniciar o painel do kubernetes, use o comando [AZ AKs Browse][az-aks-browse] . O exemplo a seguir abre o painel para o cluster chamado *myAKSCluster* no grupo de recursoschamado MyResource Group:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Esse comando cria um proxy entre o sistema de desenvolvimento e a API kubernetes e abre um navegador da Web para o painel do kubernetes. Se um navegador da Web não abrir no painel do kubernetes, copie e cole o endereço da URL anotado na CLI do Azure `http://127.0.0.1:8001`, normalmente.

![A página de logon do painel da Web do kubernetes](./media/kubernetes-dashboard/dashboard-login.png)

Você tem as seguintes opções para entrar no painel do seu cluster:

* Um [arquivo kubeconfig][kubeconfig-file]. Você pode gerar um arquivo kubeconfig usando [AZ AKs Get-Credentials][az-aks-get-credentials].
* Um token, como um token de [conta de serviço][aks-service-accounts] ou token de usuário. Em [clusters habilitados para AAD][aad-cluster], esse token seria um token do AAD. Você pode usar `kubectl config view` para listar os tokens em seu arquivo kubeconfig. Para obter mais detalhes sobre como criar um token do AAD para uso com um cluster AKS, consulte [integrar o Azure Active Directory com o serviço kubernetes do Azure usando o CLI do Azure][aad-cluster].
* A conta de serviço do painel padrão, que será usada se você clicar em *ignorar*.

> [!WARNING]
> Nunca exponha o painel do kubernetes publicamente, independentemente do método de autenticação usado.
> 
> Ao configurar a autenticação para o painel do kubernetes, é recomendável que você use um token sobre a conta de serviço do painel padrão. Um token permite que cada usuário use suas próprias permissões. O uso da conta de serviço do painel padrão pode permitir que um usuário ignore suas próprias permissões e use a conta de serviço em vez disso.
> 
> Se você optar por usar a conta de serviço do painel padrão e o cluster AKS usar o RBAC, um *ClusterRoleBinding* deverá ser criado para que você possa acessar o painel corretamente. Por padrão, o painel do kubernetes é implantado com acesso de leitura mínimo e exibe erros de acesso RBAC. Um administrador de cluster pode optar por conceder acesso adicional à conta de serviço do *kubernetes-Dashboard* , no entanto, isso pode ser um vetor para o escalonamento de privilégios. Você também pode integrar Azure Active Directory autenticação para fornecer um nível mais granular de acesso.
>
> Para criar uma associação, use o comando [kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] , conforme mostrado no exemplo a seguir. **Esta associação de exemplo não aplica nenhum componente de autenticação adicional e pode levar ao uso inseguro.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Agora você pode acessar o painel do kubernetes em seu cluster habilitado para RBAC. Para iniciar o painel do kubernetes, use o comando [AZ AKs Browse][az-aks-browse] conforme detalhado na etapa anterior.
>
> Se o cluster não usar o RBAC, não será recomendável criar um *ClusterRoleBinding*.
> 
> Para obter mais informações sobre como usar os diferentes métodos de autenticação, consulte o wiki do kubernetes Dashboard em [controles de acesso][dashboard-authentication].

Depois de escolher um método para entrar, o painel do kubernetes é exibido. Se você optar por usar o *token* ou *ignorar*, o painel do kubernetes usará as permissões do usuário conectado no momento para acessar o cluster.

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

1. Selecione implantações no menu esquerdo e escolha sua implantação do *Nginx* .
1. Selecione **Editar** na barra de navegação superior direita.
1. Localize o `spec.replica` valor, em torno da linha 20. Para aumentar o número de réplicas para o aplicativo, altere esse valor de *1* para *3*.
1. Selecione **Atualizar** quando estiver pronto.

![Editar a implantação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Leva alguns minutos para que o novo pods seja criado dentro de um conjunto de réplicas. No menu à esquerda, escolha conjuntos de **réplicas**e, em seguida, escolha o conjunto de réplicas *Nginx* . A lista de pods agora reflete a contagem de réplicas atualizada, conforme mostrado na seguinte saída de exemplo:

![Exibir informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o painel do kubernetes, consulte o [painel de interface do usuário da Web][kubernetes-dashboard]do amKubernetes.

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
