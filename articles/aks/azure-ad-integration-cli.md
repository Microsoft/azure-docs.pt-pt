---
title: Integrar o Azure Active Directory com o serviço Kubernetes do Azure
description: Saiba como utilizar a CLI do Azure para criar e o cluster de ativado o Azure Active Directory do Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: 0216a8c7d4e52e89098979223e9b792398e25038
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920172"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrar o Azure Active Directory com o serviço de Kubernetes do Azure com a CLI do Azure

O Azure Kubernetes Service (AKS) pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Nesta configuração, pode iniciar sessão num cluster do AKS com um token de autenticação do Azure AD. Operadores de cluster também podem configurar o controlo de acesso baseado em função do Kubernetes (RBAC) com base na associação de grupo de identidade ou o diretório de um utilizador.

Este artigo mostra-lhe como criar necessários componentes do Azure AD, em seguida, implementar um cluster do Azure AD habilitado e criar uma função RBAC básica do cluster do AKS. Também pode [concluir estes passos no portal do Azure][azure-ad-portal].

Para o script de exemplo completo utilizado neste artigo, consulte [exemplos da CLI do Azure - integração de AKS com o Azure AD][complete-script].

As seguintes limitações aplicam-se:

- O Azure AD só pode ser ativado ao criar um cluster novo, habilitados no RBAC. Não é possível ativar o Azure AD num cluster do AKS existente.
- *Convidado* utilizadores no Azure AD, tal como se usar um início de sessão Federado de outro diretório, não são suportadas.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

Para manter a consistência e para ajudar a executar os comandos neste artigo, crie uma variável para o nome do cluster AKS pretendido. O exemplo seguinte utiliza o nome *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Descrição geral da autenticação do Azure AD

Autenticação do Azure AD é fornecida para clusters do AKS com OpenID Connect. OpenID Connect é uma camada de identidade criada com base no protocolo de OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [abrir ID connect documentação][open-id-connect].

