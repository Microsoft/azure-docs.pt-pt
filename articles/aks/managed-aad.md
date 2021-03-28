---
title: Use Azure AD no serviço Azure Kubernetes
description: Saiba como utilizar o Azure AD no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: b7918ecc31fe152bd25153ac8c899ce3ff8fdacb
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640596"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integração do Azure Ative Directory gerido pela AKS

A integração AZure AD gerida pela AKS foi projetada para simplificar a experiência de integração da AD Azure, onde os utilizadores eram previamente obrigados a criar uma aplicação para clientes, uma aplicação de servidor, e exigiu que o inquilino da AD Azure concedesse permissões de Leitura de Diretório. Na nova versão, o fornecedor de recursos AKS gere as aplicações do cliente e do servidor para si.

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação AD do Azure

Os administradores do cluster podem configurar o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC) com base na identidade ou membro do grupo de diretórios de um utilizador. A autenticação AZure AD é fornecida aos clusters AKS com OpenID Connect. OpenID Connect é uma camada de identidade construída em cima do protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação de ligação Open ID][open-id-connect].

Saiba mais sobre o fluxo de integração AD do Azure na documentação de [conceitos de integração do Azure Ative Directory.](concepts-identity.md#azure-active-directory-integration)

## <a name="limitations"></a>Limitações 

* A integração AD gerida pela AKS não pode ser desativada
* A alteração de um cluster integrado Azure AD gerido pela Azure para o legado AAD não é suportada
* os clusters rbac ativados não-Kubernetes não são suportados para a integração Azure AD gerida pela AKS
* Mudar o inquilino AZure AD associado à integração Azure AD gerida pela AKS não é suportado

## <a name="prerequisites"></a>Pré-requisitos

* A versão Azure CLI 2.11.0 ou mais tarde
* Kubectl com uma versão mínima de [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) ou [kubelogin](https://github.com/Azure/kubelogin)
* Se estiver a utilizar [o leme,](https://github.com/helm/helm)versão mínima do leme 3.3.

> [!Important]
> Deve utilizar o Kubectl com uma versão mínima de 1.18.1 ou kubelogin. A diferença entre as versões menores de Kubernetes e kubectl não deve ser mais do que uma versão. Se não utilizar a versão correta, irá notar problemas de autenticação.

Para instalar kubectl e kubelogin, utilize os seguintes comandos:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Utilize [estas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operativos.

## <a name="before-you-begin"></a>Antes de começar

Para o seu grupo, precisa de um grupo AD Azure. Este grupo é necessário como grupo de administração para o cluster conceder permissões de administração de cluster. Você pode usar um grupo AD Azure existente, ou criar um novo. Grave a identificação do objeto do seu grupo AD Azure.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Para criar um novo grupo AD Azure para os seus administradores de cluster, utilize o seguinte comando:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster AKS com Azure AD ativado

Crie um cluster AKS utilizando os seguintes comandos CLI.

Criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Crie um cluster AKS e permita o acesso da administração ao seu grupo AD Azure

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma criação bem sucedida de um cluster Azure AD gerido pela AKS tem a seguinte secção no corpo de resposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Uma vez criado o cluster, pode começar a aceder-lhe.

## <a name="access-an-azure-ad-enabled-cluster"></a>Aceda a um cluster AD AD Azure

Você precisará do cluster [de serviço Azure Kubernetes função](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) incorporada para fazer os seguintes passos.

Obtenha as credenciais de utilizador para aceder ao cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Siga as instruções para iniciar sinsuposições.

Utilize o comando kubectl get nodes para visualizar os nóns no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configure [O controlo de acesso baseado em funções (Azure RBAC)](./azure-ad-rbac.md) para configurar grupos de segurança adicionais para os seus clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Problemas de acesso à resolução de problemas com a Azure AD

> [!Important]
> Os passos descritos abaixo estão a contornar a autenticação normal do grupo Azure AD. Use-os apenas em caso de emergência.

Se estiver permanentemente bloqueado por não ter acesso a um grupo Azure AD válido com acesso ao seu cluster, ainda pode obter as credenciais de administração para aceder diretamente ao cluster.

Para fazer estes passos, você precisará ter acesso ao admin de cluster de [serviço Azure Kubernetes.](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Ativar a integração ad Ad gerida pela AkS no seu cluster existente

Pode ativar a integração AD AD gerida pela AKS no seu cluster rbac existente. Certifique-se de definir o seu grupo de administração para manter o acesso no seu cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Uma ativação bem sucedida de um cluster Ad Azure gerido pela AKS tem a seguinte secção no corpo de resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Baixe novamente as credenciais dos utilizadores para aceder ao seu cluster seguindo os passos [aqui][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade para integração AD Azure gerida pela AKS

Se o seu cluster utilizar a integração Azure AD legacy, pode fazer upgrade para a integração Ad Ad gerida pela AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Uma migração bem sucedida de um cluster Azure AD gerido pela AKS tem a seguinte secção no corpo de resposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se quiser aceder ao cluster, siga os passos [aqui.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Sinal não interativo com kubelogin

Existem alguns cenários não interativos, como os oleodutos de integração contínua, que não estão atualmente disponíveis com kubectl. Pode utilizar [`kubelogin`](https://github.com/Azure/kubelogin) para aceder ao cluster com o principal de inscrição do serviço não interativo.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Utilizar acesso condicional com AD e AKS AZURE

Ao integrar o Azure AD com o seu cluster AKS, também pode utilizar [o Acesso Condicional][aad-conditional-access] para controlar o acesso ao seu cluster.

> [!NOTE]
> O Acesso Condicionado AD Azure é uma capacidade AZure AD Premium.

Para criar um exemplo da política de acesso condicional a utilizar com AKS, complete os seguintes passos:

1. No topo do portal Azure, procure e selecione O Diretório Ativo Azure.
1. No menu do Azure Ative Directory no lado esquerdo, selecione *aplicações Enterprise*.
1. No menu para aplicações Da Empresa no lado esquerdo, selecione *Acesso Condicional*.
1. No menu de Acesso Condicional do lado esquerdo, selecione *Políticas* e depois *Nova política*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Adicionar uma política de acesso condicional":::
1. Insira um nome para a política, como *a política aks*.
1. Selecione *Utilizadores e grupos*, em *seguida, incluir* *utilizadores e grupos selecionados selecione*. Escolha os utilizadores e grupos onde pretende aplicar a política. Para este exemplo, escolha o mesmo grupo Azure AD que tem acesso administrativo ao seu cluster.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Selecionando utilizadores ou grupos para aplicar a política de acesso condicional":::
1. Selecione *aplicativos cloud ou ações*, em *seguida, em Incluir* *selecione aplicações Selecione*. Procure o *Serviço Azure Kubernetes* e selecione *Azure Kubernetes Service AAD Server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Selecionando o Azure Kubernetes Service AD Server para aplicar a política de acesso condicional":::
1. Em *Controlos de acesso*, selecione *Concessão*. Selecione *o acesso grant* e, em *seguida, exigir que o dispositivo seja marcado como conforme*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Selecionando apenas para permitir dispositivos compatíveis para a política de acesso condicional":::
1. Sob *a política de Ativar*, selecione *On* e, em *seguida, Criar*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Habilitar a política de acesso condicional":::

Obtenha as credenciais de utilizador para aceder ao cluster, por exemplo:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Siga as instruções para iniciar sinsuposições.

Utilize o `kubectl get nodes` comando para visualizar os nóns no cluster:

```azurecli-interactive
kubectl get nodes
```

Siga as instruções para voltar a entrar. Note que existe uma mensagem de erro indicando que está a iniciar sessão com sucesso, mas o seu administrador requer que o dispositivo que solicita o acesso seja gerido pelo seu AD Azure para aceder ao recurso.

No portal Azure, navegue para O Diretório Ativo Azure, selecione *aplicações enterprise* e, em seguida, em *'Activa's* select .  Note uma entrada no topo com um *Estado* de *Falha* e um *Acesso Condicional* de *Sucesso*. Selecione a entrada e, em *seguida,* selecione Acesso Condicional em *Detalhes*. Note que a sua política de Acesso Condicional está listada.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Entrada de entrada falhada devido à política de acesso condicional":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Configure o acesso ao cluster just-in-time com Azure AD e AKS

Outra opção para o controlo de acesso ao cluster é utilizar a Gestão de Identidade Privilegiada (PIM) para pedidos just-in-time.

>[!NOTE]
> O PIM é uma capacidade Azure AD Premium que requer um Premium P2 SKU. Para mais informações sobre Azure AD SKUs, consulte o [guia de preços.][aad-pricing]

Para integrar pedidos de acesso just-in-time com um cluster AKS utilizando a integração Azure AD gerida pela AKS, complete os seguintes passos:

1. No topo do portal Azure, procure e selecione O Diretório Ativo Azure.
1. Tome nota do ID do Inquilino, referido para o resto destas instruções como `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="Num navegador web, o ecrã do portal Azure para O Diretório Ativo Azure é mostrado com o ID do inquilino realçado.":::
1. No menu do Azure Ative Directory no lado esquerdo, sob *Gerir* *grupos selecionados* e depois *Novo Grupo*.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Mostra o ecrã de grupos de grupos do portal Azure com a opção 'Novo Grupo' em destaque.":::
1. Certifique-se de que um tipo de *segurança* do grupo está selecionado e introduza um nome de grupo, como *o myJITGroup*. Sob *Azure AD Roles pode ser atribuído a este grupo (Preview)*, selecione *Sim*. Por fim, *selecione Criar*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Mostra o novo ecrã de criação de grupo do portal Azure.":::
1. Serão devolvidos à página *grupos.* Selecione o seu grupo recém-criado e tome nota do ID do objeto, referido para o resto destas instruções como `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Mostra o ecrã do portal Azure para o grupo acabado de criar, destacando o Id do Objeto":::
1. Implementar um cluster AKS com integração Azure AD gerida pela AKS utilizando os `<tenant-id>` valores e `<object-id>` valores anteriores:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. De volta ao portal Azure, no menu de *Atividade* do lado esquerdo, selecione *Acesso Privilegiado (Pré-visualização)* e selecione *Enable Privileged Access*.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="É mostrada a página de acesso privilegiado (Preview) do portal Azure, com destaque para o 'Enable privileged access'":::
1. Selecione *Adicionar Atribuições* para começar a conceder acesso.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="O ecrã de acesso privilegiado (Preview) do portal Azure após a ativação é mostrado. A opção de 'Adicionar atribuições' está em destaque.":::
1. Selecione um papel de *membro* e selecione os utilizadores e grupos a quem deseja conceder acesso ao cluster. Estas atribuições podem ser modificadas a qualquer momento por um administrador de grupo. Quando estiver pronto para seguir em frente, selecione *Next*.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="É apresentado o ecrã de adesão do portal Azure, com um utilizador de amostra selecionado para ser adicionado como membro. A opção 'Seguinte' está em destaque.":::
1. Escolha um tipo de atribuição de *Ative,* a duração desejada, e forneça uma justificação. Quando estiver pronto para prosseguir, selecione *Atribua.* Para obter mais informações sobre tipos de atribuição, consulte [Atribuir elegibilidade para um grupo privilegiado de acesso (pré-visualização) em Gestão de Identidade Privilegiada.][aad-assignments]
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="É mostrado o ecrã de definição de atribuições do portal Azure. É selecionado um tipo de atribuição de 'Activo' e foi dada uma justificação de amostra. A opção 'Atribuir' é realçada.":::

Uma vez feitas as atribuições, verifique se o acesso just-in-time está a funcionar acedendo ao cluster. Por exemplo:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Siga os passos para se inscrever.

Utilize o `kubectl get nodes` comando para visualizar os nóns no cluster:

```azurecli-interactive
kubectl get nodes
```

Observe o requisito de autenticação e siga os passos para autenticar. Se for bem sucedido, deverá ver uma saída semelhante à seguinte:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Resolução de problemas

Se `kubectl get nodes` retornar um erro semelhante ao seguinte:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Certifique-se de que o administrador do grupo de segurança deu à sua conta uma missão *Ativa.*

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a integração do Azure RBAC para a Autorização Kubernetes][azure-rbac-integration]
* Saiba mais sobre [a integração da AD AZure com a Kubernetes RBAC][azure-ad-rbac].
* Utilize [kubelogin](https://github.com/Azure/kubelogin) para aceder a funcionalidades de autenticação Azure que não estejam disponíveis em kubectl.
* Saiba mais sobre [os conceitos de identidade AKS e Kubernetes.][aks-concepts-identity]
* Utilize [os modelos Azure Resource Manager (ARM) ][aks-arm-template] para criar clusters Azure AD geridos pela AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
