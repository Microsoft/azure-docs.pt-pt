---
title: Integrar o Azure Active Directory com o serviço Kubernetes do Azure
description: Como criar clusters do Azure Kubernetes Service (AKS) habilitados no Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: 0cf83180647c142c9db2a1229674de96fec6a6bb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087538"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory com o serviço Kubernetes do Azure

O Azure Kubernetes Service (AKS) pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Nesta configuração, pode iniciar sessão num cluster do AKS com o token de autenticação do Azure Active Directory. Além disso, os administradores de cluster são capazes de configurar o controlo de acesso baseado em função do Kubernetes (RBAC) com base numa associação de grupo de identidade ou o diretório de utilizadores.

Este artigo mostra-lhe como implementar os pré-requisitos para o AKS e o Azure AD, em seguida, como implementar um cluster do Azure AD habilitado e criar uma função RBAC simple no cluster do AKS.

As seguintes limitações aplicam-se:

- O Azure AD só pode ser ativado ao criar um cluster novo, habilitados no RBAC. Não é possível ativar o Azure AD num cluster do AKS existente.
- *Convidado* utilizadores no Azure AD, tal como se estiver a utilizar um início de sessão federado a partir de um diretório diferente, não são suportadas.

## <a name="authentication-details"></a>Detalhes de autenticação

Autenticação do Azure AD é fornecida para clusters do AKS com OpenID Connect. OpenID Connect é uma camada de identidade criada com base no protocolo de OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [abrir ID connect documentação][open-id-connect].

