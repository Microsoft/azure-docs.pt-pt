---
title: Servidor de API autorizado intervalos IP no Azure Kubernetes Service (AKS)
description: Saiba como proteger o seu cluster com um intervalo de endereços IP para acesso ao servidor de API no Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 6516bbcb4ea879279812d61d9fe31f1ea4268280
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616243"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Pré-visualização - proteger o acesso para o servidor de API utilizando autorizado intervalos de endereços IP no Azure Kubernetes Service (AKS)

No Kubernetes, o servidor de API recebe pedidos de execução de ações do cluster, tais como criar recursos ou dimensionar o número de nós. O servidor de API é a forma centralizada de interagir e gerir um cluster. Para melhorar a segurança do cluster e minimizar a ataques, o servidor de API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra-lhe como utilizar intervalos de endereços IP de servidor autorizado de API para limitar os pedidos para o plano de controlo. Esta funcionalidade encontra-se em pré-visualização.

> [!IMPORTANT]
> Funcionalidades de pré-visualização do AKS são self-service, participar. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Em pré-visualização, esses recursos não se destinam a utilização de produção. Funcionalidades em pré-visualização pública enquadram-se em suporte "melhor esforço". Assistência das equipas de suporte técnico do AKS está disponível durante o horário do Pacífico fuso horário (PST) apenas. Para obter mais informações, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [FAQ de suporte do Azure][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Servidor de API funciona apenas para novos clusters do AKS que criar intervalos de IP autorizado. Este artigo mostra-lhe como criar um cluster do AKS com a CLI do Azure.

Precisa da versão 2.0.61 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão CLI de pré-visualização do aks

Para configurar intervalos IP do servidor autorizado de API, precisa da *pré-visualização do aks* CLI versão da extensão 0.4.1 ou superior. Instalar o *pré-visualização do aks* extensão da CLI do Azure com o [Adicionar extensão az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Registre-se o sinalizador de funcionalidade para a sua subscrição

Para utilizar intervalos IP do servidor autorizado de API, ative primeiro um sinalizador de funcionalidade na sua subscrição. Para registar o *APIServerSecurityPreview* sinalizador de funcionalidade, utilize o [Registre-se de funcionalidade de az][az-feature-register] comando conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando registra um recurso numa assinatura, não pode atualmente anular o registo essa funcionalidade. Depois de ativar a algumas funcionalidades de pré-visualização, as predefinições podem ser utilizadas para todos os clusters do AKS, em seguida, criados na subscrição. Não a ativar funcionalidades de pré-visualização em subscrições de produção. Utilize uma subscrição separada para testar as funcionalidades de pré-visualização e colher comentários baseados em.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para que o estado a mostrar *registado*. Pode verificar o estado de registo com o [lista de funcionalidades de az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registo do *containerservice* fornecedor de recursos com o [Registre-se fornecedor de az][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se ao configurar intervalos IP do servidor autorizado de API:

* Atualmente não é possível utilizar espaços de desenvolvimento do Azure como a comunicação com o servidor de API também é bloqueada.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Descrição geral do servidor de API autorizado intervalos de IP

O servidor de API do Kubernetes é a forma como as APIs de Kubernetes subjacente são expostos. Esse componente fornece a interação das ferramentas de gestão, tais como `kubectl` ou o dashboard do Kubernetes. AKS fornece um modelo de cluster de inquilino único, com um servidor dedicado de API. Por predefinição, o servidor de API é atribuído um endereço IP público e deve controlar o acesso ao utilizar controlos de acesso baseado em funções (RBAC).

Para proteger o acesso para o plano de controlo do AKS caso contrário publicamente acessível / servidor de API, pode ativar e utilizar autorizado intervalos de IP. Estes intervalos IP autorizados apenas permitir que os intervalos de endereços IP definidos comunicar com o servidor de API. Um pedido efetuado para o servidor de API a partir de um endereço IP que não faz parte destes intervalos IP autorizados está bloqueado. Deve continuar a utilizar o RBAC para, em seguida, autorizar utilizadores e as ações que solicitarem.

Para obter mais informações sobre o servidor de API e outros componentes de cluster, consulte [Kubernetes principais conceitos para o AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Criar um cluster do AKS (Create an AKS cluster)

Os intervalos de IP de servidor autorizado de API só funcionam para novos clusters do AKS. Não é possível ativar os intervalos de IP autorizados, como parte do cluster de criação. Se tentar ativar o processo de criar intervalos IP autorizados como parte do cluster, os nós do cluster não conseguem aceder ao servidor de API durante a implementação, como o endereço de IP de saída não está definido neste ponto.

Primeiro, crie um cluster com o [az aks criar][az-aks-create] comando. O exemplo seguinte cria um cluster de nó único com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*.

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

Para garantir que os nós num cluster de forma fiável consegue comunicar com o servidor de API quando ativa a intervalos IP autorizados na próxima seção, crie uma firewall do Azure para utilização como o gateway de saída. O endereço IP do firewall do Azure, em seguida, é adicionado à lista de autorizados endereços IP do servidor de API na próxima seção.

> [!WARNING]
> A utilização de Firewall do Azure pode implicar custos significativos ao longo de ciclos de faturação mensal. O requisito para usar o Firewall do Azure só é necessário neste período de pré-visualização inicial. Para obter mais informações e planejamento de custo, veja [preços de Firewall do Azure][azure-firewall-costs].

Primeiro, obtenha os *MC_* nome do grupo de recursos para o cluster do AKS e a rede virtual. Em seguida, crie uma sub-rede com o [criar a sub-rede de vnet de rede de az][az-network-vnet-subnet-create] comando. O exemplo seguinte cria uma sub-rede denominada *AzureFirewallSubnet* com o intervalo CIDR de *10.200.0.0/16*:

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

Para criar uma Firewall do Azure, instalar o *firewall do azure* extensão da CLI com o [Adicionar extensão az][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] comando. O exemplo seguinte cria uma firewall do Azure com o nome *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Uma firewall do Azure é atribuído um endereço IP público que o tráfego de saída flui através de. Criar um endereço público utilizando a [criar a rede de az public-ip][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] que aplica-se o IP público:

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

Agora, crie a regra de rede de firewall do Azure para *permitir* todos os *TCP* tráfego utilizando o [criar a rede de firewall de rede de az-rule][az-network-firewall-network-rule-create] comando. O exemplo seguinte cria uma regra de rede com o nome *AllowTCPOutbound* para o tráfego com qualquer endereço de origem ou de destino:

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

Para associar o firewall do Azure a rota de rede, obter as informações de tabela de rota existente, o endereço IP interno do firewall do Azure e, em seguida, o endereço IP do servidor de API. Estes endereços IP são especificados na secção seguinte para controlar como o tráfego deve ser encaminhado para a comunicação de cluster.

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

Por fim, crie uma rota no existente AKS rede rota tabela com o [criar rota de tabela de rotas de rede de az][az-network-route-table-route-create] comando que permita o tráfego para utilizar a aplicação de firewall do Azure para comunicações de servidor de API.

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

Tome nota do endereço IP público da sua aplicação de Firewall do Azure. Este endereço é adicionado à lista de intervalos de IP do servidor autorizado de API na próxima seção.

## <a name="enable-authorized-ip-ranges"></a>Ativar a intervalos IP autorizados

Para ativar a intervalos IP do servidor autorizado de API, fornecer uma lista de intervalos de endereços IP autorizados. Quando especificar um intervalo CIDR, comece com o primeiro endereço IP no intervalo. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP no intervalo, tal como *137.117.106.88/29*.

Uso [atualizar az aks][az-aks-update] comando e especifique a *- api-server-autorizado--os intervalos de ip* para permitir. Estes intervalos de endereços IP são, normalmente, intervalos de endereços utilizados por suas redes no local. Adicionar o endereço IP público do seu próprio firewall do Azure, obtido no passo anterior, como *20.42.25.196/32*.

O exemplo seguinte ativa os intervalos IP do servidor autorizado API no cluster com o nome *myAKSCluster* no grupo de recursos com o nome *myResourceGroup*. São os intervalos de endereços IP para autorizar *20.42.25.196/32* (o firewall do Azure endereço IP público), em seguida, *172.0.0.10/16* e *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Atualizar ou desativar os intervalos de IP autorizados

Para atualizar ou desativar os intervalos de IP autorizados, terá de utilizar novamente [atualizar az aks][az-aks-update] comando. Especifique o intervalo de CIDR atualizado que pretende permitir ou especificar um intervalo vazio para desativar o servidor de API autorizado intervalos IP, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, ativou a intervalos IP do servidor autorizado de API. Essa abordagem é uma parte da forma como pode executar um cluster do AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicações e clusters no AKS][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
