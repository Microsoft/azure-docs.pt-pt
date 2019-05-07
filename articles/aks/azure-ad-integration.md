---
title: Integrar o Azure Active Directory com o serviço Kubernetes do Azure
description: Como criar clusters do Azure Kubernetes Service (AKS) habilitados no Azure Active Directory.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: iainfou
ms.openlocfilehash: 026c0eefc0c4fe31e72ecad91a4a7b558f367487
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192113"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory com o serviço Kubernetes do Azure

O Azure Kubernetes Service (AKS) pode ser configurado para utilizar o Azure Active Directory (AD) para autenticação de utilizador. Nesta configuração, pode iniciar sessão para um cluster do AKS com o token de autenticação do Azure Active Directory. Além disso, os administradores de cluster são capazes de configurar o controlo de acesso baseado em função do Kubernetes (RBAC) com base na associação de grupo de identidade ou o diretório de um utilizador.

Este artigo mostra-lhe como implementar os pré-requisitos para o AKS e o Azure AD, em seguida, como implementar um cluster do Azure AD habilitado e criar uma função RBAC básica do cluster do AKS com o portal do Azure. Também pode [concluir estes passos com a CLI do Azure][azure-ad-cli].

As seguintes limitações aplicam-se:

- O Azure AD só pode ser ativado ao criar um cluster novo, habilitados no RBAC. Não é possível ativar o Azure AD num cluster do AKS existente.
- *Convidado* utilizadores no Azure AD, tal como se estiver a utilizar um início de sessão Federado de outro diretório, não são suportadas.

## <a name="authentication-details"></a>Detalhes de autenticação

Autenticação do Azure AD é fornecida para clusters do AKS com OpenID Connect. OpenID Connect é uma camada de identidade criada com base no protocolo de OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [abrir ID connect documentação][open-id-connect].

De dentro do cluster de Kubernetes, autenticação de Token do Webhook é usada para verificar os tokens de autenticação. Autenticação de token do Webhook é configurada e gerenciada como parte do AKS cluster. Para obter mais informações sobre a autenticação de token do Webhook, veja a [documentação de autenticação de webhook][kubernetes-webhook].

Para fornecer autenticação do Azure AD para um cluster do AKS, são criadas duas aplicações do Azure AD. A primeira aplicação é um componente de servidor que fornece a autenticação de utilizador. O segundo aplicativo é um componente de cliente que é utilizado quando lhe for pedido, a CLI para a autenticação. Esta aplicação de cliente utiliza a aplicação de servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicação do Azure AD estão configurados. Os passos para delegar permissões para cada um dos aplicativos devem ser concluídos por um administrador de inquilino do Azure.

## <a name="create-server-application"></a>Criar aplicação de servidor

A primeira aplicação do Azure AD é utilizada para obter uma associação de grupo utilizadores do Azure AD. Crie esta aplicação no portal do Azure.

1. Selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo**.

    * Dê um nome, a aplicação, tal como *AKSAzureADServer*.
    * Para **tipos de conta suportados**, escolha *contas neste diretório organizacional apenas*.
    * Escolher *Web* para o **URI de redirecionamento** escreva e introduza qualquer valor URI formatado como *https://aksazureadserver*.
    * Selecione **registar** quando tiver terminado.

1. Selecione **manifesto** e editar os `groupMembershipClaims` valor a `"All"`.

    ![Atualizar associação de grupo para todos](media/aad-integration/edit-manifest.png)

    **Guardar** as atualizações, depois de concluído.