De dentro do cluster de Kubernetes, autenticação de Token do Webhook é usada para verificar os tokens de autenticação. Autenticação de token do Webhook é configurada e gerenciada como parte do AKS cluster. Para obter mais informações sobre a autenticação de token do Webhook, veja a [documentação de autenticação de webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicação do Azure AD estão configurados. Esta operação deve ser concluída por um administrador de inquilino do Azure.

## <a name="create-server-application"></a>Criar aplicação de servidor

A primeira aplicação do Azure AD é utilizada para obter uma associação de grupo utilizadores do Azure AD.

1. Selecione **Azure Active Directory** > **Registos das aplicações** > **Novo registo de aplicação**.

   Dê um nome, selecione ao aplicativo **aplicação Web / API** para o tipo de aplicação e introduza qualquer valor URI formatado para **URL de início de sessão**. Selecione **criar** quando tiver terminado.

   ![Criar o registo do Azure AD](media/aad-integration/app-registration.png)

2. Selecione **manifesto** e editar os `groupMembershipClaims` valor a `"All"`.

   Guarde as atualizações, depois de concluído.

   ![Atualizar associação de grupo para todos](media/aad-integration/edit-manifest.png)

3. Novamente na aplicação do Azure AD, selecione **configurações** > **chaves**.

   Adicionar uma descrição da chave, selecione um prazo de expiração e selecione **guardar**. Anote o valor da chave. Quando implementar um Azure AD ativado o cluster do AKS, este valor é referido como o `Server application secret`.

   ![Obter a chave privada da aplicação](media/aad-integration/application-key.png)

4. Regressar à aplicação do Azure AD, selecione **configurações** > **permissões obrigatórias** > **adicionar**  >   **Selecionar uma API** > **Microsoft Graph** > **selecionar**.

   ![Selecione a graph API](media/aad-integration/graph-api.png)

5. Sob **PERMISSÕES de aplicação** coloque uma marca junto a **ler dados do diretório**.

   ![Definir permissões de gráfico de aplicação](media/aad-integration/read-directory.png)

6. Sob **PERMISSÕES DELEGADAS**, coloque uma marca junto a **iniciar sessão e ler o perfil de utilizador** e **ler dados do diretório**. Guarde as atualizações assim que estiver pronto.

   ![Definir permissões de gráfico de aplicação](media/aad-integration/delegated-permissions.png)

   Selecione **Done** (Concluído).

7. Escolher *Microsoft Graph* na lista de APIs, em seguida, selecione **conceder permissões**. Este passo irá falhar se a conta atual não é um administrador inquilino.

   ![Definir permissões de gráfico de aplicação](media/aad-integration/grant-permissions.png)

   Quando as permissões foram concedidas com êxito, a seguinte notificação é apresentada no portal do:

   ![Notificação de êxito permissões concedidas](media/aad-integration/permissions-granted.png)

8. Regresse à aplicação e tome nota dos **ID da aplicação**. Ao implementar um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Server application ID`.

   ![Obter ID da aplicação](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Criar aplicação de cliente

A segunda aplicação do Azure AD é utilizada quando iniciar sessão com a CLI do Kubernetes (kubectl).

1. Selecione **Azure Active Directory** > **Registos das aplicações** > **Novo registo de aplicação**.

   Dê um nome, selecione ao aplicativo **nativo** para o tipo de aplicação e introduza qualquer valor URI formatado para **URI de redirecionamento**. Selecione **criar** quando tiver terminado.

   ![Criar o registo do AAD](media/aad-integration/app-registration-client.png)

2. A partir da aplicação do Azure AD, selecione **configurações** > **permissões obrigatórias** > **adicionar** > **selecionar uma API** e procure o nome da aplicação de servidor criada no último passo deste documento.

   ![Configurar permissões de aplicação](media/aad-integration/select-api.png)

3. Coloque uma marca de verificação ao lado da aplicação e clique em **selecione**.

   ![Selecione o ponto final de aplicação de servidor do AAD do AKS](media/aad-integration/select-server-app.png)

   Selecione **concluído**

4. Selecione o seu servidor de API da lista e, em seguida, escolha **conceder permissões**:

   ![Conceder permissões](media/aad-integration/grant-permissions-client.png)

5. Novamente na aplicação do AD, anote o **ID da aplicação**. Ao implementar um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Client application ID`.

   ![Obter o ID da aplicação](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obter o ID de inquilino

Por fim, obtenha o ID do seu inquilino do Azure. Este valor também é utilizado quando implementar o cluster do AKS.

A partir do portal do Azure, selecione **do Azure Active Directory** > **propriedades** e tome nota do **ID do diretório**. Ao implementar um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Tenant ID`.

![Obter o ID de inquilino do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Implementar Cluster

Utilize o [criar grupo az] [ az-group-create] comando para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementar o cluster com o [criar az aks] [ az-aks-create] comando. Substitua os valores no comando de exemplo abaixo com os valores recolhidos durante a criação de aplicações do Azure AD.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Criar o enlace de RBAC

Antes de uma conta do Azure Active Directory pode ser utilizada com o cluster do AKS, um enlace de função de cluster ou enlace de função tem de ser criado. *Funções* definir as permissões para conceder, e *enlaces* aplicá-las para os usuários desejados. Estas atribuições podem ser aplicadas a um determinado espaço de nomes ou em todo o cluster. Para obter mais informações, consulte [autorização RBAC usando][rbac-authorization].

Em primeiro lugar, utilize o [az aks get-credentials] [ az-aks-get-credentials] comando com o `--admin` argumento para iniciar sessão para o cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, utilize o seguinte manifesto para criar um ClusterRoleBinding para uma conta do Azure AD. Neste exemplo dá à conta de acesso completo a todos os espaços de nomes do cluster. Crie um ficheiro, tal como *rbac-aad-user.yaml*e cole o seguinte conteúdo. Atualize o nome de utilizador com uma do inquilino do Azure AD:

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
  name: "user@contoso.com"
```

Aplicam-se de que a ligação utilizando o [aplicam-se de kubectl] [ kubectl-apply] comando conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-user.yaml
```

Também é possível criar um enlace de função para todos os membros de um grupo do Azure AD. Grupos do Azure AD são especificados com o ID de objeto de grupo, conforme mostrado no exemplo a seguir. Crie um ficheiro, tal como *rbac-aad-group.yaml*e cole o seguinte conteúdo. Atualize o ID de objeto de grupo com uma do inquilino do Azure AD:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Aplicam-se de que a ligação utilizando o [aplicam-se de kubectl] [ kubectl-apply] comando conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre como proteger um cluster de Kubernetes com o RBAC, veja [utilizando o RBAC autorização][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Cluster de acesso com o Azure AD

Em seguida, extrair o contexto para o utilizador não administrador a utilizar o [az aks get-credentials] [ az-aks-get-credentials] comando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de executar qualquer comando kubectl, será solicitado para autenticar com o Azure. Siga na tela instruções.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

Depois de concluído, o token de autenticação é colocado em cache. Apenas são reprompted para iniciar sessão quando o token expirou ou o ficheiro de configuração Kubernetes recriado.

Se vir uma mensagem de erro de autorização depois de iniciar sessão com êxito, verifique se:
1. O utilizador estiver a iniciar sessão como é não convidado na instância do Azure AD (isto é, muitas vezes, o caso se estiver a utilizar um início de sessão federado a partir de um diretório diferente).
2. O utilizador não é um membro de mais de 200 grupos.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre como proteger clusters de Kubernetes com o RBAC com o [utilizando o RBAC autorização] [ rbac-authorization] documentação.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[rbac-authorization]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
