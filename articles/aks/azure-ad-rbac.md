---
title: Use Azure AD e Kubernetes RBAC para clusters
titleSuffix: Azure Kubernetes Service
description: Saiba como utilizar a adesão do grupo Azure Ative Directory para restringir o acesso aos recursos de cluster utilizando o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC) no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769558"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controlar o acesso aos recursos de cluster utilizando o controlo de acesso baseado em funções da Kubernetes e as identidades do Azure Ative Directory no Serviço Azure Kubernetes

O Serviço Azure Kubernetes (AKS) pode ser configurado para utilizar o Azure Ative Directory (AD) para a autenticação do utilizador. Nesta configuração, você insinua-se num cluster AKS usando um token de autenticação AD Azure. Também pode configurar o controlo de acesso baseado em funções (Kubernetes RBAC) para limitar o acesso a recursos de cluster baseados na identidade ou membro do grupo de um utilizador.

Este artigo mostra-lhe como usar a filiação do grupo AD Azure para controlar o acesso a espaços de nome e recursos de cluster usando o RBAC de Kubernetes num cluster AKS. Grupos de exemplo e utilizadores são criados em Azure AD, em seguida, Roles e RoleBindings são criados no cluster AKS para conceder as permissões apropriadas para criar e visualizar recursos.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um cluster AKS existente habilitado com a integração AD AZure. Se precisar de um cluster AKS, consulte [o Diretório Ativo Integrado Azure com AKS][azure-ad-aks-cli].

Precisa da versão 2.0.61 do Azure CLI ou posteriormente instalada e configurada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Criar grupos de demonstração em Azure AD

Neste artigo, vamos criar duas funções de utilizador que podem ser usadas para mostrar como Kubernetes RBAC e Azure AD controlam o acesso aos recursos do cluster. São utilizadas as seguintes duas funções de exemplo:

* **Desenvolvedor de aplicações**
    * Um utilizador chamado *aksdev* que faz parte do grupo *appdev.*
* **Engenheiro de fiabilidade do site**
    * Um utilizador chamado *Akssre* que faz parte do grupo *opssre.*

Em ambientes de produção, você pode usar utilizadores e grupos existentes dentro de um inquilino AZure AD.

