---
title: Integrar o Azure Active Directory com o Azure Kubernetes Service
description: Saiba como utilizar o Azure CLI para criar e a Azure Ative Directory Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 83ba43c3b8a00325750ec935fd3a43ec7d56074c
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85336537"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integre o Azure Ative Directy com o Serviço Azure Kubernetes utilizando o Azure CLI

O Serviço Azure Kubernetes (AKS) pode ser configurado para utilizar o Azure Ative Directory (AD) para a autenticação do utilizador. Nesta configuração, pode iniciar sessão num cluster AKS utilizando um token de autenticação AD Azure. Os operadores de cluster também podem configurar o controlo de acesso baseado em funções (RBAC) baseado em funções com base na identidade de um utilizador ou membro do grupo de diretório.

Este artigo mostra-lhe como criar os componentes AD Azure necessários, em seguida, implementar um cluster AD ativado por AZure e criar um papel de RBAC básico no cluster AKS.

Para obter o roteiro completo da amostra utilizado neste artigo, consulte [as amostras do Azure CLI - integração AKS com Azure AD][complete-script].

Aplicam-se as seguintes limitações:

- O AZure AD só pode ser ativado quando se cria um novo cluster ativado pelo RBAC. Não é possível ativar o AD AZure num cluster AKS existente.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

Vá [https://shell.azure.com](https://shell.azure.com) para abrir a Cloud Shell no seu navegador.

Para obter consistência e para ajudar a executar os comandos neste artigo, crie uma variável para o seu nome de cluster AKS desejado. O exemplo a seguir utiliza o nome *myakscluster:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação AD do Azure

A autenticação AZure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de ligação Open ID][open-id-connect].

