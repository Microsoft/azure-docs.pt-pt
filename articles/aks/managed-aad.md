---
title: Use Azure AD no serviço Azure Kubernetes
description: Saiba como utilizar o Azure AD no Serviço Azure Kubernetes (AKS)
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: 280637be417d904de6dbb7ae2e2647026da6c838
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374547"
---
# <a name="integrate-aks-managed-azure-ad-preview"></a>Integre A azure AD gerido pela AKS (Preview)

> [!Note]
> Os clusters AKS existentes (Azure Kubernetes Service) com integração do Azure Ative Directory (Azure AD) não são afetados pela nova experiência Azure AD gerida pela AKS.

A integração AD da AZure com a Azure AD gerida pela AKS foi concebida para simplificar a experiência de integração da AD Azure, onde os utilizadores eram previamente obrigados a criar uma aplicação para clientes, uma aplicação de servidor, e exigiu que o inquilino da Azure AD concedesse permissões de Leitura ao Diretório. Na nova versão, o fornecedor de recursos AKS gere as aplicações do cliente e do servidor para si.

## <a name="limitations"></a>Limitações

* Não é possível atualmente atualizar um cluster integrado AKS AD existente para a nova experiência Azure AD gerida pela AKS.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "as-is" e "conforme disponível", e estão excluídas dos Contratos de Nível de Serviço e garantia limitada. As pré-visualizações da AKS são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço. Como tal, estas características não se destinam ao uso da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> - [Políticas de apoio da AKS](support-policies.md)
> - [FAQ de suporte Azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

* Localize o seu ID do inquilino da Conta Azure navegando no portal Azure e selecione O Diretório Ativo Azure > Propriedades > ID do Diretório

> [!Important]
> Você deve usar Kubectl com uma versão mínima de 1.18

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.5.1 ou mais tarde
- A extensão aks-preview 0.4.38
- Kubectl com uma versão mínima de [1.18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Para instalar/atualizar a extensão de pré-visualização de aks ou posterior, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Para instalar kubectl, utilize os seguintes comandos:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Utilize [estas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operativos.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```
## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação AD do Azure

Os administradores do cluster podem configurar o controlo de acesso baseado em funções (RBAC) baseado em funções com base na identidade de um utilizador ou na adesão ao grupo de diretórios. A autenticação AZure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de ligação Open ID][open-id-connect].

A partir do interior do cluster Kubernetes, a Autenticação Token Webhook é usada para verificar fichas de autenticação. A autenticação de ficha webhook é configurada e gerida como parte do cluster AKS.

## <a name="webhook-and-api-server"></a>Servidor Webhook e API

:::image type="content" source="media/aad-integration/auth-flow.png" alt-text="Fluxo de autenticação de servidores Webhook e API":::

Como mostrado no gráfico acima, o servidor API chama o servidor webhook AKS e executa os seguintes passos:

1. A aplicação do cliente Azure AD é utilizada pela kubectl para iniciar sessão com o [fluxo de autorização do dispositivo OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).
2. A AZure AD fornece um access_token, id_token e um refresh_token.
3. O utilizador faz um pedido para kubectl com um access_token da kubeconfig.
4. A Kubectl envia o access_token para a APIServer.
5. O Servidor API está configurado com o Auth WebHook Server para realizar validação.
6. O servidor webhook de autenticação confirma que a assinatura JSON Web Token é válida verificando a chave de assinatura pública Azure AD.
7. A aplicação do servidor utiliza credenciais fornecidas pelo utilizador para consultar membros do grupo do utilizador iniciado a partir da API do Gráfico MS.
8. Uma resposta é enviada para o APIServer com informações do utilizador, tais como a reivindicação principal do utilizador (UPN) do token de acesso e a adesão do grupo ao utilizador com base no ID do objeto.
9. A API executa uma decisão de autorização com base no Papel/RoleBinding da Kubernetes.
10. Uma vez autorizado, o servidor API retorna uma resposta ao kubectl.
11. A Kubectl fornece feedback ao utilizador.


## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster AKS com Azure AD ativado

Crie um cluster AKS utilizando os seguintes comandos CLI.

Criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Você pode usar um grupo AD Azure existente, ou criar um novo. Precisa da identificação do objeto para o seu grupo AD Azure.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Para resatar um novo grupo AD Azure para os seus administradores de cluster, utilize o seguinte comando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Crie um cluster AKS e permita o acesso da administração ao seu grupo AD Azure

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Uma criação bem sucedida de um cluster Azure AD gerido pela AKS tem a seguinte secção no corpo de resposta
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

O cluster é criado dentro de poucos minutos.

## <a name="access-an-azure-ad-enabled-cluster"></a>Aceda a um cluster AD AD Azure

Você precisará do [cluster de serviço Azure Kubernetes para](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) executar os seguintes passos.

Obtenha as credenciais de utilizador para aceder ao cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Siga as instruções para iniciar sinsuposições.

Utilize o comando kubectl get nodes para visualizar os nóns no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configure [o Controlo de Acesso Baseado em Função (RBAC)](https://review.docs.microsoft.com/azure/aks/azure-ad-rbac?branch=pr-en-us-117564) para configurar grupos de segurança adicionais para os seus clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Problemas de acesso à resolução de problemas com a Azure AD

> [!Important]
> Os passos descritos abaixo estão a contornar a autenticação normal do grupo Azure AD. Use-os apenas em caso de emergência.

Se estiver permanentemente bloqueado por não ter acesso a um grupo Azure AD válido com acesso ao seu cluster, ainda pode obter as credenciais de administração para aceder diretamente ao cluster.

Para executar estes passos, você precisará ter acesso ao admin de cluster de [serviço Azure Kubernetes.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role)

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-login-with-kubelogin"></a>Login não interativo com kubelogin

Existem alguns cenários não interativos, como os oleodutos de integração contínua, que não estão atualmente disponíveis com kubectl. Você pode usar [kubelogin](https://github.com/Azure/kubelogin) para aceder ao cluster com login principal de serviço não interativo.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Controlo de Acesso Baseado em Função AZure.][azure-ad-rbac]
* Utilize [kubelogin](https://github.com/Azure/kubelogin) para aceder a funcionalidades de autenticação Azure que não estão disponíveis em kubectl.
* Utilize [os modelos Azure Resource Manager (ARM)][aks-arm-template] para criar clusters Azure AD geridos pela AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

