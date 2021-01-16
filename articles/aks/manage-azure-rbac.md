---
title: Gerir a Azure RBAC em Kubernetes From Azure
titleSuffix: Azure Kubernetes Service
description: Saiba como utilizar o Azure RBAC para autorização kubernetes com o Serviço Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: af8d0300b533d9f25cddf225f4ffbe78ca6bf2cb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98249640"
---
# <a name="use-azure-rbac-for-kubernetes-authorization-preview"></a>Utilizar o RBAC do Azure para Autorização do Kubernetes (pré-visualização)

Hoje já pode alavancar [a autenticação integrada entre o Azure Ative Directory (Azure AD) e a AKS](managed-aad.md). Quando ativado, esta integração permite que os clientes utilizem utilizadores, grupos ou principais de serviço da Azure como temas em Kubernetes RBAC, consulte mais [aqui.](azure-ad-rbac.md)
Esta funcionalidade permite-lhe gerir separadamente as identidades e credenciais dos utilizadores para o Kubernetes. No entanto, ainda tem de configurar e gerir o RBAC de Azure e o RBAC de Kubernetes separadamente. Para mais detalhes sobre a autenticação e autorização com o RBAC na AKS, consulte [aqui.](concepts-identity.md)

Este documento abrange uma nova abordagem que permite a gestão unificada e o controlo de acessos através dos recursos Azure Resources, AKS e Kubernetes.

## <a name="before-you-begin"></a>Before you begin

