---
title: Utilize identidades geridas no Serviço Azure Kubernetes
description: Saiba como utilizar identidades geridas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 07/17/2020
ms.author: thomasge
ms.openlocfilehash: 4e970f242d1c51218865fe459b8012f97add3d02
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299294"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Utilize identidades geridas no Serviço Azure Kubernetes

Atualmente, um cluster Azure Kubernetes Service (AKS) (especificamente, o provedor de nuvem Kubernetes) requer uma identidade para criar recursos adicionais como equilibradores de carga e discos geridos em Azure. Esta identidade pode ser uma *identidade gerida* ou um diretor *de serviço.* Se utilizar um [principal de serviço,](kubernetes-service-principal.md)deve fornecer um ou a AKS cria um em seu nome. Se utilizar a identidade gerida, esta será criada automaticamente pela AKS. Os agrupamentos que utilizam os princípios de serviço acabam por chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade, e é por isso que é mais fácil usar identidades geridas em vez disso. Os mesmos requisitos de permissão aplicam-se tanto aos princípios de serviço como às identidades geridas.

*As identidades geridas* são essencialmente um invólucro em torno dos diretores de serviço, e tornam a sua gestão mais simples. A rotação credencial para o MI ocorre automaticamente a cada 46 dias de acordo com o padrão do Azure Ative Directory. A AKS utiliza tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador. Estas identidades são atualmente imutáveis. Para saber mais, leia sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Before you begin

Deve ter o seguinte recurso instalado:

- O Azure CLI, versão 2.8.0 ou mais tarde

## <a name="limitations"></a>Limitações

* Os agrupamentos AKS com identidades geridas só podem ser ativados durante a criação do cluster.
* Os aglomerados AKS existentes não podem ser migrados para identidades geridas.
* Durante as operações **de atualização** do cluster, a identidade gerida está temporariamente indisponível.
* Os inquilinos movem-se/migram de agrupamentos de identidade geridos não são suportados.

## <a name="summary-of-managed-identities"></a>Resumo das identidades geridas

A AKS usa várias identidades geridas para serviços incorporados e addons.

| Identidade                       | Name    | Caso de utilização | Permissões por defeito | Traga a sua própria identidade
|----------------------------|-----------|----------|
| Plano de controlo | não visível | Utilizado pela AKS para recursos de rede geridos, incluindo equilibradores de carga ingresss e IPs públicos geridos por AKS | Papel contribuinte para o grupo de recursos nó | Pré-visualizar
| Kubelet | AKS Cluster Name-agentpool | Autenticação com Registo de Contentores Azure (ACR) | NA (para kubernetes v1.15+) | Atualmente, não é suportado
| Add-on | AzurenPM | Nenhuma identidade necessária | ND | No
| Add-on | Monitorização da rede AzureCNI | Nenhuma identidade necessária | ND | No
| Add-on | azurepolicy (gatekeeper) | Nenhuma identidade necessária | ND | No
| Add-on | azurepolicy | Nenhuma identidade necessária | ND | No
| Add-on | Calico | Nenhuma identidade necessária | ND | No
| Add-on | Dashboard | Nenhuma identidade necessária | ND | No
| Add-on | HTTPApplicationRouting | Gere os recursos de rede necessários | Função do leitor para grupo de recursos de nó, papel de contribuinte para a zona DNS | No
| Add-on | Gateway de aplicação ingress | Gere os recursos de rede necessários| Papel contribuinte para o grupo de recursos de nó | No
| Add-on | omsagent | Usado para enviar métricas AKS para Azure Monitor | Função de editor de métricas de monitorização | No
| Add-on | Nó virtual (ACIConnector) | Gere os recursos de rede necessários para as instâncias do contentor Azure (ACI) | Papel contribuinte para o grupo de recursos de nó | No
| Projeto OSS | aad-pod-identidade | Permite aplicações para aceder a recursos em nuvem de forma segura com o Azure Ative Directory (AAD) | ND | Passos para conceder permissão em https://github.com/Azure/aad-pod-identity#role-assignment .

## <a name="create-an-aks-cluster-with-managed-identities"></a>Criar um cluster AKS com identidades geridas

Agora pode criar um cluster AKS com identidades geridas utilizando os seguintes comandos CLI.

Primeiro, criar um grupo de recursos Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Em seguida, criar um cluster AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Uma criação de cluster bem sucedida usando identidades geridas contém esta informação de perfil principal do serviço:

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Utilize o seguinte comando para consultar a identidade gerida do seu plano de controlo:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

O resultado deve parecer:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Uma vez criado o cluster, pode então implementar as cargas de trabalho da sua aplicação para o novo cluster e interagir com ele, tal como fez com os clusters AKS baseados em serviços.

> [!NOTE]
> Para criar e utilizar o seu próprio VNet, endereço IP estático ou disco Azure anexado onde os recursos estão fora do grupo de recursos do nó do trabalhador, utilize o PrincipalID do cluster System Assigned Managed Identity para executar uma atribuição de papel. Para obter mais informações sobre a atribuição de funções, consulte [o delegado de acesso a outros recursos da Azure.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> As autorizações concedidas ao cluster Identidade Gerida utilizada pelo fornecedor Azure Cloud podem demorar 60 minutos a preencher.

Finalmente, obtenha credenciais para aceder ao cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="bring-your-own-control-plane-mi-preview"></a>Traga o seu próprio avião de controlo MI (Preview)
Uma identidade de plano de controlo personalizado permite o acesso à identidade existente antes da criação do cluster. Isto permite cenários como a utilização de um VNET personalizado ou um Tipo de UDR com uma identidade gerida.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Deve ter os seguintes recursos instalados:
- O Azure CLI, versão 2.9.0 ou mais tarde
- A extensão aks-preview 0.4.57

Limitações para trazer o seu próprio plano de controlo MI (Pré-visualização):
* O Governo de Azure não é apoiado neste momento.
* Azure China 21Vianet não é atualmente apoiada.

```azurecli-interactive
az extension add --name aks-preview
az extension list
```

```azurecli-interactive
az extension update --name aks-preview
az extension list
```

```azurecli-interactive
az feature register --name UserAssignedIdentityPreview --namespace Microsoft.ContainerService
```

Pode levar vários minutos para que o estado seja apresentado como **Registado**. Pode verificar o estado de registo utilizando o comando [da lista de funcionalidades AZ:](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UserAssignedIdentityPreview')].{Name:name,State:properties.state}"
```

Quando o estado aparecer como registado, reaprovida o registo do fornecedor de `Microsoft.ContainerService` recursos utilizando o comando de registo do fornecedor [az:](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Se ainda não tem uma identidade gerida, deve ir em frente e criar uma, por exemplo, utilizando [o CLI de identidade az][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
O resultado deve parecer:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se a sua identidade gerida fizer parte da sua subscrição, pode utilizar [o comando CLI de identidade az][az-identity-list] para a consultar.  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Agora pode utilizar o seguinte comando para criar o seu cluster com a sua identidade existente:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Uma criação de cluster bem sucedida usando as suas próprias identidades geridas contém esta informação de perfil de nomeações do utilizador:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Passos seguintes
* Utilize [os modelos Azure Resource Manager (ARM) ][aks-arm-template] para criar clusters ativados pela Identidade Gerida.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create&preserve-view=true
[az-identity-list]: /cli/azure/identity?view=azure-cli-latest#az-identity-list&preserve-view=true
