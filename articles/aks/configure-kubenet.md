---
title: Configurar kubenet funcionamento em rede no Azure Kubernetes Service (AKS)
description: Saiba como configurar a rede de kubenet (básico) no Azure Kubernetes Service (AKS) para implementar um cluster do AKS numa rede virtual existente e a sub-rede.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 7975516e80576a9d79b35da816d049f47ebc7d3a
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650194"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Utilizar kubenet networking com seus próprios intervalos de endereços IP no Azure Kubernetes Service (AKS)

Por predefinição, o AKS clusters uso [kubenet][kubenet], e uma rede virtual do Azure e uma sub-rede são criados por si. Com o *kubenet*, nós obtém um endereço IP da sub-rede da rede virtual do Azure. Pods recebem um endereço IP a partir de um espaço de endereço logicamente diferente para a sub-rede de rede virtual do Azure de nós. Tradução de endereços de rede (NAT), em seguida, está configurada para que os pods podem chegar a recursos na rede virtual do Azure. O endereço IP de origem do tráfego é que NAT para IP primária o nó o resolveria. Essa abordagem reduz significativamente o número de endereços IP que tem de reservar no seu espaço de rede para os pods utilizar.

Com o [Interface de rede contentor do Azure (CNI)][cni-networking], cada pod obtém um endereço IP da sub-rede e podem ser acedido diretamente. Estes endereços IP tem de ser exclusivos em seu espaço de rede e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods que suporta. O número equivalente de endereços IP por nó, em seguida, é reservado com antecedência para esse nó. Esta abordagem requer um planejamento mais e, muitas vezes conduzem à exaustão de endereço IP ou a necessidade de recriar clusters numa sub-rede maior à medida que aumentam as suas exigências de aplicativo.

Este artigo mostra-lhe como utilizar *kubenet* rede a fim de criar e utilizar uma sub-rede de rede virtual para um cluster do AKS. Para obter mais informações sobre as opções de rede e considerações, consulte [conceitos de rede para o Kubernetes e o AKS][aks-network-concepts].

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão 2.0.56 da CLI do Azure ou posterior instalado e configurado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar a CLI do Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Descrição geral do kubenet com sua própria sub-rede de rede

Em muitos ambientes, definiu as redes virtuais e sub-redes com intervalos de endereços IP alocados. Estes recursos de rede virtual são utilizados para oferecer suporte a vários serviços e aplicações. Para fornecer conectividade de rede, podem utilizar os clusters do AKS *kubenet* (rede básica) ou Azure CNI (*advanced networking*).

Com o *kubenet*, apenas os nós recebem um endereço IP na sub-rede da rede virtual. Pods não podem comunicar diretamente entre si. Em vez disso, o reencaminhamento de IP e definida pelo utilizador encaminhamento (UDR) é utilizado para conectividade entre pods em todos os nós. Também poderia implantar pods por trás de um serviço que recebe um endereço IP atribuído e o tráfego para a aplicação faz o balanceamento de carga. O diagrama seguinte mostra como os nós do AKS recebem um endereço IP na sub-rede da rede virtual, mas não os pods:

![Modelo de rede Kubenet com um cluster do AKS](media/use-kubenet/kubenet-overview.png)

O Azure suporta um máximo de 400 rotas num UDR, portanto, não pode ter um maior do que 400 nós do cluster do AKS. AKS funcionalidades como [nós virtuais] [ virtual-nodes] ou políticas de rede não são suportadas com *kubenet*.

Com o *Azure CNI*, cada pod recebe um endereço IP na sub-rede IP e podem comunicar diretamente com outros serviços e os pods. Os clusters podem ser tão grandes quanto o intervalo de endereços IP que especificar. No entanto, o intervalo de endereços IP têm de ser previsto com antecedência, e todos os endereços IP são consumidos pelos nós do AKS com base no número máximo de pods que eles podem suportar. Avançadas, tais como recursos de rede e cenários [nós virtuais] [ virtual-nodes] ou as políticas de rede são suportadas com *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilidade do endereço IP e esgotamento

