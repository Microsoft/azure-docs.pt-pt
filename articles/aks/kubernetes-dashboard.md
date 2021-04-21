---
title: Gerir um cluster de serviço Azure Kubernetes com o painel web
description: Saiba como utilizar o painel de instrumentos web de UI de Kubernetes incorporado para gerir um cluster Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 0d872a60c4aea89e621fe25ade45697244a74fa8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779728"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Aceda ao painel web Kubernetes no Serviço Azure Kubernetes (AKS)

Kubernetes inclui um painel web que pode ser usado para operações básicas de gestão. Este dashboard permite-lhe visualizar o estado básico de saúde e as métricas para as suas aplicações, criar e implementar serviços e editar as aplicações existentes. Este artigo mostra-lhe como aceder ao dashboard de Kubernetes usando o Azure CLI e, em seguida, guia-o através de algumas operações básicas do dashboard.

Para obter mais informações sobre o dashboard kubernetes, consulte [o Painel de Instrumentos da Web UI da Kubernetes.][kubernetes-dashboard] AKS utiliza a versão 2.0 e maior do painel de instrumentos de código aberto.

> [!WARNING]
> **O addon do painel AKS está definido para depreciação. Utilize a [vista de recursos Kubernetes no portal Azure (pré-visualização)][kubernetes-portal] em vez disso.** 
> * O dashboard Kubernetes é ativado por padrão para clusters que executam uma versão Kubernetes inferior a 1.18.
> * O addon do painel será desativado por padrão para todos os novos clusters criados em Kubernetes 1.18 ou superior. 
 > * Começando com Kubernetes 1.19 em pré-visualização, a AKS deixará de suportar a instalação do addon de kube-dashboard gerido. 
 > * Os clusters existentes com o complemento ativado não serão afetados. Os utilizadores continuarão a ser capazes de instalar manualmente o painel de instrumentos de código aberto como software instalado pelo utilizador.

## <a name="before-you-begin"></a>Antes de começar

Os passos detalhados neste documento assumem que criou um cluster AKS e estabeleceu uma `kubectl` ligação com o cluster. Se precisar de criar um cluster AKS, consulte [Quickstart: Implemente um cluster de serviço Azure Kubernetes utilizando o Azure CLI][aks-quickstart].

Também precisa da versão Azure CLI 2.6.0 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Desative o painel de instrumentos Kubernetes

O addon kube-dashboard é **ativado por padrão em clusters mais antigos que K8s 1.18**. O addon pode ser desativado executando o seguinte comando.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Inicie o painel kubernetes

> [!WARNING]
> O addon do dashboard AKS é prectado para as versões 1.19+. Utilize a [vista de recursos kubernetes no portal Azure (pré-visualização)][kubernetes-portal] em vez disso. 
> * O seguinte comando irá agora abrir a vista de recursos do Portal Azure em vez do painel de instrumentos kubernetes para as versões 1.19 ou acima.

Para iniciar o painel kubernetes num cluster, utilize o comando [de navegação az aks.][az-aks-browse] Este comando requer a instalação do addon kube-dashboard no cluster, que é incluído por padrão em clusters que executam qualquer versão mais antiga que Kubernetes 1.18.

O exemplo a seguir abre o painel de instrumentos para o cluster denominado *myAKSCluster* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando cria um proxy entre o seu sistema de desenvolvimento e a API de Kubernetes, e abre um navegador web para o dashboard kubernetes. Se um navegador web não abrir para o painel kubernetes, copie e cole o endereço URL indicado no CLI Azure, normalmente `http://127.0.0.1:8001` .

> [!NOTE]
> Se não vir o painel de `http://127.0.0.1:8001` instrumentos, pode encaminhar manualmente para os seguintes endereços. Os agrupamentos em 1.16 ou mais https de utilização e requerem um ponto final separado.
> * K8s 1.16 ou superior: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 e abaixo: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Inscreva-se no painel de instrumentos (kubernetes 1.16+)

