---
title: Integrar Azure Active Directory com o serviço kubernetes do Azure
description: Como criar clusters do AKS (serviço kubernetes do Azure) habilitados para Azure Active Directory
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 5dabbb6458d0d0d4af51490bea0c3f38a7c5c41d
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542904"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar Azure Active Directory com o serviço kubernetes do Azure

O AKS (serviço kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD do Azure) para autenticação de usuário. Nessa configuração, você pode entrar em um cluster AKS usando o token de autenticação do Azure AD.

Os administradores de cluster podem configurar o RBAC (controle de acesso baseado em função) kubernetes com base na identidade de um usuário ou no grupo de diretórios.

Este artigo explica como:

- Implante os pré-requisitos para o AKS e o Azure AD.
- Implante um cluster habilitado para o Azure AD.
- Crie uma função de RBAC básica no cluster AKS usando o portal do Azure.

Você também pode concluir essas etapas usando o [CLI do Azure][azure-ad-cli].

> [!NOTE]
> O Azure AD só pode ser habilitado quando você cria um novo cluster habilitado para RBAC. Não é possível habilitar o Azure AD em um cluster AKS existente.

## <a name="authentication-details"></a>Detalhes da autenticação

A autenticação do Azure AD é fornecida para clusters AKS que têm o OpenID Connect. O OpenID Connect é uma camada de identidade criada sobre o protocolo OAuth 2,0.

Para obter mais informações sobre o OpenID Connect, consulte autorizar o [acesso a aplicativos Web usando o OpenID Connect e o Azure ad][open-id-connect].

Dentro de um cluster kubernetes, a autenticação de token de webhook é usada para tokens de autenticação. A autenticação de token de webhook é configurada e gerenciada como parte do cluster AKS.

Para obter mais informações sobre a autenticação de token de webhook, consulte a seção [autenticação de token][kubernetes-webhook] de webhook na documentação do kubernetes.

Para fornecer autenticação do Azure AD para um cluster AKS, dois aplicativos do Azure AD são criados. O primeiro aplicativo é um componente de servidor que fornece autenticação de usuário. O segundo aplicativo é um componente de cliente que é usado quando você é solicitado pela CLI para autenticação. Esse aplicativo cliente usa o aplicativo de servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Quando você configura o Azure AD para autenticação AKS, dois aplicativos do Azure AD são configurados. As etapas para delegar permissões para cada aplicativo devem ser concluídas por um administrador de locatários do Azure.

## <a name="create-the-server-application"></a>Criar o aplicativo de servidor

O primeiro aplicativo do Azure AD é aplicado para obter a associação de grupo do Azure AD de um usuário. Para criar esse aplicativo no portal do Azure:

1. Selecione **Azure Active Directory** > registros de aplicativonovo > **registro**.

    a. Dê um nome ao aplicativo, como *AKSAzureADServer*.

    b. Para **tipos de conta com suporte**, selecione **contas somente neste diretório organizacional**.
    
    c. Escolha **Web** para o tipo de URI de redirecionamento e, em seguida, insira qualquer valor *https://aksazureadserver* formatado por URI, como.

    d. Selecione **registrar** quando tiver terminado.

2. Selecione **manifesto**e edite o **groupMembershipClaims:** valor como **tudo**. Quando tiver concluído as atualizações, selecione **salvar**.

    ![Atualizar Associação de grupo para todos](media/aad-integration/edit-manifest.png)

3. No painel esquerdo do aplicativo Azure AD, selecione **certificados & segredos**.

    a. Selecione **+ novo segredo do cliente**.

    b. Adicione uma descrição de chave, como *AKs servidor do Azure ad*. Escolha uma hora de expiração e, em seguida, selecione **Adicionar**.

    c. Observe o valor da chave, que é exibido somente no momento. Quando você implanta um cluster AKS habilitado para Azure AD, esse valor é chamado de segredo do aplicativo do servidor.

