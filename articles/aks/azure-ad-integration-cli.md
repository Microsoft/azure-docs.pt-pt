---
title: Integrar o Diretório Ativo Azure com o Serviço Azure Kubernetes
description: Aprenda a usar o Azure CLI para criar e azure ative diretório Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273775"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrar o Diretório Ativo Azure com o Serviço Azure Kubernetes utilizando o Azure CLI

O Serviço Azure Kubernetes (AKS) pode ser configurado para utilizar o Azure Ative Directory (AD) para autenticação do utilizador. Nesta configuração, pode iniciar sessão num cluster AKS utilizando um símbolo de autenticação Azure AD. Os operadores de cluster também podem configurar o controlo de acesso baseado em funções da Kubernetes (RBAC) com base na identidade de um utilizador ou na adesão de um grupo de diretórios.

Este artigo mostra-lhe como criar os componentes AD Azure necessários, em seguida, implementar um cluster ativado por AD Azure e criar uma função rBAC básica no cluster AKS. Também pode [completar estes passos utilizando o portal Azure.][azure-ad-portal]

Para obter o script completo de amostras utilizado neste artigo, consulte [amostras Azure CLI - integração AKS com Azure AD][complete-script].

Aplicam-se as seguintes limitações:

- O Azure AD só pode ser ativado quando criar um novo cluster ativado por RBAC. Não se pode ativar o Azure AD num aglomerado AKS existente.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.61 ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][install-azure-cli].

