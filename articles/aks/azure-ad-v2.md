---
title: Use Azure AD no Serviço Azure Kubernetes
description: Saiba como utilizar o Azure AD no Serviço Azure Kubernetes (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114654"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrar o Azure AD no Serviço Azure Kubernetes (Pré-visualização)

> [!Note]
> Os atuais clusters AKS v1 com integração aD não são afetados pela nova experiência AKS v2.

A integração da Azure AD com o AKS v2 foi concebida para simplificar a integração do Azure AD com a experiência AKS v1, onde os utilizadores eram obrigados a criar uma aplicação para clientes, uma aplicação de servidor, e exigiu que o inquilino da AD Azure concedesse permissões ao Diretório. Na nova versão, o fornecedor de recursos AKS gere as aplicações de clientes e servidores para si.

## <a name="limitations"></a>Limitações

* Não é possível atualizar atualmente um cluster Azure AD ativado por AKS v1 para a experiência v2.

> [!IMPORTANT]
> As funcionalidades de pré-visualização AKS estão disponíveis numa base de autosserviço, opt-in. As pré-visualizações são fornecidas "as-is" e "conforme disponível", e estão excluídas dos Acordos de Nível de Serviço e da garantia limitada. As pré-visualizações do AKS são parcialmente cobertas pelo apoio ao cliente numa base de melhor esforço. Como tal, estas características não se destinam à utilização da produção. Para mais informações, consulte os seguintes artigos de apoio:
>
> - [Políticas de apoio aks](support-policies.md)
> - [FaQ de suporte azure](faq.md)

## <a name="before-you-begin"></a>Antes de começar

Deve ter os seguintes recursos instalados:

- O Azure CLI, versão 2.2.0 ou posterior
- A extensão de 0.4.38 de aks
- Kubectl com uma versão mínima de [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Para instalar/atualizar a extensão de pré-visualização de aks ou posteriormente, utilize os seguintes comandos Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Para instalar kubectl, utilize o seguinte:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Utilize [estas instruções](https://kubernetes.io/docs/tasks/tools/install-kubectl/) para outros sistemas operativos.

> [!CAUTION]
> Depois de registar uma funcionalidade numa subscrição, não pode atualmente desregistar essa funcionalidade. Quando ativa algumas funcionalidades de pré-visualização, podem ser utilizadas predefinições para todos os clusters AKS criados posteriormente na subscrição. Não ative funcionalidades de pré-visualização nas subscrições de produção. Em vez disso, utilize uma subscrição separada para testar funcionalidades de pré-visualização e recolher feedback.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Pode levar vários minutos para o estado mostrar como **Registado**. Pode verificar o estado de registo utilizando o comando da [lista de características az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Quando o estado mostrar como registado, `Microsoft.ContainerService` atualização do registo do fornecedor de recursos utilizando o comando de registo do [fornecedor az:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Criar um cluster AKS com AD Azure ativado

Agora pode criar um cluster AKS utilizando os seguintes comandos CLI.

Primeiro, criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Em seguida, criar um cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
O comando acima cria um cluster AKS de três nós, mas o utilizador, que criou o cluster, por padrão, não é membro de um grupo que tem acesso a este cluster. Este utilizador precisa de criar um grupo Azure AD, adicionar-se como membro do grupo e, em seguida, atualizar o cluster como mostrado abaixo. Siga as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Uma vez criado um grupo e adicionado a si mesmo (e outros) como membro, pode atualizar o cluster com o grupo Azure AD usando o seguinte comando

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Alternativamente, se criar primeiro um grupo e adicionar membros, pode ativar o grupo AD Azure no tempo de criação do seguinte comando,

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Uma criação bem-sucedida de um cluster Azure AD v2 tem a seguinte secção no corpo de resposta
```
"Azure ADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

O aglomerado é criado em poucos minutos.

## <a name="access-an-azure-ad-enabled-cluster"></a>Aceder a um cluster ativado pela AD Azure
Para obter as credenciais de administração para aceder ao cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Agora use o kubectl obter comando de nós para ver nós no cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Para obter as credenciais do utilizador para aceder ao cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Siga as instruções para iniciar sessão.

Recebe: **Tem de ser iniciado no servidor (Não autorizado)**

O utilizador acima obtém um erro porque o utilizador não faz parte de um grupo que tem acesso ao cluster.

## <a name="next-steps"></a>Passos seguintes

Conheça o Controlo de [Acesso baseado em Funções Azure AD.][azure-ad-rbac]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
