---
title: Integrar o Azure Active Directory com o serviço Kubernetes do Azure
description: Como criar clusters do Azure Kubernetes Service (AKS) habilitados no Azure Active Directory
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616394"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory com o serviço Kubernetes do Azure

O Azure Kubernetes Service (AKS) pode ser configurado para utilizar o Azure Active Directory (Azure AD) para autenticação de utilizador. Nesta configuração, pode iniciar sessão para um cluster do AKS utilizando o token de autenticação do Azure AD.

Administradores de cluster podem configurar o controlo de acesso baseado em função do Kubernetes (RBAC) com base na associação de grupo de identidade ou o diretório de um utilizador.

Este artigo explica como:

- Implemente os pré-requisitos para o AKS e o Azure AD.
- Implemente um cluster do Azure AD-ativado.
- Crie uma função RBAC básica do cluster do AKS com o portal do Azure.

Também pode concluir estes passos, utilizando o [CLI do Azure][azure-ad-cli].

> [!NOTE]
> O Azure AD só pode ser ativado ao criar um novo cluster habilitados no RBAC. Não é possível ativar o Azure AD num cluster do AKS existente.

## <a name="authentication-details"></a>Detalhes de autenticação

Autenticação do Azure AD é fornecida para clusters do AKS com OpenID Connect. OpenID Connect é uma camada de identidade criada com base no protocolo de OAuth 2.0.

Para obter mais informações sobre o OpenID Connect, consulte [autorizar o acesso a aplicações web com OpenID Connect e o Azure AD][open-id-connect].

Dentro de um cluster do Kubernetes, autenticação de token do webhook é utilizado para tokens de autenticação. Autenticação de token do Webhook é configurada e gerenciada como parte do AKS cluster.

Para obter mais informações sobre a autenticação de token do webhook, veja a [autenticação de Token do Webhook][kubernetes-webhook] seção na documentação do Kubernetes.

Para fornecer autenticação do Azure AD para um cluster do AKS, são criadas duas aplicações do Azure AD. A primeira aplicação é um componente de servidor que oferece autenticação de utilizador. O segundo aplicativo é um componente de cliente que é utilizado quando lhe for pedido, a CLI para a autenticação. Esta aplicação de cliente utiliza a aplicação de servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Quando configurar o Azure AD para autenticação do AKS, são configuradas duas aplicações do Azure AD. Os passos para delegar permissões para cada aplicativo devem ser concluídos por um administrador de inquilino do Azure.

## <a name="create-the-server-application"></a>Criar a aplicação de servidor

A primeira aplicação do Azure AD é aplicada para obter a associação de grupo do Azure AD de um utilizador. Para criar esta aplicação no portal do Azure:

1. Selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo**.

    a. Dê um nome, a aplicação, tal como *AKSAzureADServer*.

    b. Para **tipos de conta suportados**, selecione **contas neste diretório organizacional apenas**.
    
    c. Escolher **Web** para o URI de redirecionamento escreva e, em seguida, introduza qualquer valor de formatação URI, tal como *https://aksazureadserver* .

    d. Selecione **registar** quando tiver terminado.

2. Selecione **manifesto**e, em seguida, edite a **groupMembershipClaims:** valor como **todos os**. Quando tiver terminado com as atualizações, selecione **guardar**.

    ![Atualizar associação de grupo para todos](media/aad-integration/edit-manifest.png)

3. No painel esquerdo da aplicação do Azure AD, selecione **certificados e segredos**.

    a. Selecione **+ novo segredo do cliente**.

    b. Adicionar uma descrição da chave, como *servidor do AKS do Azure AD*. Escolha um prazo de expiração e, em seguida, selecione **adicionar**.

    c. Tenha em atenção o valor da chave, que é exibido apenas neste momento. Quando implementa um cluster do AKS habilitados no AD do Azure, este valor é chamado o segredo de aplicação de servidor.

