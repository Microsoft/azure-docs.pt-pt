---
title: Intervalos de IP autorizados do servidor de API no serviço kubernetes do Azure (AKS)
description: Saiba como proteger seu cluster usando um intervalo de endereços IP para acessar o servidor de API no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 59e64b7c84e589da57ea28d6655c9305f4fdc101
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058352"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Visualização-acesso seguro ao servidor de API usando intervalos de endereços IP autorizados no serviço de kubernetes do Azure (AKS)

No kubernetes, o servidor de API recebe solicitações para executar ações no cluster, como para criar recursos ou dimensionar o número de nós. O servidor de API é a maneira central de interagir com e gerenciar um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor de API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra como usar intervalos de endereços IP autorizados do servidor de API para limitar solicitações ao plano de controle. Esta funcionalidade encontra-se em pré-visualização.

> [!IMPORTANT]
> Os recursos de visualização do AKS são consentimento de autoatendimento. As visualizações são fornecidas "no estado em que se encontram" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações do AKS são parcialmente cobertas pelo suporte ao cliente com base no melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção. Para obter outras incompatibilidades, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes do suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você está trabalhando com clusters que usam [kubenet][kubenet].  Com os clusters baseados em [CNI (interface de rede de contêiner do Azure)][cni-networking] , você não terá a tabela de rotas necessária para proteger o acesso.  Você precisará criar a tabela de rotas manualmente.  Consulte [Gerenciando tabelas de rotas](https://docs.microsoft.com/azure/virtual-network/manage-route-table) para obter mais informações.

Os intervalos de IP autorizados do servidor de API só funcionam para novos clusters AKS que você criar. Este artigo mostra como criar um cluster AKS usando o CLI do Azure.

Você precisa do CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI do AKS-Preview

Para configurar intervalos de IP autorizados do servidor de API, você precisa da extensão da CLI *AKs-Preview* versão 0.4.1 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Registrar o sinalizador de recurso para sua assinatura

Para usar intervalos de IP autorizados do servidor de API, primeiro habilite um sinalizador de recurso em sua assinatura. Para registrar o sinalizador de recurso *APIServerSecurityPreview* , use o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Leva alguns minutos para que o status seja mostrado *registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você configura intervalos de IP autorizados do servidor de API:

* No momento, não é possível usar Azure Dev Spaces porque a comunicação com o servidor de API também está bloqueada.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral dos intervalos de IP autorizados do servidor de API

O servidor de API do kubernetes é como as APIs subjacentes do kubernetes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, `kubectl` como o ou o painel do kubernetes. O AKS fornece um mestre de cluster de locatário único, com um servidor de API dedicado. Por padrão, o servidor de API recebe um endereço IP público e você deve controlar o acesso usando controles de acesso baseado em função (RBAC).

Para proteger o acesso ao servidor do plano de controle AKS/API publicamente acessível, você pode habilitar e usar intervalos de IP autorizados. Esses intervalos de IP autorizados permitem que os intervalos de endereços IP definidos se comuniquem com o servidor de API. Uma solicitação feita ao servidor de API de um endereço IP que não faz parte desses intervalos de IP autorizado é bloqueada. Você deve continuar a usar o RBAC para autorizar os usuários e as ações que eles solicitam.

Para obter mais informações sobre o servidor de API e outros componentes de cluster, consulte [kubernetes Core Concepts for AKs][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Os intervalos de IP autorizados do servidor de API funcionam apenas para novos clusters AKS. Você não pode habilitar intervalos de IP autorizados como parte da operação de criação de cluster. Se você tentar habilitar intervalos de IP autorizados como parte do processo de criação do cluster, os nós de cluster não poderão acessar o servidor de API durante a implantação, pois o endereço IP de saída não está definido nesse ponto.

Primeiro, crie um cluster usando o comando [AZ AKs Create][az-aks-create] . O exemplo a seguir cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Criar gateway de saída para regras de firewall

Para garantir que os nós em um cluster possam se comunicar de forma confiável com o servidor de API quando você habilitar intervalos de IP autorizados na próxima seção, crie um firewall do Azure para uso como o gateway de saída. O endereço IP do firewall do Azure é então adicionado à lista de endereços IP do servidor de API autorizados na próxima seção.

> [!WARNING]
> O uso do firewall do Azure pode incorrer em custos significativos em um ciclo de cobrança mensal. O requisito para usar o Firewall do Azure só deve ser necessário neste período inicial de visualização. Para obter mais informações e planejamento de custos, consulte [preços do firewall do Azure][azure-firewall-costs].
>
> Como alternativa, se o cluster usar o [balanceador de carga SKU padrão][standard-sku-lb], você não precisará configurar o Firewall do Azure como o gateway de saída. Use [AZ Network Public-IP List][az-network-public-ip-list] e especifique o grupo de recursos do seu cluster AKs, que geralmente começa com *MC_* . Isso exibe o IP público para o cluster, que pode ser exibido na lista de permissões. Por exemplo:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

Primeiro, obtenha o nome do grupo de recursos *MC_* para o cluster AKs e a rede virtual. Em seguida, crie uma sub-rede usando o comando [AZ Network vnet subnet Create][az-network-vnet-subnet-create] . O exemplo a seguir cria uma sub-rede chamada *AzureFirewallSubnet* com o intervalo CIDR de *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Para criar um firewall do Azure, instale a extensão da CLI *do Azure-firewall* usando o comando [AZ Extension Add][az-extension-add] . Em seguida, crie um firewall usando o comando [AZ Network firewall Create][az-network-firewall-create] . O exemplo a seguir cria um firewall do Azure chamado *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Um firewall do Azure recebe um endereço IP público no qual o tráfego de saída flui. Crie um endereço público usando o comando [AZ Network Public-IP Create][az-network-public-ip-create] e, em seguida, crie uma configuração de IP no firewall usando o [AZ Network firewall IP-config Create][az-network-firewall-ip-config-create] que aplica o IP público:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Agora, crie a regra de rede do firewall do Azure para *permitir* todo o tráfego *TCP* usando o comando [AZ Network firewall Network-Rule Create][az-network-firewall-network-rule-create] . O exemplo a seguir cria uma regra de rede chamada *AllowTCPOutbound* para o tráfego com qualquer endereço de origem ou de destino:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Para associar o Firewall do Azure à rota de rede, obtenha as informações da tabela de rotas existente, o endereço IP interno do firewall do Azure e o endereço IP do servidor de API. Esses endereços IP são especificados na próxima seção para controlar como o tráfego deve ser roteado para comunicação de cluster.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Por fim, crie uma rota na tabela de rotas de rede AKS existente usando o comando [AZ Network Route-Table Route Create,][az-network-route-table-route-create] que permite que o tráfego use a comunicação do dispositivo de firewall do Azure para servidor de API.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Anote o endereço IP público do seu dispositivo de firewall do Azure. Esse endereço é adicionado à lista de intervalos de IP autorizados do servidor de API na próxima seção.

## <a name="enable-authorized-ip-ranges"></a>Habilitar intervalos de IP autorizados

Para habilitar intervalos de IP autorizados do servidor de API, forneça uma lista de intervalos de endereços IP autorizados. Quando você especificar um intervalo CIDR, comece com o primeiro endereço IP no intervalo. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP no intervalo, como *137.117.106.88/29*.

Use o comando [AZ AKs Update][az-aks-update] e especifique os *intervalos de--API-Server-Authorized-IP* para permitir. Esses intervalos de endereços IP geralmente são intervalos de endereços usados por suas redes locais. Adicione o endereço IP público do seu próprio firewall do Azure obtido na etapa anterior, como *20.42.25.196/32*.

O exemplo a seguir habilita os intervalos de IP autorizados do servidor de API no cluster chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group. Os intervalos de endereços IP a serem autorizados são *20.42.25.196/32* (o endereço IP público do firewall do Azure), em seguida, *172.0.0.0/16* e *168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Atualizar ou desabilitar intervalos de IP autorizados

Para atualizar ou desabilitar intervalos de IP autorizados, use novamente o comando [AZ AKs Update][az-aks-update] . Especifique o intervalo CIDR atualizado que você deseja permitir ou especifique um intervalo vazio para desabilitar os intervalos de IP autorizados do servidor de API, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você habilitou intervalos de IP autorizados do servidor de API. Essa abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicativos e clusters no AKs][concepts-security] e [as práticas recomendadas para segurança de cluster e atualizações no AKs][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md