4. No painel esquerdo do aplicativo Azure AD, selecione permissões de **API**e, em seguida, selecione **+ Adicionar uma permissão**.

    a. Em **APIs da Microsoft**, selecione **Microsoft Graph**.

    b. Selecione **permissões delegadas**e, em seguida, marque a caixa de seleção ao lado de **diretório > Directory. Read. All (ler dados do diretório)** .

    c. Se uma permissão delegada padrão para usuário **> usuário. ler (entrar e ler perfil de usuário)** não existir, marque a caixa de seleção ao lado dela.

    d. Selecione **permissões de aplicativo**e, em seguida, marque a caixa de seleção ao lado de **diretório > Directory. Read. All (ler dados do diretório)** .

    ![Definir permissões de gráfico](media/aad-integration/graph-permissions.png)

    e. Selecione **adicionar permissões** para salvar as atualizações.

    f. Em **conceder consentimento**, selecione **conceder consentimento do administrador**. Esse botão não estará disponível se a conta atual não for um administrador de locatário.

    Quando as permissões são concedidas com êxito, a notificação a seguir é exibida no Portal:

   ![Notificação de permissões bem-sucedidas concedidas](media/aad-integration/permissions-granted.png)

5. No painel esquerdo do aplicativo Azure AD, selecione **expor uma API**e, em seguida, selecione **+ Adicionar um escopo**.
    
    a. Insira um **nome de escopo**, um **nome de exibição de consentimento de administrador**e uma descrição de consentimento de **administrador** , como *AKSAzureADServer*.

    b. Verifique se **estado** está definido como **habilitado**.

    ![Expor o aplicativo de servidor como uma API para uso com outros serviços](media/aad-integration/expose-api.png)

    c. Selecione **Adicionar escopo**.

