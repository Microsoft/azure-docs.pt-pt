---
title: Recursos de cluster de controle com o RBAC e o Azure AD no Azure Kubernetes Service
description: Saiba como utilizar a associação de grupo do Azure Active Directory para restringir o acesso aos recursos do cluster utilizando o controlo de acesso baseado em funções (RBAC) no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: e974c47d1dfb04f66b622c64a7143d00de87c4cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467549"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controlar o acesso a recursos de cluster através de controlo de acesso baseado em funções e as identidades do Azure Active Directory no Azure Kubernetes Service

O Azure Kubernetes Service (AKS) pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Nesta configuração, fazer logon para um cluster do AKS com um token de autenticação do Azure AD. Também pode configurar Kubernetes o controlo de acesso baseado em funções (RBAC) para limitar o acesso aos recursos do cluster com base em ou associação um utilizador a grupos.

Este artigo mostra-lhe como utilizar a associação a grupos do Azure AD para controlar o acesso aos espaços de nomes e os recursos através do RBAC de Kubernetes num cluster do AKS do cluster. Exemplo de grupos e utilizadores são criados no Azure AD, em seguida, funções e RoleBindings são criados no cluster do AKS para conceder as permissões adequadas para criar e ver os recursos.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem um cluster do AKS existente ativado com a integração do Azure AD. Se precisar de um cluster do AKS, veja [integrar o Azure Active Directory com o AKS][azure-ad-aks-cli].

Precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Criar grupos de demonstração no Azure AD

Neste artigo, vamos criar duas funções de utilizador que podem ser utilizadas para mostrar como o RBAC de Kubernetes e o Azure AD controlam o acesso aos recursos do cluster. As seguintes funções de duas exemplo são utilizadas:

* **Desenvolvedor de aplicativos**
    * Um utilizador com o nome *aksdev* que faz parte da *appdev* grupo.
* **Engenheiro de fiabilidade de site**
    * Um utilizador com o nome *akssre* que faz parte da *opssre* grupo.

Em ambientes de produção, pode usar os utilizadores e grupos dentro de um inquilino do Azure AD.