4. No painel esquerdo da aplicação do Azure AD, selecione **permissões de API**e, em seguida, selecione **+ adicionar uma permissão**.

    a. Sob **APIs da Microsoft**, selecione **Microsoft Graph**.

    b. Selecione **permissões delegadas**e, em seguida, selecione a caixa de verificação junto a **Directory > Directory.Read.All (dados do diretório de leitura)** .

    c. Se um padrão delegado permissão para **utilizador > User.Read (iniciar sessão e ler o perfil de utilizador)** não existir, selecione a caixa de verificação junto ao mesmo.

    d. Selecione **permissões de aplicação**e, em seguida, selecione a caixa de verificação junto a **Directory > Directory.Read.All (dados do diretório de leitura)** .

    ![Definir permissões de gráfico](media/aad-integration/graph-permissions.png)

    e. Selecione **adicionar permissões** para guardar as atualizações.

    f. Sob **conceder autorização**, selecione **conceder autorização de administrador**. Este botão não está disponível se a conta atual não é um administrador inquilino.

    Quando as permissões são concedidas com êxito, a seguinte notificação é apresentada no portal do:

   ![Notificação de êxito permissões concedidas](media/aad-integration/permissions-granted.png)

5. No painel esquerdo da aplicação do Azure AD, selecione **expor uma API**e, em seguida, selecione **+ adicionar um âmbito**.
    
    a. Introduza um **nome do âmbito**, uma **nome de exibição de consentimento de administrador**e, em seguida, uma **descrição de consentimento do Admin** como *AKSAzureADServer*.

    b. Certifique-se **estado** está definida como **ativado**.

    ![Expor a aplicação de servidor como uma API para utilização com outros serviços](media/aad-integration/expose-api.png)

    c. Selecione **Adicionar âmbito**.

