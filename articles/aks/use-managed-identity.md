---
title: Utilize identidades geridas no Serviço Azure Kubernetes
description: Saiba como utilizar identidades geridas no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 7eb0ab6247e8afc27f938b8b4a25d1fb1ee723f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876999"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Utilize identidades geridas no Serviço Azure Kubernetes

Atualmente, um cluster Azure Kubernetes Service (AKS) (especificamente, o provedor de nuvem Kubernetes) requer uma identidade para criar recursos adicionais como equilibradores de carga e discos geridos em Azure. Esta identidade pode ser uma *identidade gerida* ou um diretor *de serviço.* Se utilizar um [principal de serviço,](kubernetes-service-principal.md)deve fornecer um ou a AKS cria um em seu nome. Se utilizar a identidade gerida, esta será criada automaticamente pela AKS. Os agrupamentos que utilizam os princípios de serviço acabam por chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade, e é por isso que é mais fácil usar identidades geridas em vez disso. Os mesmos requisitos de permissão aplicam-se tanto aos princípios de serviço como às identidades geridas.

*As identidades geridas* são essencialmente um invólucro em torno dos diretores de serviço, e tornam a sua gestão mais simples. A rotação credencial para o MI ocorre automaticamente a cada 46 dias de acordo com o padrão do Azure Ative Directory. A AKS utiliza tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador. Estas identidades são atualmente imutáveis. Para saber mais, leia sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Antes de começar

Deve ter o seguinte recurso instalado:

- O Azure CLI, versão 2.15.1 ou mais tarde

## <a name="limitations"></a>Limitações

* Os inquilinos movem-se/migram de agrupamentos de identidade geridos não são suportados.
* Se o cluster `aad-pod-identity` tiver ativado, as cápsulas Node-Managed Identity (NMI) modificam os iptables dos nós para intercetar chamadas para o ponto final dos metadados Azure Instance. Esta configuração significa que qualquer pedido feito ao ponto final dos metadados é intercetado pelo NMI mesmo que a cápsula não utilize `aad-pod-identity` . AzurePodIdentityException CRD pode ser configurado para informar `aad-pod-identity` que quaisquer pedidos para o ponto final de metadados originários de um pod que corresponda às etiquetas definidas em CRD devem ser proxiited sem qualquer processamento em NMI. As cápsulas do sistema com `kubernetes.azure.com/managedby: aks` etiqueta no espaço de _nomes do sistema kube_ devem ser excluídas `aad-pod-identity` configurando o CRD AzurePodIdentityException. Para obter mais informações, consulte [Desativar a identidade do aad-pod para uma cápsula ou aplicação específica.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)
  Para configurar uma exceção, instale a [yaML de exceção ao microfone.](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)

## <a name="summary-of-managed-identities"></a>Resumo das identidades geridas

A AKS usa várias identidades geridas para serviços incorporados e addons.

| Identidade                       | Name    | Caso de utilização | Permissões por defeito | Traga a sua própria identidade
|----------------------------|-----------|----------|
| Plano de controlo | não visível | Utilizados por componentes de planos de controlo AKS para gerir recursos de cluster, incluindo equilibradores de carga ingressuais e IPs públicos geridos pela AKS, e operações de Cluster Autoscaler | Papel contribuinte para o grupo de recursos nó | Suportado
| Kubelet | AKS Cluster Name-agentpool | Autenticação com Registo de Contentores Azure (ACR) | NA (para kubernetes v1.15+) | Atualmente, não é suportado
| Add-on | AzurenPM | Nenhuma identidade necessária | ND | No
| Add-on | Monitorização da rede AzureCNI | Nenhuma identidade necessária | ND | No
| Add-on | política azul (gatekeeper) | Nenhuma identidade necessária | ND | No
| Add-on | política azul | Nenhuma identidade necessária | ND | No
| Add-on | Calico | Nenhuma identidade necessária | ND | No
| Add-on | Dashboard | Nenhuma identidade necessária | ND | No
| Add-on | HTTPApplicationRouting | Gere os recursos de rede necessários | Função do leitor para grupo de recursos de nó, papel de contribuinte para a zona DNS | No
| Add-on | Gateway de aplicação ingress | Gere os recursos de rede necessários| Papel contribuinte para o grupo de recursos de nó | No
| Add-on | omsagent | Usado para enviar métricas AKS para Azure Monitor | Função de editor de métricas de monitorização | No
| Add-on | Virtual-Node (ACIConnector) | Gere os recursos de rede necessários para as instâncias do contentor Azure (ACI) | Papel contribuinte para o grupo de recursos de nó | No
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

Uma vez criado o cluster, pode então implementar as cargas de trabalho da sua aplicação para o novo cluster e interagir com ele, tal como fez com os clusters AKS baseados em serviços.

Finalmente, obtenha credenciais para aceder ao cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Atualizar um cluster AKS para identidades geridas (Pré-visualização)

Agora pode atualizar um cluster AKS que está a trabalhar com os principais de serviço para trabalhar com identidades geridas utilizando os seguintes comandos CLI.

Em primeiro lugar, registe a bandeira de características para a identidade atribuída ao sistema:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Atualizar a identidade atribuída ao sistema:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Registar a bandeira de recurso para a identidade atribuída ao utilizador:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Atualizar a identidade atribuída ao utilizador:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Uma vez atualizadas as identidades atribuídas ao sistema ou atribuídas ao utilizador para a identidade gerida, execute um `az aks nodepool upgrade --node-image-only` nos seus nós para completar a atualização para a identidade gerida.