6. Retorne à página **visão geral** do aplicativo e anote a **ID do aplicativo (cliente)** . Quando você implanta um cluster AKS habilitado para Azure AD, esse valor é chamado de ID de aplicativo de servidor.

    ![Obter ID do aplicativo](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Criar o aplicativo cliente

O segundo aplicativo do Azure AD é usado quando você entra com a CLI do kubernetes (kubectl).

1. Selecione **Azure Active Directory** > registros de aplicativonovo > **registro**.

    a. Dê um nome ao aplicativo, como *AKSAzureADClient*.

    b. Para **tipos de conta com suporte**, selecione **contas somente neste diretório organizacional**.

    c. Selecione **Web** para o tipo de URI de redirecionamento e, em seguida, insira qualquer *https://aksazureadclient* valor formatado por URI, como.

    d. Selecione **registrar** quando tiver terminado.

2. No painel esquerdo do aplicativo Azure AD, selecione permissões de **API**e, em seguida, selecione **+ Adicionar uma permissão**.

    a. Selecione **minhas APIs**e, em seguida, escolha o aplicativo de servidor do Azure ad criado na etapa anterior, como *AKSAzureADServer*.

    b. Selecione **permissões delegadas**e, em seguida, marque a caixa de seleção ao lado de seu aplicativo de servidor do Azure AD.

    ![Configurar permissões de aplicativo](media/aad-integration/select-api.png)

    c. Selecione **adicionar permissões**.

    d. Em **conceder consentimento**, selecione **conceder consentimento do administrador**. Esse botão não estará disponível se a conta atual não for um administrador de locatário. Quando as permissões são concedidas, a notificação a seguir é exibida no Portal:

    ![Notificação de permissões bem-sucedidas concedidas](media/aad-integration/permissions-granted.png)

3. No painel esquerdo do aplicativo Azure AD, selecione **autenticação**.

    - Em **tipo de cliente padrão**, selecione **Sim** para **tratar o cliente como um cliente público**.

5. No painel esquerdo do aplicativo Azure AD, observe a ID do aplicativo. Quando você implanta um cluster AKS habilitado para Azure AD, esse valor é chamado de ID do aplicativo cliente.

   ![Obter a ID do aplicativo](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Obter a ID do locatário

Em seguida, obtenha a ID do seu locatário do Azure. Esse valor é usado quando você cria o cluster AKS.

No portal do Azure, selecione **Azure Active Directory** > **Propriedades** e anote a **ID do diretório**. Quando você cria um cluster AKS habilitado para Azure AD, esse valor é chamado de ID de locatário.

![Obter a ID de locatário do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Implantar o cluster AKS

Use o comando [AZ Group Create][az-group-create] para criar um grupo de recursos para o cluster AKs.

```azurecli
az group create --name myResourceGroup --location eastus
```

Use o comando [AZ AKs Create][az-aks-create] para implantar o cluster AKs. Em seguida, substitua os valores no comando de exemplo a seguir. Use os valores coletados quando você criou os aplicativos do Azure AD para a ID do aplicativo do servidor, o segredo do aplicativo, a ID do aplicativo cliente e a ID do locatário.

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

Um cluster AKS leva alguns minutos para ser criado.

## <a name="create-an-rbac-binding"></a>Criar uma associação RBAC

> [!NOTE]
> O nome da Associação de função de cluster diferencia maiúsculas de minúsculas.

Antes de usar uma conta de Azure Active Directory com um cluster AKS, você deve criar a associação de função ou a associação de função de cluster. As funções definem as permissões a serem concedidas e as associações as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [usando a autorização do RBAC][rbac-authorization].

Primeiro, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] com `--admin` o argumento para entrar no cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie ClusterRoleBinding para uma conta do Azure AD que você deseja conceder acesso ao cluster AKS. O exemplo a seguir dá à conta acesso completo a todos os namespaces no cluster:

- Se o usuário para o qual você concede a associação de RBAC estiver no mesmo locatário do Azure AD, atribua permissões com base no nome principal do usuário (UPN). Vá para a etapa para criar o manifesto YAML para ClusterRoleBinding.

- Se o usuário estiver em um locatário do Azure AD diferente, consulte e use a propriedade **ObjectID** em seu lugar. Se necessário, obtenha o objectId da conta de usuário necessária usando o comando [AZ ad User show][az-ad-user-show] . Forneça o nome principal do usuário (UPN) da conta necessária:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um arquivo, como *RBAC-AAD-User. YAML*e cole o conteúdo a seguir. Na última linha, substitua **userPrincipalName_or_objectId** com a ID de objeto ou UPN. A escolha depende se o usuário é o mesmo locatário do Azure AD ou não.

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

Aplique a Associação usando o comando [kubectl Apply][kubectl-apply] , conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-user.yaml
```

Uma associação de função também pode ser criada para todos os membros de um grupo do Azure AD. Os grupos do Azure AD são especificados usando a ID de objeto de grupo, conforme mostrado no exemplo a seguir.

Crie um arquivo, como *RBAC-AAD-Group. YAML*e cole o conteúdo a seguir. Atualize a ID de objeto de grupo com uma do seu locatário do Azure AD:

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

Aplique a Associação usando o comando [kubectl Apply][kubectl-apply] , conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre como proteger um cluster kubernetes com o RBAC, consulte [usando a autorização do RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Acessar o cluster com o Azure AD

Receba o contexto do usuário não administrador usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] .

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de executar o `kubectl` comando, você será solicitado a autenticar usando o Azure. Siga as instruções na tela para concluir o processo, conforme mostrado no exemplo a seguir:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando o processo for concluído, o token de autenticação será armazenado em cache. Você será solicitado a entrar novamente quando o token expirar ou o arquivo de configuração kubernetes for recriado.

Se você vir uma mensagem de erro de autorização depois de entrar com êxito, verifique os seguintes critérios:

```console
error: You must be logged in to the server (Unauthorized)
```


- Você definiu a ID de objeto ou o UPN apropriado, dependendo de se a conta de usuário está no mesmo locatário do Azure AD ou não.
- O usuário não é membro de mais de 200 grupos.
- O segredo definido no registro do aplicativo para o servidor corresponde ao valor configurado `--aad-server-app-secret`usando.

## <a name="next-steps"></a>Passos seguintes

Para usar os usuários e grupos do Azure AD para controlar o acesso aos recursos de cluster, consulte [controlar o acesso a recursos de cluster usando o controle de acesso baseado em função e identidades do Azure AD no AKs][azure-ad-rbac].

Para obter mais informações sobre como proteger clusters do kubernetes, consulte [Opções de acesso e identidade para AKs][rbac-authorization].

Para saber mais sobre identidade e controle de recursos, consulte [práticas recomendadas para autenticação e autorização no AKs][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