> [!IMPORTANT]
> A partir de [v1.10.1 do dashboard de Kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) ou kubernetes v1.16+ a conta de serviço "kubernetes-dashboard" já não pode ser utilizada para recuperar recursos devido a uma [correção de segurança nesse lançamento](https://github.com/kubernetes/dashboard/pull/3400). Como resultado, os pedidos sem informação de auth devolvem um [erro não autorizado 401](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Um token ao portador recuperado de uma conta de serviço ainda pode ser usado como neste [exemplo do Painel de Instrumentos de Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), mas isso impacta o fluxo de login do add-on do dashboard em comparação com as versões mais antigas.
>
>Se ainda executar uma versão antes do 1.16, ainda pode dar permissões à conta de serviço "kubernetes-dashboard", mas isso não é **recomendado:**
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

O ecrã inicial apresentado requer um kubeconfig ou um token. Ambas as opções requerem permissões de recursos para exibir esses recursos no painel de instrumentos.

![ecrã de login](./media/kubernetes-dashboard/login.png)

**Use um kubeconfig**

Para clusters AZure AD ativados e não AD azure, um kubeconfig pode ser passado dentro Certifique-se de que os tokens de acesso são válidos, se as suas fichas estiverem expiradas, pode atualizar fichas através de kubectl.

1. Desconfig de administrador com `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Selecione `Kubeconfig` e clique para abrir o seletor de `Choose kubeconfig file` ficheiros
1. Selecione o seu ficheiro kubeconfig (predefinições para $HOME/.kube/config)
1. Clique `Sign In`

**Use um símbolo**

1. Para **o cluster ativado por AD não Azure,** corra `kubectl config view` e copie o símbolo associado à conta de utilizador do seu cluster.
1. Cole na opção simbólica no início do s.A.    
1. Clique `Sign In`

Para aglomerados Azure AD ativados, recupere o seu token AAD com o seguinte comando. Valide que substituiu o grupo de recursos e o nome do cluster no comando.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Uma vez bem sucedido, será exibida uma página semelhante à seguinte.

![A página geral do painel web kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Criar uma aplicação

As seguintes medidas exigem que o utilizador tenha permissões para os respetivos recursos. 

Para ver como o dashboard kubernetes pode reduzir a complexidade das tarefas de gestão, vamos criar uma aplicação. Pode criar uma aplicação a partir do dashboard Kubernetes fornecendo entrada de texto, um ficheiro YAML ou através de um assistente gráfico.

Para criar uma aplicação, complete os seguintes passos:

1. Selecione o botão **Criar** na janela superior direita.
1. Para utilizar o assistente gráfico, opte por **criar uma aplicação.**
1. Fornecer um nome para a implantação, como *nginx*
1. Introduza o nome para a imagem do recipiente a utilizar, tal como *nginx:1.15.5*
1. Para expor a porta 80 para tráfego web, você cria um serviço Kubernetes. Em **Serviço**, selecione **External**, em seguida, introduza **80** para a porta e porta-alvo.
1. Quando estiver pronto, **selecione Implementar** para criar a aplicação.

![Implementar uma aplicação no painel web de Kubernetes](./media/kubernetes-dashboard/create-app.png)

Demora um minuto ou dois para que um endereço IP externo público seja atribuído ao serviço Kubernetes. No tamanho esquerdo, sob **serviços selecionados de Discovery e Load Balanceing** .  O serviço da sua aplicação está listado, incluindo os *pontos finais externos,* como mostra o seguinte exemplo:

![Ver lista de serviços e pontos finais](./media/kubernetes-dashboard/view-services.png)

Selecione o endereço de ponto final para abrir uma janela do navegador web para a página NGINX predefinido:

![Ver a página NGINX predefinida da aplicação implementada](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Ver informações do pod

O dashboard Kubernetes pode fornecer métricas básicas de monitorização e informações de resolução de problemas, tais como registos.

Para ver mais informações sobre as suas cápsulas de aplicação, selecione **Pods** no menu esquerdo. A lista de cápsulas disponíveis é mostrada. Escolha o seu vômo *nginx* para visualizar informações, tais como o consumo de recursos:

![Ver informações do pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Editar a aplicação

Além de criar e visualizar aplicações, o dashboard Kubernetes pode ser usado para editar e atualizar implementações de aplicações. Para fornecer redundância adicional para a aplicação, vamos aumentar o número de réplicas NGINX.

Para editar uma implementação:

1. Selecione **Implementações** no menu da esquerda e, em seguida, escolha a sua implementação *nginx.*
1. **Selecione Editar** na barra de navegação superior direita.
1. Localize o `spec.replica` valor, em torno da linha 20. Para aumentar o número de réplicas para a aplicação, altere este valor de *1* para *3*.
1. Selecione **Update** quando estiver pronto.

![Editar a implementação para atualizar o número de réplicas](./media/kubernetes-dashboard/edit-deployment.png)

Leva alguns momentos para que as novas cápsulas sejam criadas dentro de um conjunto de réplicas. No menu da esquerda, escolha **Replica Sets** e, em seguida, escolha o seu conjunto *de réplicas nginx.* A lista de cápsulas reflete agora a contagem de réplicas atualizada, como mostra a seguinte saída de exemplo:

![Ver informações sobre o conjunto de réplicas](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o dashboard Kubernetes, consulte o [Painel de Instrumentos web da Kubernetes.][kubernetes-dashboard]

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
[az-account-get-access-token]: /cli/azure/account#az_account_get-access-token
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