Primeiro, obtenha a identificação de recursos do seu cluster AKS usando o comando [de show az aks.][az-aks-show] Atribua o ID do recurso a uma variável nomeada *AKS_ID* para que possa ser referenciado em comandos adicionais.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Crie o primeiro grupo de exemplo em Azure AD para os desenvolvedores de aplicações que usam o [grupo az criar][az-ad-group-create] comando. O exemplo a seguir cria um grupo chamado *appdev:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Agora, crie uma atribuição de papel Azure para o grupo *appdev* usando a [atribuição de funções az criar][az-role-assignment-create] comando. Esta atribuição permite que qualquer membro do grupo `kubectl` interaja com um cluster AKS, concedendo-lhes o *Papel de Utilizador do Cluster de Serviço Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se receber um erro como `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , aguarde alguns segundos para que o ID do grupo Azure AD se propague através do diretório e tente novamente o `az role assignment create` comando.

Crie um segundo grupo de exemplo, este para SREs nomeado *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Mais uma vez, crie uma atribuição de papel Azure para conceder aos membros do grupo o *Azure Kubernetes Service Cluster User Role*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Criar utilizadores de demonstração em Azure AD

Com dois exemplos criados em Azure AD para os nossos desenvolvedores de aplicações e SREs, agora permite criar dois utilizadores exemplo. Para testar a integração do RBAC de Kubernetes no final do artigo, você inicia súm em o cluster AKS com estas contas.

Desa estada o nome principal do utilizador (UPN) e a palavra-passe para os desenvolvedores de aplicações. O seguinte comando solicita-lhe que o UPN e o define para *AAD_DEV_UPN* para utilização num comando posterior (lembre-se que os comandos deste artigo são introduzidos numa concha BASH). A UPN deve incluir o nome de domínio verificado do seu inquilino, por `aksdev@contoso.com` exemplo.

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

O seguinte comando solicita-lhe a palavra-passe e define-a para *AAD_DEV_PW* para utilização num comando posterior.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Crie a primeira conta de utilizador em Azure AD utilizando o [comando do utilizador az ad.][az-ad-user-create]

O exemplo a seguir cria um utilizador com o nome de ecrã *AKS Dev* e a UPN e protege a palavra-passe utilizando os valores em *AAD_DEV_UPN* e *AAD_DEV_PW:*

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Adicione agora o utilizador ao grupo *appdev* criado na secção anterior utilizando o comando de adicionar membro do [grupo az:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Desafine a UPN e a palavra-passe para SREs. O seguinte comando solicita-lhe que o UPN e o define para *AAD_SRE_UPN* para utilização num comando posterior (lembre-se que os comandos deste artigo são introduzidos numa concha BASH). A UPN deve incluir o nome de domínio verificado do seu inquilino, por `akssre@contoso.com` exemplo.

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

O seguinte comando solicita-lhe a palavra-passe e define-a para *AAD_SRE_PW* para utilização num comando posterior.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Criar uma segunda conta de utilizador. O exemplo a seguir cria um utilizador com o nome de ecrã *AKS SRE* e a UPN e protege a palavra-passe utilizando os valores em *AAD_SRE_UPN* e *AAD_SRE_PW:*

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Criar os recursos de cluster AKS para devs de aplicações

Os grupos AD Azure e os utilizadores são agora criados. Foram criadas atribuições de funções Azure para que os membros do grupo se conectem a um cluster AKS como utilizador regular. Agora, vamos configurar o cluster AKS para permitir a estes diferentes grupos o acesso a recursos específicos.

Primeiro, obtenha as credenciais de administrador de cluster usando o comando [az aks get-credentials.][az-aks-get-credentials] Numa das seguintes secções, obtém-se as credenciais regulares do cluster do *utilizador* para ver o fluxo de autenticação AD AZure em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Crie um espaço de nome no cluster AKS usando o comando de espaço de [nome de criação de kubectl.][kubectl-create] O exemplo a seguir cria um *nome dev:*

```console
kubectl create namespace dev
```

Em Kubernetes, *as funções* definem as permissões a conceder, e *os RoleBindings* aplicam-nas aos utilizadores ou grupos pretendidos. Estas atribuições podem ser aplicadas a um determinado espaço de nome, ou em todo o cluster. Para obter mais informações, consulte [a autorização do RBAC da Kubernetes.][rbac-authorization]

Primeiro, crie um Papel para o espaço de nome *dev.* Esta função concede permissões completas ao espaço de nomes. Em ambientes de produção, pode especificar mais permissões granulares para diferentes utilizadores ou grupos.

Crie um ficheiro com o nome `role-dev-namespace.yaml` e cole o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a Função utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Em seguida, obtenha o ID de recursos para o grupo *appdev* usando o comando [de show de grupo az ad.][az-ad-group-show] Este grupo é definido como tema de um RoleBinding no próximo passo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Agora, crie um RoleBinding para o grupo *appdev* usar o Role anteriormente criado para o acesso ao espaço de nome. Crie um ficheiro com o nome `rolebinding-dev-namespace.yaml` e cole o seguinte manifesto YAML. Na última linha, substitua o *grupoObjectId*  pela saída de ID do objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Crie o RoleBinding utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Criar os recursos de cluster AKS para SREs

Agora, repita os passos anteriores para criar um espaço de nome, role e roleBinding para as SREs.

Primeiro, crie um espaço de nome para *sre* usando o comando de espaço de [nome de criação de kubectl:][kubectl-create]

```console
kubectl create namespace sre
```

Crie um ficheiro com o nome `role-sre-namespace.yaml` e cole o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a Função utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Obtenha o ID de recursos para o grupo *opssre* usando o comando [de show de grupo az ad:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Crie um RoleBinding para o grupo *opssre* usar o Papel previamente criado para o acesso ao espaço de nome. Crie um ficheiro com o nome `rolebinding-sre-namespace.yaml` e cole o seguinte manifesto YAML. Na última linha, substitua o *grupoObjectId*  pela saída de ID do objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Crie o RoleBinding utilizando o comando [de aplicação de kubectl][kubectl-apply] e especifique o nome de ficheiro do seu manifesto YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagir com os recursos do cluster utilizando identidades Azure AD

Agora, vamos testar as permissões esperadas funcionam quando crias e geres recursos num cluster AKS. Nestes exemplos, você agenda e visualiza pods no espaço de nome atribuído pelo utilizador. Em seguida, você tenta agendar e visualizar cápsulas fora do espaço de nome atribuído.

Em primeiro lugar, reinicie o contexto *kubeconfig* usando o comando [az aks get-credentials.][az-aks-get-credentials] Numa secção anterior, define-se o contexto utilizando as credenciais de administração do cluster. O utilizador administrativo ignora o sinal Azure AD em solicitações. Sem o `--admin` parâmetro, é aplicado o contexto do utilizador que requer que todos os pedidos sejam autenticados usando Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Agende um pod NGINX básico utilizando o comando [de corrida de kubectl][kubectl-run] no espaço de nome *dev:*

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Como sinal em imediato, insira as credenciais para a sua própria `appdev@contoso.com` conta criada no início do artigo. Uma vez assinado com sucesso, o token da conta está em cache para futuros `kubectl` comandos. O NGINX está agendado com sucesso, como mostra a seguinte saída de exemplo:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Agora use o [comando de pods de kubectl][kubectl-get] para visualizar cápsulas no espaço de nome *dev.*

```console
kubectl get pods --namespace dev
```

Como mostrado na saída do exemplo seguinte, o pod NGINX está *a funcionar* com sucesso:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Criar e visualizar recursos de cluster fora do espaço de nome atribuído

Agora tente ver cápsulas fora do espaço de nome *dev.* Use o [comando de pods de kubectl][kubectl-get] novamente, desta vez para ver `--all-namespaces` o seguinte:

```console
kubectl get pods --all-namespaces
```

A adesão do grupo do utilizador não tem um Papel Kubernetes que permita esta ação, como mostra a seguinte saída de exemplo:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Da mesma forma, tente agendar uma cápsula em diferentes espaços de nome, como o espaço de nome *sre.* A adesão do utilizador ao grupo não se alinha com um Papel e RoleBinding da Kubernetes para conceder estas permissões, como mostra a seguinte saída de exemplo:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testar o acesso SRE aos recursos de cluster AKS

Para confirmar que a nossa filiação ao grupo AZure AD e a NCC de Kubernetes funcionam corretamente entre diferentes utilizadores e grupos, experimente os comandos anteriores quando se inscrever como utilizador *do opssre.*

Reinicie o contexto *kubeconfig* utilizando o comando [az aks get-credentials][az-aks-get-credentials] que limpa o token de autenticação previamente em cache para o utilizador *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Tente agendar e ver cápsulas no espaço de nome *sre* atribuído. Quando solicitado, inicie sedução com as suas `opssre@contoso.com` credenciais criadas no início do artigo:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Como mostrado na saída do exemplo a seguir, pode criar e visualizar com sucesso as cápsulas:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Agora, tente visualizar ou agendar cápsulas fora do espaço de nome sRE atribuído:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Estes `kubectl` comandos falham, como mostra a saída do exemplo seguinte. A filiação do grupo do utilizador e a Kubernetes Role and RoleBindings não concedem permissões para criar ou gerenciar recursos em outros espaços de nome:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Limpar os recursos

Neste artigo, criou recursos no cluster AKS e utilizadores e grupos em Azure AD. Para limpar todos estes recursos, executar os seguintes comandos:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre como proteger os clusters kubernetes, consulte [opções de Acesso e identidade para AKS)][rbac-authorization].

Para obter as melhores práticas em matéria de identidade e controlo de recursos, consulte [as melhores práticas de autenticação e autorização em AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