Primeiro, obtenha o ID de recurso do seu cluster do AKS com o [show do az aks] [ az-aks-show] comando. Atribuir o ID de recurso a uma variável chamada *AKS_ID* para que possam ser referenciados em comandos adicionais.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Criar o primeiro grupo de exemplo no Azure AD para os desenvolvedores de aplicativos com o [criar grupo do ad az] [ az-ad-group-create] comando. O exemplo seguinte cria um grupo denominado *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Agora, crie uma atribuição de função do Azure para o *appdev* grupo utilizando o [criação da atribuição de função de az] [ az-role-assignment-create] comando. Esta atribuição permite que qualquer membro do grupo, utilize `kubectl` para interagir com um cluster do AKS, concedendo-lhes a *função de utilizador do Cluster do Azure Kubernetes Service*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se receber um erro, como `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, aguarde alguns segundos para o ID de objeto de grupo do Azure AD propagar através do diretório, em seguida, tente o `az role assignment create` novamente o comando.

Criar um segundo grupo de exemplo, com o nome de uma para SREs *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Novamente, criar uma atribuição de função do Azure para membros do grupo de conceder a *função de utilizador do Cluster do Azure Kubernetes Service*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Criar utilizadores de demonstração no Azure AD

Com dois grupos de exemplo criados no Azure AD para os nossos desenvolvedores de aplicativos e SREs, agora, vamos criar dois utilizadores de exemplo. Para testar a integração de RBAC no final do artigo, entrar para o cluster do AKS com estas contas.

Criar a primeira conta de utilizador no Azure AD com o [criar utilizador do ad az] [ az-ad-user-create] comando.

O exemplo seguinte cria um utilizador com o nome a apresentar *AKS Dev* e o nome principal de utilizador (UPN) do `aksdev@contoso.com`. Atualizar o UPN para incluir um domínio verificado do seu inquilino do Azure AD (substitua *contoso.com* com o seu próprio domínio) e fornecer sua própria seguro `--password` credencial:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Agora, adicione o utilizador para o *appdev* grupo criado a secção anterior com o [Adicionar membro do grupo az ad] [ az-ad-group-member-add] comando:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Crie uma segunda conta de utilizador. O exemplo seguinte cria um utilizador com o nome a apresentar *AKS SRE* e o nome principal de utilizador (UPN) do `akssre@contoso.com`. Novamente, atualize o UPN para incluir um domínio verificado do seu inquilino do Azure AD (substitua *contoso.com* com o seu próprio domínio) e fornecer sua própria seguro `--password` credencial:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Criar os recursos de cluster do AKS para os programadores de aplicações

Os grupos do Azure AD e os utilizadores agora são criados. Atribuições de funções do Azure foram criadas para os membros do grupo ligar a um cluster do AKS como um usuário normal. Agora, vamos configurar o cluster do AKS para permitir o acesso desses grupos diferentes a recursos específicos.

Primeiro, obtenha o cluster de credenciais de administrador com o [az aks get-credentials] [ az-aks-get-credentials] comando. Uma das seções a seguir, obtém regular *utilizador* as credenciais para ver a autenticação do Azure AD de cluster fluxo em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Criar um espaço de nomes no cluster do AKS com o [kubectl criar espaço de nomes] [ kubectl-create] comando. O exemplo seguinte cria um espaço de nomes *dev*:

```console
kubectl create namespace dev
```

No Kubernetes, *funções* definir as permissões para conceder, e *RoleBindings* aplicá-las a pretendido de utilizadores ou grupos. Estas atribuições podem ser aplicadas a um determinado espaço de nomes ou em todo o cluster. Para obter mais informações, consulte [autorização RBAC usando][rbac-authorization].

Primeiro, crie uma função para o *dev* espaço de nomes. Esta função concede permissões completas para o espaço de nomes. Em ambientes de produção, pode especificar permissões mais granulares para diferentes utilizadores ou grupos.

Crie um ficheiro denominado `role-dev-namespace.yaml` e cole o manifesto YAML seguinte:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Crie a função com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome de ficheiro do seu manifesto YAML de:

```console
kubectl apply -f role-dev-namespace.yaml
```

Em seguida, obter o ID de recurso para o *appdev* grupo utilizando o [show de grupo do ad az] [ az-ad-group-show] comando. Este grupo está definido como o assunto de um RoleBinding no próximo passo.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Agora, crie um RoleBinding para o *appdev* grupo a utilizar a função criada anteriormente para acesso de espaço de nomes. Crie um ficheiro denominado `rolebinding-dev-namespace.yaml` e cole o manifesto YAML seguinte. Na última linha, substitua *groupObjectId* com a saída de ID de objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Crie a RoleBinding com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome de ficheiro do seu manifesto YAML de:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Criar os recursos de cluster do AKS para SREs

Agora, repita os passos anteriores para criar um espaço de nomes, função e RoleBinding para os SREs.

Primeiro, crie um espaço de nomes para *sre* utilizando o [kubectl criar espaço de nomes] [ kubectl-create] comando:

```console
kubectl create namespace sre
```

Crie um ficheiro denominado `role-sre-namespace.yaml` e cole o manifesto YAML seguinte:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Crie a função com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome de ficheiro do seu manifesto YAML de:

```console
kubectl apply -f role-sre-namespace.yaml
```

Obter o ID de recurso para o *opssre* grupo utilizando o [show de grupo do ad az] [ az-ad-group-show] comando:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Criar um RoleBinding para o *opssre* grupo a utilizar a função criada anteriormente para acesso de espaço de nomes. Crie um ficheiro denominado `rolebinding-sre-namespace.yaml` e cole o manifesto YAML seguinte. Na última linha, substitua *groupObjectId* com a saída de ID de objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
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

Crie a RoleBinding com o [aplicam-se de kubectl] [ kubectl-apply] de comando e especifique o nome de ficheiro do seu manifesto YAML de:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagir com os recursos de cluster através de identidades do Azure AD

Agora, vamos testar o trabalho de permissões esperado quando cria e gerir os recursos num cluster do AKS. Nestes exemplos, agendar e ver os pods no espaço de nomes atribuídos ao utilizador. Em seguida, tentar pods de agendamento e a vista de fora do espaço de nomes atribuído.

Em primeiro lugar, repor a *kubeconfig* usando o contexto de [az aks get-credentials] [ az-aks-get-credentials] comando. Numa secção anterior, define o contexto com as credenciais de administrador de cluster. O utilizador administrador ignora os pedidos de início de sessão do Azure AD. Sem o `--admin` parâmetro, o contexto do usuário é aplicado que requer que todos os pedidos sejam autenticados com o Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Agenda uma através de pod NGINX básico a [kubectl execute] [ kubectl-run] comando no *dev* espaço de nomes:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Como o início de sessão da linha de comandos, introduza as credenciais de seu próprio `appdev@contoso.com` conta criada no início do artigo. Assim que tiver entrado com êxito nos, o token de conta é colocado em cache para futuro `kubectl` comandos. O NGINX é agendar com êxito, conforme mostrado no seguinte exemplo:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Agora utilizar o [kubectl obter pods] [ kubectl-get] comando para ver os pods no *dev* espaço de nomes.

```console
kubectl get pods --namespace dev
```

Conforme mostrado na seguinte saída de exemplo, o pod NGINX é com êxito *em execução*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Criar e ver os recursos de cluster fora do espaço de nomes atribuído

Experimente agora ver os pods fora dos *dev* espaço de nomes. Utilize o [kubectl obter pods] [ kubectl-get] novamente, o comando neste momento para ver `--all-namespaces` da seguinte forma:

```console
kubectl get pods --all-namespaces
```

Associação a grupos do utilizador não tem uma função do Kubernetes que permite que esta ação, conforme mostrado no seguinte exemplo:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Da mesma forma, tente agendar um pod no espaço de nomes diferente, tais como o *sre* espaço de nomes. Associação a grupos do utilizador não se alinham com uma função de Kubernetes e RoleBinding para conceder estas permissões, conforme mostrado no seguinte exemplo:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testar o acesso SRE para os recursos de cluster do AKS

Para confirmar que nossos associação a grupos do Azure AD e o Kubernetes RBAC funcionem corretamente entre usuários e grupos diferentes, experimente os comandos anteriores quando a sessão iniciada como o *opssre* utilizador.

Repor o *kubeconfig* usando o contexto de [az aks get-credentials] [ az-aks-get-credentials] comando que limpe o token de autenticação anteriormente em cache para o *aksdev*  utilizador:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Tente pods de agendamento e a vista no atribuído *sre* espaço de nomes. Quando lhe for pedido, inicie sessão com a sua própria `opssre@contoso.com` credenciais criadas no início do artigo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Com êxito, conforme mostrado na seguinte saída de exemplo, pode criar e ver os pods da mesma:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Experimente agora ver ou agendar pods fora do espaço de nomes SRE atribuído:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Estes `kubectl` comandos falharem, conforme mostrado na seguinte saída de exemplo. Associação de grupo do utilizador e a função de Kubernetes e RoleBindings não conceder permissões para criar ou do Gestor de recursos em outros namespaces:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, criou recursos no cluster do AKS e os utilizadores e grupos no Azure AD. Para limpar todos estes recursos, execute os seguintes comandos:

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

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como proteger os clusters do Kubernetes, consulte [opções de acesso e identidade para o AKS)][rbac-authorization].

Para melhores práticas no controlo de identidade e de recursos, consulte [melhores práticas para autenticação e autorização no AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
