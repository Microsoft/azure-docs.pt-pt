---
title: Utilize identidades geridas no Serviço Azure Kubernetes
description: Saiba como utilizar identidades geridas no Serviço Azure Kubernetes (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 07/10/2020
ms.author: mlearned
ms.openlocfilehash: 95a303a4b6a83901560b26679bca920b9de4d3f4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250910"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Utilize identidades geridas no Serviço Azure Kubernetes

Atualmente, um cluster Azure Kubernetes Service (AKS) (especificamente, o provedor de nuvem Kubernetes) requer uma identidade para criar recursos adicionais como equilibradores de carga e discos geridos em Azure. Esta identidade pode ser uma *identidade gerida* ou um diretor *de serviço.* Se utilizar um [principal de serviço,](kubernetes-service-principal.md)deve fornecer um ou a AKS cria um em seu nome. Se utilizar a identidade gerida, esta será criada automaticamente pela AKS. Os agrupamentos que utilizam os princípios de serviço acabam por chegar a um estado em que o diretor de serviço deve ser renovado para manter o cluster a funcionar. Gerir os diretores de serviços adiciona complexidade, e é por isso que é mais fácil usar identidades geridas em vez disso. Os mesmos requisitos de permissão aplicam-se tanto aos princípios de serviço como às identidades geridas.

*As identidades geridas* são essencialmente um invólucro em torno dos diretores de serviço, e tornam a sua gestão mais simples. A rotação credencial para o MI ocorre automaticamente a cada 46 dias de acordo com o padrão do Azure Ative Directory. A AKS utiliza tipos de identidade geridos atribuídos pelo sistema e atribuídos pelo utilizador. Estas identidades são atualmente imutáveis. Para saber mais, leia sobre [identidades geridas para recursos Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="before-you-begin"></a>Before you begin

Deve ter o seguinte recurso instalado:

- O Azure CLI, versão 2.8.0 ou mais tarde

## <a name="limitations"></a>Limitações

* Trazer as suas próprias identidades geridas não é suportado atualmente.
* Os agrupamentos AKS com identidades geridas só podem ser ativados durante a criação do cluster.
* Os clusters AKS existentes não podem ser atualizados ou atualizados para permitir identidades geridas.
* Durante as operações **de atualização** do cluster, a identidade gerida está temporariamente indisponível.

## <a name="summary-of-managed-identities"></a>Resumo das identidades geridas

A AKS usa várias identidades geridas para serviços incorporados e addons.

| Identidade                       | Nome    | Caso de utilização | Permissões por defeito | Traga a sua própria identidade
|----------------------------|-----------|----------|
| Plano de controlo | não visível | Utilizado pela AKS para gerir recursos de networking, por exemplo, criar um equilibrador de carga para entradas, IP público, etc.| Papel contribuinte para o grupo de recursos nó | Atualmente, não é suportado
| Kubelet | AKS Cluster Name-agentpool | Autenticação com Registo de Contentores Azure (ACR) | Função do leitor para grupo de recursos de nó | Atualmente, não é suportado
| Add-on | AzurenPM | Nenhuma identidade necessária | ND | Não
| Add-on | Monitorização da rede AzureCNI | Nenhuma identidade necessária | ND | Não
| Add-on | azurepolicy (gatekeeper) | Nenhuma identidade necessária | ND | Não
| Add-on | azurepolicy | Nenhuma identidade necessária | ND | Não
| Add-on | Calico | Nenhuma identidade necessária | ND | Não
| Add-on | Dashboard | Nenhuma identidade necessária | ND | Não
| Add-on | HTTPApplicationRouting | Gere os recursos de rede necessários | Função do leitor para grupo de recursos de nó, papel de contribuinte para a zona DNS | Não
| Add-on | Gateway de aplicação ingress | Gere os recursos de rede necessários| Papel contribuinte para o grupo de recursos de nó | Não
| Add-on | omsagent | Usado para enviar métricas AKS para Azure Monitor | Função de editor de métricas de monitorização | Não
| Add-on | Nó virtual (ACIConnector) | Gere os recursos de rede necessários para as instâncias do contentor Azure (ACI) | Papel contribuinte para o grupo de recursos de nó | Não


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

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Utilize o seguinte comando para consultar a identidade gerida do seu plano de controlo:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

O resultado deve parecer:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Para criar e utilizar o seu próprio VNet, endereço IP estático ou disco Azure anexado onde os recursos estão fora do grupo de recursos do nó do trabalhador, utilize o PrincipalID do cluster System Assigned Managed Identity para executar uma atribuição de papel. Para obter mais informações sobre a atribuição de funções, consulte [o delegado de acesso a outros recursos da Azure.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)
>
> As autorizações concedidas ao cluster Identidade Gerida utilizada pelo fornecedor Azure Cloud podem demorar 60 minutos a preencher.

Finalmente, obtenha credenciais para aceder ao cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

O cluster será criado em poucos minutos. Em seguida, pode implementar as suas cargas de trabalho de aplicação para o novo cluster e interagir com ele, tal como fez com os clusters AKS baseados em serviços.

## <a name="next-steps"></a>Passos seguintes
* Utilize [os modelos Azure Resource Manager (ARM)][aks-arm-template] para criar clusters ativados pela Identidade Gerida.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