A partir do interior do cluster Kubernetes, a Autenticação Token Webhook é usada para verificar fichas de autenticação. A autenticação de ficha webhook é configurada e gerida como parte do cluster AKS. Para obter mais informações sobre a autenticação do token Webhook, consulte a [documentação de autenticação webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar a Ad Azure para a autenticação AKS, duas aplicações AD Azure são configuradas. Esta operação deve ser concluída por um administrador inquilino da Azure.

## <a name="create-azure-ad-server-component"></a>Criar componente de servidor de anúncios Azure

Para se integrar com a AKS, cria e utiliza uma aplicação AD Azure que funciona como um ponto final para os pedidos de identidade. A primeira aplicação AD AZure que precisa recebe a adesão do grupo AD Azure para um utilizador.

Crie o componente de aplicação do servidor utilizando o comando [de criação de aplicação az][az-ad-app-create] e, em seguida, atualize as reivindicações de membros do grupo utilizando o comando [de atualização de aplicações az ad.][az-ad-app-update] O exemplo a seguir utiliza a variável *aksname* definida na secção [Antes de começar,](#before-you-begin) e cria uma variável

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Agora crie um principal de serviço para a aplicação do servidor usando o comando [ad sp ad sp.][az-ad-sp-create] Este diretor de serviço é utilizado para se autenticar dentro da plataforma Azure. Em seguida, obtenha o segredo principal do serviço usando o comando [de reset credencial ad sp][az-ad-sp-credential-reset] e atribua à variável nomeada *serverApplicationSecret* para ser usado em um dos seguintes passos:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

A AZure AD necessita de permissões para executar as seguintes ações:

* Ler os dados do diretório
* Iniciar sessão e ler o perfil de utilizador

Atribua estas permissões utilizando o comando [de adicionar a permissão da app az ad:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Por fim, conceda as permissões atribuídas no passo anterior para a aplicação do servidor utilizando o comando [de concessão de permissão de ad az.][az-ad-app-permission-grant] Este passo falha se a conta corrente não for um administrador inquilino. Também precisa adicionar permissões para a aplicação Azure AD para solicitar informações que de outra forma possam exigir consentimento administrativo usando a permissão de [ad app ad-consent][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Criar componente de cliente AZure AD

A segunda aplicação AD AZure é utilizada quando um utilizador faz login no cluster AKS com o CLI de Kubernetes ( `kubectl` ). Esta aplicação do cliente recebe o pedido de autenticação do utilizador e verifica as suas credenciais e permissões. Crie a aplicação AD AZure para a componente do cliente utilizando o comando [da app az ad create:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Criar um principal de serviço para a aplicação do cliente utilizando o comando [ad sp ad sp:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obtenha o ID oAuth2 para a aplicação do servidor para permitir o fluxo de autenticação entre os dois componentes da aplicação utilizando o comando [de show de aplicações az ad.][az-ad-app-show] Este iD oAuth2 é usado no passo seguinte.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adicione as permissões para que a aplicação do cliente e os componentes da aplicação do servidor utilizem o fluxo de comunicação oAuth2 utilizando o comando [de adicionar a az ad app.][az-ad-app-permission-add] Em seguida, conceda permissões para a aplicação do cliente para comunicar com a aplicação do servidor usando o comando [de concessão de permissão de ad app az:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Implementar o cluster

Com as duas aplicações AD AZure criadas, agora crie o cluster AKS em si. Em primeiro lugar, crie um grupo de recursos utilizando o [grupo az criar][az-group-create] comando. O exemplo a seguir cria o grupo de recursos na região *leste:*

Criar um grupo de recursos para o cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obtenha a identificação do inquilino da sua assinatura Azure usando o comando [de show de conta az.][az-account-show] Em seguida, crie o cluster AKS usando as [az aks criar][az-aks-create] comando. O comando para criar o cluster AKS fornece os IDs de aplicação do servidor e do cliente, o segredo principal do serviço de aplicação do servidor e o seu ID do seu inquilino:

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

Finalmente, obtenha as credenciais de administrador de cluster usando o comando [az aks get-credentials.][az-aks-get-credentials] Num dos passos seguintes, obtém-se as credenciais regulares do cluster do *utilizador* para ver o fluxo de autenticação AD AZure em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Criar ligação RBAC

Antes de uma conta Azure Ative Directory poder ser usada com o cluster AKS, é necessário criar uma ligação de funções ou de cluster. *As funções* definem as permissões a conceder e *as ligações* aplicam-nas aos utilizadores desejados. Estas atribuições podem ser aplicadas a um determinado espaço de nome, ou em todo o cluster. Para obter mais informações, consulte [a autorização do RBAC.][rbac-authorization]

Obtenha o nome principal do utilizador (UPN) para o utilizador que está atualmente a iniciar sessão utilizando o comando [de exibição assinado no anúncio az.][az-ad-signed-in-user-show] Esta conta de utilizador está ativada para a integração da AD Azure no passo seguinte.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se o utilizador que concede a ligação RBAC estiver no mesmo inquilino AD Azure, atribua permissões com base no *nome de utilizadorPrincipalName*. Se o utilizador estiver num inquilino AD Azure diferente, consultar e utilizar a propriedade *objectId.*

Crie um manifesto YAML nomeado `basic-azure-ad-binding.yaml` e cole os seguintes conteúdos. Na última linha, substitua *userPrincipalName_or_objectId* pela saída de ID upn ou objeto do comando anterior:

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

Crie o ClusterRoleBinding utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Cluster de acesso com Azure AD

Agora vamos testar a integração da autenticação AZure AD para o cluster AKS. Desconfig o `kubectl` contexto config para utilizar credenciais regulares do utilizador. Este contexto transmite todos os pedidos de autenticação através do Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Agora use o [comando de pods kubectl][kubectl-get] para visualizar cápsulas em todos os espaços de nome:

```console
kubectl get pods --all-namespaces
```

Você recebe um sinal em solicitação para autenticar usando credenciais Azure AD usando um navegador web. Depois de autenticado com sucesso, o `kubectl` comando exibe as cápsulas no cluster AKS, como mostra a seguinte saída de exemplo:

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

O sinal de autenticação recebido `kubectl` é em cache. Só é repremiado para iniciar sação quando o token tiver expirado ou se o ficheiro config de Kubernetes for recriado.

Se vir uma mensagem de erro de autorização depois de ter assinado com sucesso a utilização de um navegador web como na saída do exemplo seguinte, verifique os seguintes problemas possíveis:

```output
error: You must be logged in to the server (Unauthorized)
```

* Definiu o ID ou UPN do objeto apropriado, dependendo se a conta de utilizador está no mesmo inquilino AD AZure ou não.
* O utilizador não é membro de mais de 200 grupos.
* Segredo definido no registo de candidatura para servidor corresponde ao valor configurado usando`--aad-server-app-secret`

## <a name="next-steps"></a>Passos seguintes

Para obter o roteiro completo que contém os comandos mostrados neste artigo, consulte o script de [integração AD AD AZure nas amostras AKS repo][complete-script].

Para utilizar utilizadores e grupos de AD Azure para controlar o acesso aos recursos do cluster, consulte [o controlo do acesso aos recursos do cluster utilizando o controlo de acesso baseado em funções e identidades AD AD em AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters kubernetes, consulte [opções de Acesso e identidade para AKS)][rbac-authorization].

Para obter as melhores práticas em matéria de identidade e controlo de recursos, consulte [as melhores práticas de autenticação e autorização em AKS.][operator-best-practices-identity]

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