## <a name="obtain-and-use-the-system-assigned-managed-identity-for-your-aks-cluster"></a>Obtenha e utilize a identidade gerida atribuída pelo sistema para o seu cluster AKS

Confirme que o seu cluster AKS está a utilizar a identidade gerida com o seguinte comando CLI:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "servicePrincipalProfile"
```

Se o cluster estiver a usar identidades geridas, verá um `clientId` valor de "msi". Em vez disso, um cluster que usa um Diretor de Serviço mostrará o ID do objeto. Por exemplo: 

```output
{
  "clientId": "msi"
}
```

Depois de verificar se o cluster está a utilizar identidades geridas, pode encontrar o ID do objeto do sistema de controlo atribuído com o seguinte comando:

```azurecli-interactive
az aks show -g <RGName> -n <ClusterName> --query "identity"
```

```output
{
    "principalId": "<object-id>",
    "tenantId": "<tenant-id>",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
```

> [!NOTE]
> Para criar e utilizar o seu próprio VNet, endereço IP estático ou disco Azure anexado onde os recursos estão fora do grupo de recursos do nó do trabalhador, utilize o PrincipalID do cluster System Assigned Managed Identity para executar uma atribuição de papel. Para obter mais informações sobre a atribuição de funções, consulte [o delegado de acesso a outros recursos da Azure.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> As autorizações concedidas ao cluster Identidade Gerida utilizada pelo fornecedor Azure Cloud podem demorar 60 minutos a preencher.


## <a name="bring-your-own-control-plane-mi"></a>Traga o seu próprio avião de controlo MI
Uma identidade de plano de controlo personalizado permite o acesso à identidade existente antes da criação do cluster. Esta funcionalidade permite cenários como a utilização de um VNET personalizado ou um Tipo de UDR de saída com uma identidade gerida pré-criada.

Deve ter o Azure CLI, versão 2.15.1 ou posteriormente instalado.

### <a name="limitations"></a>Limitações
* O Governo de Azure não é apoiado neste momento.
* Azure China 21Vianet não é atualmente apoiada.

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

## <a name="bring-your-own-kubelet-mi-preview"></a>Traga o seu próprio kubelet MI (Preview)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Uma identidade Kubelet permite o acesso à identidade existente antes da criação do cluster. Esta funcionalidade permite cenários como a ligação ao ACR com uma identidade gerida pré-criada.

### <a name="prerequisites"></a>Pré-requisitos

- Deve ter o Azure CLI, versão 2.21.1 ou posteriormente instalado.
- Tem de ter a pré-visualização aks, a versão 0.5.10 ou posteriormente instalada.

### <a name="limitations"></a>Limitações

- Só funciona com um cluster gerido User-Assigned.
- O Governo de Azure não é apoiado neste momento.
- Azure China 21Vianet não é atualmente apoiada.

Primeiro, registe a bandeira de recurso para a identidade de Kubelet:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n CustomKubeletIdentityPreview
```

Demora alguns minutos para que o estado seja *apresentado.* Pode verificar o estado de registo utilizando o comando [da lista de recursos az:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomKubeletIdentityPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, reaprovi o registo do fornecedor de recursos *Microsoft.ContainerService* utilizando o comando [de registo do fornecedor az:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="create-or-obtain-managed-identities"></a>Criar ou obter identidades geridas

Se ainda não tens uma identidade gerida por um avião de controlo, deves ir em frente e criar uma. O exemplo a seguir utiliza o comando [de criação de identidade az:][az-identity-create]

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
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se ainda não tem uma identidade gerida por kubelet, deve ir em frente e criar uma. O exemplo a seguir utiliza o comando [de criação de identidade az:][az-identity-create]

```azurecli-interactive
az identity create --name myKubeletIdentity --resource-group myResourceGroup
```

O resultado deve parecer:

```output
{
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity", 
  "location": "westus2",
  "name": "myKubeletIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Se a sua identidade gerida existente fizer parte da sua subscrição, pode utilizar o comando [da lista de identidade az][az-identity-list] para a consultar:

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

### <a name="create-a-cluster-using-kubelet-identity"></a>Criar um cluster usando a identidade do kubelet

Agora pode usar o seguinte comando para criar o seu cluster com as suas identidades existentes. Forneça o id de identidade do plano de controlo através `assign-identity` e a identidade gerida por kubelet através `assign-kublet-identity` de:

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
    --assign-kubelet-identity <kubelet-identity-id> \
```

Uma criação de cluster bem sucedida usando a sua própria identidade gerida por kubelet contém a seguinte saída:

```output
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
        "clientId": "<client-id>",
        "principalId": "<principal-id>"
      }
    }
  },
  "identityProfile": {
    "kubeletidentity": {
      "clientId": "<client-id>",
      "objectId": "<object-id>",
      "resourceId": "/subscriptions/<subscriptionid>/resourcegroups/resourcegroups/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myKubeletIdentity"
    }
  },
```

## <a name="next-steps"></a>Passos seguintes
* Utilize [modelos de Gestor de Recursos Azure ][aks-arm-template] para criar clusters ativados por identidade gerida.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-list]: /cli/azure/identity#az_identity_list
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