Vá a [https://shell.azure.com](https://shell.azure.com) para abrir cloud Shell no seu navegador.

Para consistência e para ajudar a executar os comandos neste artigo, crie uma variável para o seu nome de cluster AKS desejado. O exemplo que se segue usa o nome *myakscluster:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação da AD Azure

A autenticação Azure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a documentação de [ligação][open-id-connect]open ID .

A partir do interior do cluster Kubernetes, a autenticação de token webhook é usada para verificar tokens de autenticação. A autenticação token webhook é configurada e gerida como parte do cluster AKS. Para obter mais informações sobre a autenticação token Webhook, consulte a documentação de [autenticação do webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação AKS, duas aplicações Azure AD estão configuradas. Esta operação deve ser concluída por um administrador de inquilinos azure.

## <a name="create-azure-ad-server-component"></a>Criar componente de servidor AD Azure

Para integrar com aks, cria e utiliza uma aplicação Azure AD que funciona como um ponto final para os pedidos de identidade. A primeira aplicação Azure AD de que precisa recebe a adesão ao grupo Azure AD para um utilizador.

Criar o componente de aplicação do servidor utilizando a [aplicação az ad criar][az-ad-app-create] comando, em seguida, atualizar as reivindicações de membrodo grupo usando o comando de atualização de [app az az.][az-ad-app-update] O exemplo seguinte usa a variável *aksname* definida na secção [Antes de iniciar,](#before-you-begin) e cria uma variável

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Agora crie um diretor de serviço para a aplicação do servidor usando o [az ad sp criar][az-ad-sp-create] comando. Este diretor de serviço é utilizado para autenticar-se dentro da plataforma Azure. Em seguida, obtenha o segredo principal do serviço utilizando o comando de [reset credencial az ad sp][az-ad-sp-credential-reset] e designe para a variável chamada *servidorApplicationSecret* para utilização num dos seguintes passos:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

O Azure AD necessita de permissões para realizar as seguintes ações:

* Ler dados do diretório
* Iniciar sessão e ler o perfil de utilizador

Atribuir estas permissões utilizando a permissão da [aplicação az ad adicionar][az-ad-app-permission-add] comando:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Por fim, conceda as permissões atribuídas no passo anterior para a aplicação do servidor utilizando o comando de permissão de permissão de [aplicação az az.][az-ad-app-permission-grant] Este passo falha se a conta corrente não for administradora de inquilinos. Também precisa de adicionar permissões para o pedido da AD Azure para solicitar informações que possam de outra forma necessitar de consentimento administrativo usando a [permissão de autorização da az app ad admin-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Criar componente de cliente Azure AD

A segunda aplicação Azure AD é utilizada quando um utilizador regista o cluster AKS com o Kubernetes CLI (`kubectl`). Esta aplicação de cliente retira o pedido de autenticação do utilizador e verifica as suas credenciais e permissões. Criar a aplicação Azure AD para o componente cliente utilizando a [aplicação az ad criar][az-ad-app-create] comando:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Criar um diretor de serviço para a aplicação do cliente utilizando o [az ad sp criar][az-ad-sp-create] comando:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obtenha o ID oAuth2 para a aplicação do servidor para permitir o fluxo de autenticação entre os dois componentes da aplicação usando o comando [de show de aplicações az ad.][az-ad-app-show] Este ID oAuth2 é usado no próximo passo.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adicione as permissões para a aplicação do cliente e componentes de aplicação do servidor para usar o fluxo de comunicação oAuth2 usando a permissão de permissão da [aplicação az az adicionar][az-ad-app-permission-add] comando. Em seguida, conceda permissões para a aplicação do cliente para comunicação com a aplicação do servidor usando o comando de permissão de permissão de [aplicação az az:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Implementar o cluster

Com as duas aplicações Azure AD criadas, agora crie o próprio cluster AKS. Primeiro, criar um grupo de recursos usando o [grupo AZ criar][az-group-create] comando. O exemplo seguinte cria o grupo de recursos na região *leste:*

Criar um grupo de recursos para o cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obtenha a identificação do inquilino da sua assinatura Azure usando o comando de [show de conta az.][az-account-show] Em seguida, criar o cluster AKS usando as [aks az criar][az-aks-create] comando. O comando para criar o cluster AKS fornece os IDs de aplicação do servidor e do cliente, o principal segredo do serviço de aplicação do servidor e o seu ID do inquilino:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Finalmente, obtenha as credenciais de administração do cluster usando o comando [az aks get-credentials.][az-aks-get-credentials] Numa das seguintes etapas, obtém-se as credenciais regulares de cluster de *utilizadores* para ver o fluxo de autenticação da AD Azure em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Criar ligação RBAC

Antes de uma conta azure Ative Diretório poder ser usada com o cluster AKS, é necessário criar uma função vinculativa ou de ligação de cluster. *As funções* definem as permissões de concessão e as *encadernações* aplicam-nas aos utilizadores pretendidos. Estas atribuições podem ser aplicadas a um determinado espaço de nome, ou em todo o cluster. Para mais informações, consulte [a utilização da autorização RBAC][rbac-authorization].

Obtenha o nome principal do utilizador (UPN) para o utilizador atualmente iniciado na utilização do comando de [show de az ad ad-signed-in-user.][az-ad-signed-in-user-show] Esta conta de utilizador está ativada para a integração de AD Azure no próximo passo.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se o utilizador que concede a ligação RBAC estiver no mesmo inquilino Azure AD, atribua permissões com base no *nome principal*do utilizador . Se o utilizador estiver num inquilino Azure AD diferente, questione e utilize a propriedade *objectId.*

Crie um manifesto YAML chamado `basic-azure-ad-binding.yaml` e cola os seguintes conteúdos. Na última linha, substitua *userPrincipalName_or_objectId* com a saída UPN ou id do objeto do comando anterior:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Crie o ClusterRoleBinding utilizando o comando de aplicação do [kubectl][kubectl-apply] e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Cluster de acesso com Azure AD

Agora vamos testar a integração da autenticação Azure AD para o cluster AKS. Delineie o contexto `kubectl` config para utilizar credenciais regulares de utilizador. Este contexto passa todos os pedidos de autenticação através da Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Agora use o [kubectl obter comando de casulos][kubectl-get] para ver cápsulas em todos os espaços de nome:

```console
kubectl get pods --all-namespaces
```

Recebe um sinal de autenticação solicitado para autenticar usando credenciais de AD Azure usando um navegador web. Depois de autenticar com sucesso, o comando `kubectl` exibe as cápsulas no cluster AKS, como mostra a seguinte saída de exemplo:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

O símbolo de autenticação recebido para `kubectl` está em cache. Só é solicitado para iniciar sessão quando o token tiver expirado ou o ficheiro config Kubernetes for recriado.

Se vir uma mensagem de erro de autorização depois de ter assinado com sucesso a utilização de um navegador web como na seguinte saída de exemplo, verifique os seguintes problemas possíveis:

```output
error: You must be logged in to the server (Unauthorized)
```

* Definiu o ID ou UPN de objeto apropriado, dependendo se a conta de utilizador está ou não no mesmo inquilino Azure AD.
* O utilizador não é membro de mais de 200 grupos.
* Segredo definido no registo de aplicação para servidor corresponde ao valor configurado usando `--aad-server-app-secret`

## <a name="next-steps"></a>Passos seguintes

Para o script completo que contém os comandos mostrados neste artigo, consulte o script de integração da [AD Azure nas amostras AKS repo][complete-script].

Para utilizar utilizadores e grupos da AD Azure para controlar o acesso aos recursos de cluster, consulte o controlo do acesso aos recursos de cluster utilizando o [controlo de acesso baseado em papéis e identidades azure ad em AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters kubernetes, consulte opções de [acesso e identidade para AKS)][rbac-authorization].

Para obter as melhores práticas de controlo de identidade e recursos, consulte [as melhores práticas de autenticação e autorização no AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