6. Regresse à aplicação **descrição geral** página bem como observar a **ID de aplicação (cliente)** . Quando implementa um cluster do AKS habilitados no AD do Azure, este valor é chamado de ID de aplicação de servidor.

    ![Obter ID da aplicação](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Criar a aplicação de cliente

A segunda aplicação do Azure AD é utilizada quando inicia sessão com a CLI do Kubernetes (kubectl).

1. Selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo**.

    a. Dê um nome, a aplicação, tal como *AKSAzureADClient*.

    b. Para **tipos de conta suportados**, selecione **contas neste diretório organizacional apenas**.

    c. Selecione **Web** para o URI de redirecionamento escreva e, em seguida, introduza qualquer valor URI formatado como *https://aksazureadclient* .

    d. Selecione **registar** quando tiver terminado.

2. No painel esquerdo da aplicação do Azure AD, selecione **permissões de API**e, em seguida, selecione **+ adicionar uma permissão**.

    a. Selecione **as minhas APIs**e, em seguida, escolha a aplicação de servidor do Azure AD criada no passo anterior, tais como *AKSAzureADServer*.

    b. Selecione **permissões delegadas**e, em seguida, selecione a caixa de verificação junto a sua aplicação de servidor do Azure AD.

    ![Configurar permissões de aplicação](media/aad-integration/select-api.png)

    c. Selecione **adicionar permissões**.

    d. Sob **conceder autorização**, selecione **conceder autorização de administrador**. Este botão não está disponível se a conta atual não é um administrador inquilino. Quando as permissões são concedidas, a seguinte notificação é apresentada no portal do:

    ![Notificação de êxito permissões concedidas](media/aad-integration/permissions-granted.png)

3. No painel esquerdo da aplicação do Azure AD, selecione **autenticação**.

    - Sob **predefinido a tipo de cliente**, selecione **Sim** para **tratar o cliente como um cliente público**.

5. No painel esquerdo da aplicação do Azure AD, tenha em atenção o ID da aplicação. Quando implementa um cluster do AKS habilitados no AD do Azure, este valor é chamado de ID de aplicação de cliente.

   ![Obter o ID da aplicação](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Obter o ID de inquilino

Em seguida, obtenha o ID do seu inquilino do Azure. Este valor é utilizado ao criar o cluster do AKS.

A partir do portal do Azure, selecione **do Azure Active Directory** > **propriedades** e tenha em atenção o **ID do diretório**. Quando cria um cluster do AKS habilitados no AD do Azure, este valor é chamado de ID do inquilino.

![Obter o ID de inquilino do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Implementar o cluster do AKS

Utilize o [criar grupo az][az-group-create] comando para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Utilize o [az aks criar][az-aks-create] comando para implementar o cluster do AKS. Em seguida, substitua os valores no comando de exemplo seguinte. Utilize os valores recolhidos aquando da criação de aplicações do Azure AD para o ID da aplicação de servidor, segredo da aplicação, ID de aplicação de cliente e ID do inquilino.

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

Um cluster do AKS demora alguns minutos para criar.

## <a name="create-an-rbac-binding"></a>Crie um enlace de RBAC

Antes de utilizar uma conta do Azure Active Directory com um cluster do AKS, tem de criar o enlace de função ou enlace de função de cluster. Funções definem as permissões a conceder e enlaces aplicá-las para os usuários desejados. Estas atribuições podem ser aplicadas a um determinado espaço de nomes ou em todo o cluster. Para obter mais informações, consulte [autorização RBAC usando][rbac-authorization].

Em primeiro lugar, utilize o [az aks get-credentials][az-aks-get-credentials] comando com o `--admin` argumento para iniciar sessão para o cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie ClusterRoleBinding para uma conta do Azure AD que pretende conceder acesso para o cluster do AKS. O exemplo a seguir dá à conta de acesso completo a todos os espaços de nomes do cluster:

- Se o utilizador conceder que o enlace do RBAC para está no mesmo inquilino do Azure AD, atribua permissões com base no nome principal de utilizador (UPN). Avançar para o passo para criar o manifesto YAML ClusterRoleBinding.

- Se o utilizador estiver no Azure AD com um diferente de inquilino, consultar e utilizar o **objectId** propriedade em vez disso. Se for necessário, obtenha o objectId da conta de utilizador necessário, utilizando o [show de utilizador do ad az][az-ad-user-show] comando. Forneça o nome principal de utilizador (UPN) da conta necessária:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um ficheiro, tal como *rbac-aad-user.yaml*e, em seguida, cole o seguinte conteúdo. Na última linha, substitua **userPrincipalName_or_objectId** com o ID de UPN ou o objeto. A escolha depende se o utilizador está ao mesmo inquilino do Azure AD ou não.

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

Aplicam-se a ligação ao utilizar o [kubectl aplicar][kubectl-apply] comando conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-user.yaml
```

Também é possível criar um enlace de função para todos os membros de um grupo do Azure AD. Grupos do Azure AD são especificados com o ID de objeto de grupo, conforme mostrado no exemplo a seguir.

Crie um ficheiro, tal como *rbac-aad-group.yaml*e, em seguida, cole o seguinte conteúdo. Atualize o ID de objeto de grupo com uma do inquilino do Azure AD:

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

Aplicam-se a ligação ao utilizar o [kubectl aplicar][kubectl-apply] comando conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre como proteger um cluster de Kubernetes com o RBAC, veja [utilizando o RBAC autorização][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Aceder ao cluster com o Azure AD

Extrair o contexto para o utilizador não administrador ao utilizar o [az aks get-credentials][az-aks-get-credentials] comando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de executar o `kubectl` de comando, será solicitado para autenticar ao utilizar o Azure. Siga na tela instruções para concluir o processo, conforme mostrado no exemplo a seguir:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando o processo estiver concluído, o token de autenticação é colocado em cache. Apenas lhe for pedido para iniciar sessão novamente quando o token expira ou o ficheiro de configuração Kubernetes seja recriado.

Se vir uma mensagem de erro de autorização, depois de entrar com êxito, verifique os seguintes critérios:

```console
error: You must be logged in to the server (Unauthorized)
```


- Definiu o ID de objeto apropriado ou UPN, dependendo se a conta de utilizador está no mesmo inquilino do Azure AD ou não.
- O utilizador não é um membro de mais de 200 grupos.
- O segredo definido no registo de aplicação para o servidor corresponde ao valor configurado com `--aad-server-app-secret`.

## <a name="next-steps"></a>Passos Seguintes

Para utilizar grupos e utilizadores do Azure AD para controlar o acesso aos recursos do cluster, consulte [controlar o acesso aos recursos do cluster com o controlo de acesso baseado em funções e identidades do Azure AD no AKS][azure-ad-rbac].

Para obter mais informações sobre como clusters seguros do Kubernetes, consulte [opções de acesso e identidade para o AKS][rbac-authorization].

Para saber mais sobre o controlo de identidade e de recursos, veja [melhores práticas para autenticação e autorização no AKS][operator-best-practices-identity].

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