Com o *Azure CNI*, um problema comum é o intervalo de endereços IP atribuído é demasiado pequeno para, em seguida, adicionar nós adicionais ao dimensionar ou atualizar um cluster. A equipe de rede também pode não ser capaz de emitir um intervalo de endereços IP grande o suficiente para suportar as suas exigências de aplicativo esperado.

Como um comprometimento, pode criar um cluster do AKS que utiliza *kubenet* e ligar a uma sub-rede de rede virtual existente. Esta abordagem permite que os nós receber endereços IP definidos, sem a necessidade de reservar um grande número de endereços IP com antecedência para todos os pods potenciais que poderiam ser executada no cluster.

Com o *kubenet*, pode utilizar um muito menor do intervalo de endereços IP e conseguir suportar grandes clusters e pedidos de aplicações. Por exemplo, até mesmo com um */27* intervalo de endereços IP, pode executar um cluster de nó de 20 a 25 com espaço suficiente para dimensionar ou atualizar. Este tamanho de cluster deve suportar até *2.200 2,750* pods (com um máximo de padrão de 110 pods por nó).

Os cálculos de básicos seguintes comparam a diferença nos modelos de rede:

- **kubenet** -um simples */24* intervalo de endereços IP pode suportar até *251* nós do cluster (a cada sub-rede da rede virtual do Azure reserva os primeiros três endereços IP para operações de gestão)
  - Esta contagem de nó pode suportar até *27,610* pods (com um máximo de padrão de 110 pods por nó com *kubenet*)
- **Azure CNI** -essa mesma basic */24* intervalo de sub-rede só podia dar suporte um máximo de *8* nós do cluster
  - Esta contagem de nó só pode suportar até *240* pods (com um máximo predefinido de 30 pods por nó com *Azure CNI*)

> [!NOTE]
> Estes valores máximos não levam em atualização de conta ou as operações de dimensionamento. Na prática, não é possível executar o número máximo de nós que suporta o intervalo de endereços IP de sub-rede. Tem de deixar alguns endereços IP disponíveis para utilização durante a escala de operações de atualização.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Peering de rede virtual e ligações do ExpressRoute

Para fornecer conectividade no local, ambos *kubenet* e *Azure CNI* podem utilizar as abordagens de rede [peering de rede virtual do Azure] [ vnet-peering]ou [as ligações ExpressRoute][express-route]. Planear os intervalos de endereços IP com cuidado para evitar a sobreposição e encaminhamento de tráfego incorreto. Por exemplo, muitas redes no local se utilizar um *10.0.0.0/8* intervalo que tenha sido anunciado através da ligação de ExpressRoute de endereços. É recomendado para criar os seus clusters do AKS em sub-redes da rede virtual do Azure fora deste intervalo de endereços, tal como *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Escolha um modelo de rede para utilizar

A escolha de qual Plug-in de rede a utilizar para o AKS cluster é normalmente um equilíbrio entre flexibilidade e necessidades de configuração avançada. As seguintes considerações de ajudar a estrutura de tópicos quando cada modelo de rede pode ser o mais adequado.

Uso *kubenet* quando:

- Tem a limitava o espaço de endereços IP.
- A maioria da comunicação pod está dentro do cluster.
- Não precisa de recursos avançados como nós virtuais ou política de rede.

Uso *Azure CNI* quando:

- Tem o espaço de endereços IP disponível.
- A maioria da comunicação pod é recursos fora do cluster.
- Não pretende gerir as UDRs.
- Terá funcionalidades avançadas, como nós virtuais ou política de rede.

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

Para começar a utilizar com o uso *kubenet* e a sua própria sub-rede da rede virtual, crie primeiro um grupo de recursos utilizando o [criar grupo az] [ az-group-create] comando. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se não tiver uma rede virtual existente e a sub-rede a utilizar, criar estes recursos de rede com o [criar a vnet de rede de az] [ az-network-vnet-create] comando. No exemplo seguinte, a rede virtual é denominada *myVnet* com o prefixo de endereço *10.0.0.0/8*. Uma sub-rede é criada com o nome *myAKSSubnet* com o prefixo de endereço *10.240.0.0/16*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Criar um principal de serviço e atribuir permissões

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. O principal de serviço tem de ter permissões para gerir a rede virtual e a sub-rede que utilizam os nós do AKS. Para criar um principal de serviço, utilize o [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] comando:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

