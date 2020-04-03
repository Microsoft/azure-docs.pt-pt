---
title: Integrar o Azure Active Directory com o Azure Kubernetes Service
description: Como criar clusters azure Ative Directory Service (AKS) habilitados para o Azure Ative Directory
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617587"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory com o Azure Kubernetes Service

O Serviço Azure Kubernetes (AKS) pode ser configurado para utilizar o Azure Ative Directory (Azure AD) para autenticação do utilizador. Nesta configuração, pode iniciar sessão num cluster AKS utilizando o seu símbolo de autenticação Azure AD.

Os administradores de cluster podem configurar o controlo de acesso baseado em funções da Kubernetes (RBAC) com base na identidade de um utilizador ou na adesão de um grupo de diretórios.

Este artigo explica como:

- Implemente os pré-requisitos para AKS e Azure AD.
- Implementar um cluster ativado por AD Azure.
- Crie uma função rBAC básica no cluster AKS utilizando o portal Azure.

Também pode completar estes passos utilizando o [Azure CLI][azure-ad-cli].

> [!NOTE]
> O Azure AD só pode ser ativado quando criar um novo cluster ativado por RBAC. Não se pode ativar o Azure AD num aglomerado AKS existente.

## <a name="authentication-details"></a>Detalhes da autenticação

A autenticação Azure AD é fornecida aos clusters AKS que têm OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0.

Para mais informações sobre o OpenID Connect, consulte [Autorizar o acesso a aplicações web utilizando o OpenID Connect e o Azure AD][open-id-connect].

Dentro de um cluster Kubernetes, a autenticação de token webhook é usada para autenticar tokens. A autenticação token webhook é configurada e gerida como parte do cluster AKS.

Para obter mais informações sobre a autenticação do webhook token, consulte a secção de [autenticação token webhook][kubernetes-webhook] na Documentação Kubernetes.

Para fornecer autenticação Azure AD para um cluster AKS, são criadas duas aplicações Azure AD. A primeira aplicação é um componente do servidor que fornece a autenticação do utilizador. A segunda aplicação é um componente cliente que é usado quando é solicitado pelo CLI para autenticação. Esta aplicação de cliente utiliza a aplicação do servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Quando configura ruma a AD Azure para autenticação AKS, duas aplicações Azure AD são configuradas. As medidas para delegar permissões para cada pedido devem ser preenchidas por um administrador de inquilino sinuoso azure.

## <a name="create-the-server-application"></a>Criar a aplicação do servidor

A primeira aplicação Azure AD é aplicada para obter a adesão de um utilizador ao grupo Azure AD. Para criar esta aplicação no portal Azure:

1. Selecione Registos de**Aplicações** >  **de Diretório** > Ativo Azure**Novas inscrições**.

    a. Dê à aplicação um nome, como *AKSAzureADServer*.

    b. Para **tipos de conta suportados,** selecione Contas **apenas neste diretório organizacional**.
    
    c. Escolha **Web** web para o tipo URI redirecionamento e, *https://aksazureadserver*em seguida, introduza qualquer valor formato URI, como .

    d. Selecione **Registar-se** quando terminar.

2. Selecione **Manifesto**, e depois editar o **grupoMembershipClaims:** valor como **Todos**. Quando terminar as atualizações, selecione **Guardar**.

    ![Atualizar a adesão ao grupo para todos](media/aad-integration/edit-manifest.png)

3. No painel esquerdo da aplicação Azure AD, selecione **Certificados & segredos.**

    a. Selecione **+ Novo segredo de cliente.**

    b. Adicione uma descrição chave, como *o servidor AKS Azure AD*. Escolha um tempo de validade e, em seguida, selecione **Adicionar**.

    c. Note o valor-chave, que é apresentado apenas neste momento. Quando implementa um cluster AKS ativado por AD, este valor chama-se segredo de aplicação do servidor.

4. No painel esquerdo da aplicação Azure AD, selecione **permissões API,** e, em seguida, selecione **+ Adicione uma permissão**.

    a. Em **apis da Microsoft,** selecione **Microsoft Graph**.

    b. Selecione **permissões delegadas**, e, em seguida, selecione a caixa de verificação ao lado do **Diretório > Diretório.Leia.Todos (Leia os dados do diretório)**.

    c. Se não existir uma permissão dedelegado predefinida para **o Utilizador > Utilizador.Leia (Faça o check-in e leia** o perfil do utilizador) não existe, selecione a caixa de verificação ao lado.

    d. Selecione **permissões de aplicação**, e, em seguida, selecione a caixa de verificação ao lado do **Diretório > Diretório.Leia.Todos (Leia os dados do diretório)**.

    ![Definir permissões de gráfico](media/aad-integration/graph-permissions.png)

    e. Selecione **Adicionar permissões** para guardar as atualizações.

    f. Sob o consentimento do **Grant,** selecione **Grant admin consent**. Este botão não estará disponível a conta corrente que está a ser utilizada não está listada como administradora de inquilinos.

    Quando as permissões são concedidas com sucesso, a seguinte notificação é apresentada no portal:

   ![Notificação de permissões bem sucedidas concedidas](media/aad-integration/permissions-granted.png)