A capacidade de gerir o RBAC para os recursos de Kubernetes da Azure dá-lhe a opção de gerir o RBAC para os recursos do cluster, quer utilizando mecanismos Azure ou Kubernetes nativos. Quando ativados, os principais AD do Azure serão validados exclusivamente pela Azure RBAC, enquanto os utilizadores regulares de Kubernetes e contas de serviço são validados exclusivamente pela Kubernetes RBAC. Para mais detalhes sobre a autenticação e autorização com o RBAC na AKS, consulte [aqui.](concepts-identity.md#azure-rbac-for-kubernetes-authorization-preview)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="prerequisites"></a>Pré-requisitos 
- Certifique-se de que tem a versão Azure CLI 2.9.0 ou mais tarde
- Certifique-se de que tem a bandeira de `EnableAzureRBACPreview` recurso ativada.
- Certifique-se de que tem a `aks-preview` [extensão CLI][az-extension-add] v0.4.55 ou superior instalada
- Certifique-se de que instalou [kubectl v1.18.3+][az-aks-install-cli]

#### <a name="register-enableazurerbacpreview-preview-feature"></a>Registar `EnableAzureRBACPreview` recurso de pré-visualização

Para criar um cluster AKS que utilize O RBAC Azure para a Autorização Kubernetes, tem de ativar a bandeira de `EnableAzureRBACPreview` funcionalidades na sua subscrição.

Registe a bandeira de `EnableAzureRBACPreview` características utilizando o comando [de registo de recurso az,][az-feature-register] como mostra o seguinte exemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureRBACPreview"
```

 Pode verificar o estado de registo utilizando o comando [da lista de recursos az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureRBACPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

#### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar um cluster AKS que utilize o Azure RBAC, precisa da versão de extensão CLI *de pré-visualização aks* 0.4.55 ou superior. Instale a extensão Azure CLI *de pré-visualização aks* utilizando o comando [de adicionar extensão az][az-extension-add] ou instale quaisquer atualizações disponíveis utilizando o comando de atualização de [extensão az:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitações

- Requer [integração AD gerida azure](managed-aad.md).
- Não é possível integrar o Azure RBAC para a autorização de Kubernetes em clusters existentes durante a pré-visualização, mas poderá fazê-lo na Disponibilidade Geral (GA).
- Utilize [kubectl v1.18.3+][az-aks-install-cli].
- Se você tem CRDs e está fazendo definições de papéis personalizados, a única maneira de cobrir CRDs hoje é fornecer `Microsoft.ContainerService/managedClusters/*/read` . A AKS está a trabalhar em fornecer mais permissões granulares para CRDs. Para os restantes objetos pode utilizar os grupos API específicos, por exemplo: `Microsoft.ContainerService/apps/deployments/read` .
- As novas atribuições de funções podem levar até 5 min para propagar e ser atualizadas pelo servidor de autorização.
- Requer que o inquilino AZure AD configurado para a autenticação seja o mesmo que o inquilino para a subscrição que detém o cluster AKS. 

## <a name="create-a-new-cluster-using-azure-rbac-and-managed-azure-ad-integration"></a>Criar um novo cluster usando a Azure RBAC e gerir a integração AD do AZure

Crie um cluster AKS utilizando os seguintes comandos CLI.

Criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Crie o cluster AKS com integração AD gerida e Azure RBAC para a Autorização Kubernetes.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad --enable-azure-rbac
```

Uma criação bem sucedida de um cluster com integração AD AZure e Azure RBAC para a Autorização Kubernetes tem a seguinte secção no organismo de resposta:

```json
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "enableAzureRbac": true,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "****-****-****-****-****"
  }
```

## <a name="create-role-assignments-for-users-to-access-cluster"></a>Criar atribuições de funções para os utilizadores acederem ao cluster

A AKS fornece as seguintes quatro funções incorporadas:


| Função                                | Description  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC Reader  | Permite o acesso apenas à leitura para ver a maioria dos objetos num espaço de nome. Não permite visualizar papéis ou encadernações de papéis. Esta função não permite a `Secrets` visualização, uma vez que a leitura do conteúdo dos Segredos permite o acesso às credenciais do ServiceAccount no espaço de nomes, o que permitiria o acesso da API como qualquer ServiceAccount no espaço de nomes (uma forma de escalada de privilégio)  |
| Azure Kubernetes Service RBAC Writer | Permite ler/escrever o acesso à maioria dos objetos num espaço de nome. Esta função não permite visualizar ou modificar papéis ou encadernações de papéis. No entanto, esta função permite aceder `Secrets` e executar Pods como qualquer ServiceAccount no espaço de nomes, para que possa ser usado para obter os níveis de acesso API de qualquer ServiceAccount no espaço de nomes. |
| Azure Kubernetes Serviço RBAC Administrador  | Permite o acesso a administrador, destinado a ser concedido dentro de um espaço de nome. Permite o acesso de leitura/escrita à maioria dos recursos num espaço de nome (ou âmbito de cluster), incluindo a capacidade de criar papéis e encadernações de papéis dentro do espaço de nomes. Esta função não permite escrever acesso à quota de recursos ou ao próprio espaço de nome. |
| Azure Kubernetes Serviço RBAC Cluster Admin  | Permite o acesso do super-utilizador para realizar qualquer ação em qualquer recurso. Dá total controlo sobre todos os recursos do cluster e em todos os espaços de nome. |


As atribuições de funções a todo o **cluster AKS** podem ser feitas quer na lâmina do controlo de acesso (IAM) do recurso de cluster no portal Azure, quer através da utilização de comandos Azure CLI, conforme mostrado abaixo:

```bash
# Get your AKS Resource ID
AKS_ID=$(az aks show -g MyResourceGroup -n MyManagedCluster --query id -o tsv)
```

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Admin" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

onde `<AAD-ENTITY-ID>` poderia ser um nome de utilizador (por exemplo, ) ou mesmo o user@contoso.com ClientID de um principal de serviço.

Também pode criar atribuições de funções a um **espaço de nome** específico dentro do cluster:

```azurecli-interactive
az role assignment create --role "Azure Kubernetes Service RBAC Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID/namespaces/<namespace-name>
```

Hoje em dia, as atribuições de funções apetecível para espaços de nome precisam de ser configuradas através do Azure CLI.


### <a name="create-custom-roles-definitions"></a>Criar definições de funções personalizadas

Opcionalmente, pode optar por criar a sua própria definição de papel e, em seguida, atribuir como acima.

Abaixo está um exemplo de uma definição de papel que permite que um utilizador leia apenas implementações e nada mais. Pode consultar aqui a lista completa de [possíveis](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)ações.


Copie o json abaixo num ficheiro chamado `deploy-view.json` .

```json
{
    "Name": "AKS Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.ContainerService/managedClusters/apps/deployments/read"  
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<YOUR SUBSCRIPTION ID>"
    ]   
}
```

Substitua `<YOUR SUBSCRIPTION ID>` o ID da sua subscrição, que pode obter executando:

```azurecli-interactive
az account show --query id -o tsv
```


Agora podemos criar a definição de função executando o comando abaixo a partir da pasta onde `deploy-view.json` guardou:

```azurecli-interactive
az role definition create --role-definition @deploy-view.json 
```

Agora que tem a sua definição de papel, pode atribuí-la a um utilizador ou outra identidade executando:

```azurecli-interactive
az role assignment create --role "AKS Deployment Viewer" --assignee <AAD-ENTITY-ID> --scope $AKS_ID
```

## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubectl"></a>Use Azure RBAC para autorização kubernetes com `kubectl`

> [!NOTE]
> Certifique-se de que tem o mais recente kubectl executando o comando abaixo:
>
> ```azurecli-interactive
> az aks install-cli
> ```
> Talvez precises de o executar com `sudo` privilégios. 

Agora que atribuiu o seu papel e permissões desejadas. Pode começar a ligar para a API de Kubernetes, por exemplo, a partir de `kubectl` .

Para este fim, vamos primeiro obter o kubeconfig do cluster usando o comando abaixo:

```azurecli-interactive
az aks get-credentials -g MyResourceGroup -n MyManagedCluster
```

> [!IMPORTANT]
> Você precisará do cluster [de serviço Azure Kubernetes função](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) incorporada para executar o passo acima.

Agora, você pode usar kubectl para, por exemplo, listar os nós no cluster. A primeira vez que o executar terá de iniciar sedência, e os comandos subsequentes usarão o respetivo token de acesso.

```azurecli-interactive
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="use-azure-rbac-for-kubernetes-authorization-with-kubelogin"></a>Use Azure RBAC para autorização kubernetes com `kubelogin`

Para desbloquear cenários adicionais como logins não interativos, versões mais antigas `kubectl` ou alavancagem de SSO em vários clusters sem a necessidade de iniciar sessão em novos clusters, tendo em conta que o seu token ainda é válido, a AKS criou um plugin executivo chamado [`kubelogin`](https://github.com/Azure/kubelogin) .

Pode usá-lo correndo:

```bash
export KUBECONFIG=/path/to/kubeconfig
kubelogin convert-kubeconfig
``` 

Da primeira vez, terá de assinar de forma interativa como com o kubectl regular, mas depois já não terá de o fazer, mesmo para novos clusters AD Azure (desde que o seu token ainda seja válido).

```bash
kubectl get nodes
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-93451573-vmss000000   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000001   Ready    agent   3h6m   v1.15.11
aks-nodepool1-93451573-vmss000002   Ready    agent   3h6m   v1.15.11
```


## <a name="clean-up"></a>Limpeza

### <a name="clean-role-assignment"></a>Atribuição de Função Limpa

```azurecli-interactive
az role assignment list --scope $AKS_ID --query [].id -o tsv
```
Copie o ID ou iDs de todas as tarefas que fez e depois.

```azurecli-interactive
az role assignment delete --ids <LIST OF ASSIGNMENT IDS>
```

### <a name="clean-up-role-definition"></a>Limpar a definição de função

```azurecli-interactive
az role definition delete -n "AKS Deployment Viewer"
```

### <a name="delete-cluster-and-resource-group"></a>Eliminar cluster e grupo de recursos

```azurecli-interactive
az group delete -n MyResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre AKS Autenticação, Autorização, Kubernetes RBAC e Azure RBAC [aqui.](concepts-identity.md)
- Leia mais sobre a Azure RBAC [aqui.](../role-based-access-control/overview.md)
- Leia mais sobre todas as ações que pode usar para definir granularmente papéis Azure personalizados para a autorização de Kubernetes [aqui.](../role-based-access-control/resource-provider-operations.md#microsoftcontainerservice)


<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli&preserve-view=true