O resultado de exemplo seguinte mostra o ID da aplicação e a palavra-passe para o seu principal de serviço. Estes valores são utilizados nos passos adicionais para atribuir uma função ao principal de serviço e, em seguida, criar o cluster do AKS:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Para atribuir as delegações corretas nos restantes passos, utilize o [show de vnet de rede de az] [ az-network-vnet-show] e [show de sub-rede de vnet de rede de az] [ az-network-vnet-subnet-show] comandos para obter os IDs dos recursos necessários. Estes IDs de recurso são armazenadas como variáveis e referenciados nas etapas restantes:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Agora, atribua o principal de serviço para o seu cluster do AKS *contribuinte* permissões na rede virtual com o [criação da atribuição de função de az] [ az-role-assignment-create] comando. Fornecer seu próprio  *\<appId >* conforme mostrado no resultado do comando anterior para criar o principal de serviço:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster do AKS na rede virtual

Acabou de criar uma rede virtual e uma sub-rede e criou e receber permissões para um principal de serviço para usar esses recursos de rede. Agora, criar um cluster do AKS na sua rede virtual e sub-rede a utilizar o [criar az aks] [ az-aks-create] comando. Definir o seu principal de serviço  *\<appId >* e  *\<palavra-passe >*, como mostra a saída do comando anterior para criar o principal de serviço.

Os seguintes intervalos de endereços IP também são definidos como parte do cluster Criar processo:

* O *– serviço cidr* é usado para atribuir um endereço IP de serviços internos do cluster do AKS. Este intervalo de endereços IP deve ser um espaço de endereços que não está em utilização em outro lugar no seu ambiente de rede. Isto inclui quaisquer intervalos de rede no local se ligar, ou se planear ligar redes virtuais do Azure com o Expressroute ou ligações de VPN de Site a Site.

* O *-- dns-ip do serviço* endereço deve ser o *.10* endereço do seu intervalo de endereços IP do serviço.

* O *– pod cidr* deve ser um espaço de endereçamento amplo que não está em utilização em outro lugar no seu ambiente de rede. Isto inclui quaisquer intervalos de rede no local se ligar, ou se planear ligar redes virtuais do Azure com o Express Route ou uma ligação VPN de Site a Site.
    * Este intervalo de endereços tem de ser suficientemente grande para acomodar o número de nós que pretende dimensionar até. Não é possível alterar este intervalo de endereços assim que o cluster é implementado, se precisar de mais endereços para outros nós.
    * O intervalo de endereços IP de pod é usado para atribuir um */24* espaço para cada nó do cluster de endereços. No exemplo a seguir, o *– pod cidr* dos *192.168.0.0/16* atribui o primeiro nó *192.168.0.0/24*, o segundo nó *192.168.1.0/24*e o nó de terceiro *192.168.2.0/24*.
    * Como as escalas de cluster ou atualizações, a plataforma do Azure continua atribuir um intervalo de endereços IP de pod para cada novo nó.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

## <a name="associate-network-resources-with-the-node-subnet"></a>Associar recursos de rede com a sub-rede de nó

Quando cria um cluster do AKS, uma tabela de grupo e a rota da segurança de rede são criados. Estes recursos de rede são geridos pelo plano de controlo do AKS e atualizados quando criar e expor os serviços. Associe a tabela de grupo e a rota da segurança de rede à sub-rede de rede virtual da seguinte forma:

```azurecli-interactive
# Get the MC_ resource group for the AKS cluster resources
MC_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)

# Get the route table for the cluster
ROUTE_TABLE=$(az network route-table list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Get the network security group
NODE_NSG=$(az network nsg list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Update the subnet to associate the route table and network security group
az network vnet subnet update \
    --route-table $ROUTE_TABLE \
    --network-security-group $NODE_NSG \
    --ids $SUBNET_ID
```

## <a name="next-steps"></a>Passos Seguintes

Com um cluster do AKS implementado na sua sub-rede de rede virtual existente, agora, pode utilizar o cluster como habitualmente. Introdução ao [criar aplicações com o Azure Dev espaços] [ dev-spaces] ou [com o Draft][use-draft], ou [implementar aplicações com Helm] [use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