5. No painel esquerdo da aplicação Azure AD, selecione **Expor uma API,** e, em seguida, selecione **+ Adicione um âmbito**.
    
    a. Introduza um **nome de âmbito,** um nome de **exibição**de consentimento do Administrador e, em seguida, uma descrição de **consentimento do Administrador,** como *AKSAzureADServer*.

    b. Certifique-se de que o **Estado** está definido para **ativado**.

    ![Expor a aplicação do servidor como uma API para uso com outros serviços](media/aad-integration/expose-api.png)

    c. **Selecione Adicionar âmbito**.

6. Volte à página de **visão geral** da aplicação e note o ID da **Aplicação (cliente).** Quando implementa um cluster AKS ativado por AD, este valor chama-se ID de aplicação do servidor.

    ![Obter ID de aplicação](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Criar a aplicação do cliente

A segunda aplicação Azure AD é usada quando se inscreveu no Kubernetes CLI (kubectl).

1. Selecione Registos de**Aplicações** >  **de Diretório** > Ativo Azure**Novas inscrições**.

    a. Dê à aplicação um nome, como *AKSAzureADClient*.

    b. Para **tipos de conta suportados,** selecione Contas **apenas neste diretório organizacional**.

    c. Selecione **Web** para o tipo URI redirecionamento *https://aksazureadclient*e, em seguida, introduza qualquer valor formato URI, tais como .

    >[!NOTE]
    >Se estiver a criar um novo cluster ativado por RBAC para suportar o Monitor Azure para contentores, adicione os seguintes dois URLs adicionais de redirecionamento a esta lista como tipos de aplicações **Web.** O valor url da `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` primeira base deve ser e o segundo valor URL base deve ser`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Se estiver a usar esta funcionalidade na Azure China, `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` o valor URL da primeira base deve ser e o valor url da segunda base deve ser`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Para mais informações, consulte [como configurar a funcionalidade Live Data (pré-visualização)](../azure-monitor/insights/container-insights-livedata-setup.md) para o Monitor Azure para contentores e as etapas para configurar a autenticação no âmbito da secção de [autenticação integrada da Configuração AD.](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)

    d. Selecione **Registar-se** quando terminar.

2. No painel esquerdo da aplicação Azure AD, selecione **permissões API,** e, em seguida, selecione **+ Adicione uma permissão**.

    a. Selecione **As Minhas APIs**e, em seguida, escolha a sua aplicação de servidor AD Azure criada na etapa anterior, como *AKSAzureADServer*.

    b. Selecione **permissões delegadas**e, em seguida, selecione a caixa de verificação ao lado da sua aplicação de servidor AD Azure.

    ![Configurar permissões de candidatura](media/aad-integration/select-api.png)

    c. **Selecione Adicionar permissões**.

    d. Sob o consentimento do **Grant,** selecione **Grant admin consent**. Este botão não está disponível se a conta corrente não for administradora de inquilinos. Quando as permissões são concedidas, a seguinte notificação é apresentada no portal:

    ![Notificação de permissões bem sucedidas concedidas](media/aad-integration/permissions-granted.png)

3. No painel esquerdo da aplicação Azure AD, **selecione Autenticação**.

    - Sob o tipo de **cliente Predefinido,** selecione **Sim** para Tratar o cliente como **cliente público**.

5. No painel esquerdo da aplicação Azure AD, note o ID da aplicação. Quando implementa um cluster AKS ativado por AD, este valor chama-se ID de aplicação de cliente.

   ![Obtenha o ID da aplicação](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Obter a identificação do inquilino

Em seguida, pegue a identificação do seu inquilino Azure. Este valor é usado quando se cria o cluster AKS.

A partir do portal Azure, selecione **Azure Ative Directory** > **Properties** e note o ID do **Diretório**. Quando você cria um cluster AKS ativado por AD Azure, este valor é chamado de ID do inquilino.

![Obtenha a identificação do inquilino Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Implementar o cluster AKS

Use o [grupo AZ criar][az-group-create] comando para criar um grupo de recursos para o cluster AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Use as [aks az criar][az-aks-create] comando para implantar o cluster AKS. Em seguida, substitua os valores no comando da amostra seguinte. Utilize os valores recolhidos quando criou as aplicações Azure AD para o ID da aplicação do servidor, segredo de aplicação, ID da aplicação do cliente e ID do inquilino.

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

Um aglomerado AKS leva alguns minutos para criar.

## <a name="create-an-rbac-binding"></a>Criar uma ligação RBAC

> [!NOTE]
> O nome de ligação do cluster é sensível ao caso.

Antes de utilizar uma conta de Diretório Ativo Azure com um cluster AKS, deve criar uma ligação de funções ou de ligação ao cluster. As funções definem as permissões de concessão e as encadernações aplicam-nas aos utilizadores pretendidos. Estas atribuições podem ser aplicadas a um determinado espaço de nome, ou em todo o cluster. Para mais informações, consulte [a utilização da autorização RBAC][rbac-authorization].

Primeiro, use o comando [az aks get-credentials][az-aks-get-credentials] com o `--admin` argumento de assinar no cluster com acesso administrativo.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie clusterRoleBinding para uma conta Azure AD que pretende conceder acesso ao cluster AKS. O exemplo seguinte dá à conta acesso total a todos os espaços de nome no cluster:

- Se o utilizador que conceder a ligação RBAC estiver no mesmo inquilino Azure AD, atribua permissões com base no nome principal do utilizador (UPN). Passe para o passo para criar o manifesto YAML para ClusterRoleBinding.

- Se o utilizador estiver num inquilino Azure AD diferente, questione e utilize a propriedade **objectId.** Se necessário, obtenha o objectId da conta de utilizador necessária utilizando o comando de [show de utilizadores az az.][az-ad-user-show] Fornecer o nome principal do utilizador (UPN) da conta requerida:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um ficheiro, como *rbac-aad-user.yaml,* e, em seguida, colá-se os seguintes conteúdos. Na última linha, substitua **userPrincipalName_or_objectId** pelo UPN ou identificação do objeto. A escolha depende se o utilizador é ou não o mesmo inquilino azure AD.

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

Aplicar a ligação utilizando o comando de [aplicação kubectl,][kubectl-apply] tal como mostrado no seguinte exemplo:

```console
kubectl apply -f rbac-aad-user.yaml
```

Um papel vinculativo também pode ser criado para todos os membros de um grupo Azure AD. Os grupos AD Azure são especificados utilizando o ID do objeto de grupo, como mostra o seguinte exemplo.

Crie um ficheiro, como *o rbac-aad-group.yaml,* e, em seguida, colá-se os seguintes conteúdos. Atualize o ID do objeto de grupo com um do seu inquilino Azure AD:

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

Aplicar a ligação utilizando o comando de [aplicação kubectl,][kubectl-apply] tal como mostrado no seguinte exemplo:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre a segurança de um cluster Kubernetes com RBAC, consulte A Utilização da [Autorização RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Aceda ao cluster com AD Azure

Puxe o contexto para o utilizador não administrador utilizando o comando [az aks get-credentials.][az-aks-get-credentials]

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de `kubectl` executar o comando, será solicitado a autenticar usando Azure. Siga as instruções no ecrã para terminar o processo, como mostra o seguinte exemplo:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando o processo estiver concluído, o símbolo de autenticação é emcached. Só lhe é pedido que assine de novo quando o símbolo expirar, ou o ficheiro config Kubernetes seja recriado.

Se vir uma mensagem de erro de autorização depois de iniciar sessão com sucesso, verifique os seguintes critérios:

```console
error: You must be logged in to the server (Unauthorized)
```


- Definiu o ID ou UPN de objeto apropriado, dependendo se a conta de utilizador está ou não no mesmo inquilino Azure AD.
- O utilizador não é membro de mais de 200 grupos.
- O segredo definido no registo de aplicação para `--aad-server-app-secret`servidor corresponde ao valor configurado pela utilização .

## <a name="next-steps"></a>Passos seguintes

Para utilizar utilizadores e grupos da AD Azure para controlar o acesso aos recursos de cluster, consulte o controlo do acesso aos recursos de cluster utilizando o [controlo de acesso baseado em papéis e identidades azure ad em AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters kubernetes, consulte opções de [acesso e identidade para AKS][rbac-authorization].

Para saber mais sobre identidade e controlo de recursos, consulte [as melhores práticas de autenticação e autorização no AKS.][operator-best-practices-identity]

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