De dentro do cluster de Kubernetes, autenticação de Token do Webhook é usada para verificar os tokens de autenticação. Autenticação de token do Webhook é configurada e gerenciada como parte do AKS cluster. Para obter mais informações sobre a autenticação de token do Webhook, veja a [documentação de autenticação de webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicação do Azure AD estão configurados. Esta operação deve ser concluída por um administrador de inquilino do Azure.

## <a name="create-azure-ad-server-component"></a>Criar o componente de servidor do Azure AD

Para integrar com o AKS, pode criar e utiliza uma aplicação do Azure AD que age como um ponto final para os pedidos de identidade. A primeira aplicação do Azure AD que tem de obtém a associação a grupos do Azure AD de um utilizador.

Crie a aplicação de servidor componente com o [criar az ad app] [ az-ad-app-create] comando, em seguida, atualizar a associação a grupos de afirmações utilizando o [atualização do az ad app] [ az-ad-app-update] comando. O exemplo seguinte utiliza a *aksname* variável definida a [antes de começar](#before-you-begin) secção e cria uma variável

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Agora, criar um principal de serviço para a aplicação de servidor com o [az ad sp crie] [ az-ad-sp-create] comando. Este principal de serviço é utilizado para autenticar-se na plataforma do Azure. Em seguida, obter o principal de serviço secreta através a [credencial do az ad sp reposição] [ az-ad-sp-credential-reset] de comandos e atribuir à variável com o nome *serverApplicationSecret* para utilização do passos seguintes:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

O Azure AD necessita de permissões para executar as seguintes ações:

* Ler dados do diretório
* Iniciar sessão e ler perfil do utilizador

Atribuir estas permissões com o [adicionar permissão da aplicação ad az] [ az-ad-app-permission-add] comando:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Por fim, conceder as permissões atribuídas no passo anterior para a aplicação de servidor utilizando o [concessão de permissões do az ad app] [ az-ad-app-permission-grant] comando. Este passo falhe se a conta atual não é um administrador inquilino. Também precisa de adicionar permissões para a aplicação do Azure AD solicitar informações que caso contrário, podem requerer a utilização de consentimento administrativo a [az ad app permissão-consentimento de administrador][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Criar o componente de cliente do Azure AD

A segunda aplicação do Azure AD é utilizada quando um utilizador iniciar sessão para o cluster do AKS com a CLI do Kubernetes (`kubectl`). Esta aplicação de cliente recebe o pedido de autenticação do usuário e verifica as suas credenciais e permissões. Criar a aplicação do Azure AD para o componente de cliente que utilize o [az ad app crie] [ az-ad-app-create] comando:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Criar um principal de serviço para a aplicação de cliente que utilize o [az ad sp crie] [ az-ad-sp-create] comando:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obter o ID de oAuth2 para a aplicação de servidor para permitir que o fluxo de autenticação entre os componentes de duas aplicações com o [show do az ad app] [ az-ad-app-show] comando. Este ID de oAuth2 é utilizado no próximo passo.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adicione as permissões para a aplicação de cliente e componentes da aplicação de servidor para utilizar a comunicação de oAuth2 fluxo usando a [adicionar permissão da aplicação ad az] [ az-ad-app-permission-add] comando. Em seguida, conceder permissões para a aplicação de cliente para comunicação com a aplicação de servidor com o [concessão de permissões do az ad app] [ az-ad-app-permission-grant] comando:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Implementar o cluster

Com as duas aplicações do Azure AD criadas, agora, crie o próprio cluster do AKS. Primeiro, crie um grupo de recursos utilizando o [criar grupo az] [ az-group-create] comando. O exemplo seguinte cria o grupo de recursos no *EastUS* região:

Crie um grupo de recursos para o cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obter o ID de inquilino da sua subscrição do Azure com o [show de conta de az] [ az-account-show] comando. Em seguida, crie o cluster do AKS com o [criar az aks] [ az-aks-create] comando. O comando para criar o cluster do AKS fornece o servidor e cliente IDs da aplicação, o segredo de principal do serviço de aplicações de servidor e o seu ID de inquilino:

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

Por fim, obtenha o cluster de credenciais de administrador com o [az aks get-credentials] [ az-aks-get-credentials] comando. Em um dos seguintes passos, obtém regular *utilizador* as credenciais para ver a autenticação do Azure AD de cluster fluxo em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Criar o enlace de RBAC

Antes de uma conta do Azure Active Directory pode ser utilizada com o cluster do AKS, um enlace de função de cluster ou enlace de função tem de ser criado. *Funções* definir as permissões para conceder, e *enlaces* aplicá-las para os usuários desejados. Estas atribuições podem ser aplicadas a um determinado espaço de nomes ou em todo o cluster. Para obter mais informações, consulte [autorização RBAC usando][rbac-authorization].

Obter o nome principal de utilizador (UPN), para o usuário atualmente sessão iniciada, utilizando o [show de sessão iniciada no utilizador do ad az] [ az-ad-signed-in-user-show] comando. Esta conta de utilizador está ativada para a integração do Azure AD no próximo passo.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se o utilizador pode conceder a vinculação de RBAC para estiver no mesmo inquilino do Azure AD, atribuir permissões com base no *userPrincipalName*. Se o utilizador estiver no Azure AD com um diferente de inquilino, consultar e utilizar o *objectId* propriedade em vez disso.

Criar um manifesto YAML com o nome `basic-azure-ad-binding.yaml` e cole o seguinte conteúdo. Na última linha, substitua *userPrincipalName_or_objectId* com o UPN ou o objeto de saída do ID de comando anterior:

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

Crie a ClusterRoleBinding com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome de ficheiro do seu manifesto YAML de:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Cluster de acesso com o Azure AD

Agora vamos testar a integração da autenticação do Azure AD para o cluster do AKS. Definir o `kubectl` contexto de configuração para utilizar credenciais de utilizador normal. Neste contexto passa todos os pedidos de autenticação através do Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Agora utilizar o [kubectl obter pods] [ kubectl-get] comando para ver os pods em todos os espaços de nomes:

```console
kubectl get pods --all-namespaces
```

Receberá um início de sessão na linha de comandos para efetuar a autenticação com credenciais do Azure AD com um navegador da web. Depois de autenticado com êxito, o `kubectl` comando apresenta os pods do cluster do AKS, conforme mostrado no seguinte exemplo:

```console
$ kubectl get pods --all-namespaces

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

O token de autenticação recebidos para `kubectl` é colocado em cache. Apenas são reprompted de iniciar sessão quando o token expirou ou o ficheiro de configuração Kubernetes seja recriado.

Se vir uma mensagem de erro de autorização, depois de se com êxito usando um navegador da web como na saída de exemplo seguinte, verifique os seguintes problemas possíveis:

```console
error: You must be logged in to the server (Unauthorized)
```

* O utilizador tem sessão iniciada no não é um *convidado* na instância do Azure AD (isto é, muitas vezes, o caso se utilizar um início de sessão federado a partir de um diretório diferente).
* O utilizador não é um membro de mais de 200 grupos.

## <a name="next-steps"></a>Passos Seguintes

Para o script completo que contém os comandos mostrados neste artigo, consulte a [repositório de exemplos de script de integração do Azure AD no AKS][complete-script].

Para utilizar grupos e utilizadores do Azure AD para controlar o acesso aos recursos do cluster, consulte [controlar o acesso aos recursos do cluster com o controlo de acesso baseado em funções e identidades do Azure AD no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters do Kubernetes, consulte [opções de acesso e identidade para o AKS)][rbac-authorization].

Para melhores práticas no controlo de identidade e de recursos, consulte [melhores práticas para autenticação e autorização no AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
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