1. No painel de navegação esquerdo da aplicação do Azure AD, selecione **certificados e segredos**.

    * Escolher **+ novo segredo do cliente**.
    * Adicionar uma descrição da chave, como *servidor do AKS do Azure AD*. Escolha uma hora de expiração, em seguida, selecione **adicionar**.
    * Anote o valor da chave. Apenas ele tem apresentado neste momento inicial. Quando implementa um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Server application secret`.

1. No painel de navegação esquerdo da aplicação do Azure AD, selecione **permissões de API**, em seguida, optar por **+ adicionar uma permissão**.

    * Sob **APIs da Microsoft**, escolha *Microsoft Graph*.
    * Escolher **permissões delegadas**, em seguida, coloque uma marca junto a **Directory > Directory.Read.All (dados do diretório de leitura)**.
        * Se um padrão delegado permissão para **utilizador > User.Read (iniciar sessão e ler o perfil de utilizador)** não existir, coloque uma marca esta permissão.
    * Escolher **permissões de aplicação**, em seguida, coloque uma marca junto a **Directory > Directory.Read.All (dados do diretório de leitura)**.

        ![Definir permissões de gráfico](media/aad-integration/graph-permissions.png)

    * Escolher **adicionar permissões** para guardar as atualizações.

    * Sob o **conceder autorização** secção, optar por **conceder autorização de administrador**. Este botão estiver a cinzento e não está disponível se a conta atual não é um administrador inquilino.

        Quando as permissões foram concedidas com êxito, a seguinte notificação é apresentada no portal do:

        ![Notificação de êxito permissões concedidas](media/aad-integration/permissions-granted.png)

1. No painel de navegação esquerdo da aplicação do Azure AD, selecione **expor uma API**, em seguida, optar por **+ adicionar um âmbito**.
    
    * Definir um *nome do âmbito*, *nome de exibição de consentimento de administrador*, e *descrição de consentimento do admin*, como *AKSAzureADServer*.
    * Certifique-se de que o **estado** está definida como *ativado*.

        ![Expor a aplicação de servidor como uma API para utilização com outros serviços](media/aad-integration/expose-api.png)

    * Escolher **Adicionar âmbito**.

1. Regresse à aplicação **descrição geral** página e tome nota do **ID de aplicação (cliente)**. Quando implementa um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Server application ID`.

   ![Obter ID da aplicação](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Criar aplicação de cliente

A segunda aplicação do Azure AD é utilizada quando iniciar sessão com a CLI do Kubernetes (`kubectl`).

1. Selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo**.

    * Dê um nome, a aplicação, tal como *AKSAzureADClient*.
    * Para **tipos de conta suportados**, escolha *contas neste diretório organizacional apenas*.
    * Escolher *Web* para o **URI de redirecionamento** escreva e introduza qualquer valor URI formatado como *https://aksazureadclient*.
    * Selecione **registar** quando tiver terminado.

1. No painel de navegação esquerdo da aplicação do Azure AD, selecione **permissões de API**, em seguida, optar por **+ adicionar uma permissão**.

    * Selecione **as minhas APIs**, em seguida, escolha a aplicação de servidor do Azure AD criada no passo anterior, tais como *AKSAzureADServer*.
    * Escolher **permissões delegadas**, em seguida, colocar uma marca de verificação junto a sua aplicação de servidor do Azure AD.

        ![Configurar permissões de aplicação](media/aad-integration/select-api.png)

    * Selecione **adicionar permissões**.

    * Sob o **conceder autorização** secção, optar por **conceder autorização de administrador**. Este botão estiver a cinzento e não está disponível se a conta atual não é um administrador inquilino.

        Quando as permissões foram concedidas com êxito, a seguinte notificação é apresentada no portal do:

        ![Notificação de êxito permissões concedidas](media/aad-integration/permissions-granted.png)

1. Na navegação do lado esquerda da aplicação do Azure AD, anote o **ID da aplicação**. Ao implementar um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Client application ID`.

   ![Obter o ID da aplicação](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Obter o ID de inquilino

Por fim, obtenha o ID do seu inquilino do Azure. Este valor é utilizado ao criar o cluster do AKS.

A partir do portal do Azure, selecione **do Azure Active Directory** > **propriedades** e tome nota do **ID do diretório**. Quando cria um cluster do AKS habilitados no AD do Azure, este valor é referido como o `Tenant ID`.

![Obter o ID de inquilino do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Implementar Cluster

Utilize o [criar grupo az] [ az-group-create] comando para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementar o cluster com o [criar az aks] [ az-aks-create] comando. Substitua os valores no comando de exemplo abaixo com os valores recolhidos durante a criação de aplicações do Azure AD para o ID da aplicação de servidor e segredo, ID de aplicação de cliente e o ID de inquilino:

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

Demora alguns minutos para criar o cluster do AKS.

## <a name="create-rbac-binding"></a>Criar o enlace de RBAC

Antes de uma conta do Azure Active Directory pode ser utilizada com o cluster do AKS, um enlace de função de cluster ou enlace de função tem de ser criado. *Funções* definir as permissões para conceder, e *enlaces* aplicá-las para os usuários desejados. Estas atribuições podem ser aplicadas a um determinado espaço de nomes ou em todo o cluster. Para obter mais informações, consulte [autorização RBAC usando][rbac-authorization].

Em primeiro lugar, utilize o [az aks get-credentials] [ az-aks-get-credentials] comando com o `--admin` argumento para iniciar sessão para o cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie um ClusterRoleBinding para uma conta do Azure AD que pretende conceder acesso para o cluster do AKS. O exemplo a seguir dá à conta de acesso completo a todos os espaços de nomes do cluster.

- Se o utilizador conceder que o enlace do RBAC para está no mesmo inquilino do Azure AD, atribua permissões com base no nome principal de utilizador (UPN). Avançar para o passo para criar o manifesto YAML o ClusterRuleBinding.

- Se o utilizador estiver no Azure AD com um diferente de inquilino, consultar e utilizar o *objectId* propriedade em vez disso. Se for necessário, obter o *objectId* do utilizador necessário contas utilizando o [show de utilizador do ad az] [ az-ad-user-show] comando. Forneça o nome principal de utilizador (UPN) da conta necessária:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um ficheiro, tal como *rbac-aad-user.yaml*e cole o seguinte conteúdo. Na última linha, substitua *userPrincipalName_or_objectId* com o ID de UPN ou o objeto, dependendo se o utilizador é o mesmo inquilino do Azure AD ou não.

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

Depois de executar um `kubectl` de comando, lhe for pedido para autenticar com o Azure. Siga na tela instruções para concluir o processo, conforme mostrado no exemplo a seguir:

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando terminar, o token de autenticação é colocado em cache. Apenas são reprompted para iniciar sessão quando o token expirou ou o ficheiro de configuração Kubernetes recriado.

Se vir uma mensagem de erro de autorização depois de iniciar sessão com êxito, verifique se:
1. O utilizador estiver a iniciar sessão como é não convidado na instância do Azure AD (neste cenário é muitas vezes o caso se utilizar uma conta federada a partir de um diretório diferente).
2. O utilizador não é um membro de mais de 200 grupos.
3. Segredo definido no registo de aplicação para o servidor não corresponde ao valor configurado usando - aad-server--segredo da aplicação

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Passos Seguintes

Para utilizar grupos e utilizadores do Azure AD para controlar o acesso aos recursos do cluster, consulte [controlar o acesso aos recursos do cluster com o controlo de acesso baseado em funções e identidades do Azure AD no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters do Kubernetes, consulte [opções de acesso e identidade para o AKS)][rbac-authorization].

Para melhores práticas no controlo de identidade e de recursos, consulte [melhores práticas para autenticação e autorização no AKS][operator-best-practices-identity].

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
