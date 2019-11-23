---
title: Configurar a rede kubenet no serviço kubernetes do Azure (AKS)
description: Saiba como configurar a rede kubenet (básica) no AKS (serviço kubernetes do Azure) para implantar um cluster AKS em uma rede virtual e sub-rede existentes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/26/2019
ms.author: mlearned
ms.reviewer: nieberts, jomore
ms.openlocfilehash: b233c5dd639bb6652f201727748a081f6a8a4c64
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950340"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Usar a rede kubenet com seus próprios intervalos de endereços IP no serviço de kubernetes do Azure (AKS)

Por padrão, os clusters AKS usam [kubenet][kubenet]e uma rede virtual do Azure e uma sub-rede são criadas para você. Com o *kubenet*, os nós obtêm um endereço IP da sub-rede da rede virtual do Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente para a sub-rede da rede virtual do Azure dos nós. A conversão de endereços de rede (NAT) é então configurada para que o pods possa alcançar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é NAT para o endereço IP primário do nó. Essa abordagem reduz consideravelmente o número de endereços IP que você precisa reservar em seu espaço de rede para que os pods usem.

Com a [CNI (interface de rede de contêiner do Azure)][cni-networking], cada pod Obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o espaço de rede e devem ser planejados antecipadamente. Cada nó tem um parâmetro de configuração para o número máximo de pods que oferece suporte. O número equivalente de endereços IP por nó é então reservado antecipadamente para esse nó. Essa abordagem requer mais planejamento e, muitas vezes, leva a esgotamento de endereço IP ou a necessidade de recriar clusters em uma sub-rede maior conforme as demandas do seu aplicativo crescem.

Este artigo mostra como usar a rede *kubenet* para criar e usar uma sub-rede de rede virtual para um cluster AKs. Para obter mais informações sobre opções e considerações de rede, consulte [conceitos de rede para kubernetes e AKs][aks-network-concepts].

> [!WARNING]
> Para usar pools de nós do Windows Server (atualmente em visualização no AKS), você deve usar o CNI do Azure. O uso de kubenet como o modelo de rede não está disponível para contêineres do Windows Server.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.65 ou posterior instalado e configurado. Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Visão geral da rede kubenet com sua própria sub-rede

Em muitos ambientes, você definiu redes virtuais e sub-redes com intervalos de endereços IP alocados. Esses recursos de rede virtual são usados para dar suporte a vários serviços e aplicativos. Para fornecer conectividade de rede, os clusters AKS podem usar o *kubenet* (rede básica) ou o Azure CNI (*rede avançada*).

Com o *kubenet*, somente os nós recebem um endereço IP na sub-rede da rede virtual. Os pods não podem se comunicar diretamente entre si. Em vez disso, o roteamento definido pelo usuário (UDR) e o encaminhamento de IP são usados para conectividade entre os pods entre os nós. Você também pode implantar pods atrás de um serviço que recebe um endereço IP atribuído e o tráfego de balanceamento de carga para o aplicativo. O diagrama a seguir mostra como os nós AKS recebem um endereço IP na sub-rede da rede virtual, mas não os pods:

![Modelo de rede Kubenet com um cluster AKS](media/use-kubenet/kubenet-overview.png)

O Azure dá suporte a um máximo de 400 rotas em um UDR, portanto, você não pode ter um cluster AKS com mais de 400 nós. Não há suporte para [nós virtuais][virtual-nodes] AKs e políticas de rede do Azure com *kubenet*.  Você pode usar [as políticas de rede do Calico][calico-network-policies], pois elas têm suporte com o kubenet.

Com o *Azure CNI*, cada pod recebe um endereço IP na sub-rede IP e pode se comunicar diretamente com outros pods e serviços. Os clusters podem ser tão grandes quanto o intervalo de endereços IP que você especificar. No entanto, o intervalo de endereços IP deve ser planejado antecipadamente e todos os endereços IP são consumidos pelos nós AKS com base no número máximo de pods para os quais eles podem dar suporte. Recursos e cenários de rede avançados, como [nós virtuais][virtual-nodes] ou políticas de rede (Azure ou Calico), têm suporte com o *CNI do Azure*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilidade e esgotamento do endereço IP

Com o *Azure CNI*, um problema comum é que o intervalo de endereços IP atribuído é muito pequeno para, em seguida, adicionar mais nós ao dimensionar ou atualizar um cluster. A equipe de rede também pode não ser capaz de emitir um grande intervalo de endereços IP suficiente para dar suporte às demandas de aplicativos esperadas.

Como um comprometimento, você pode criar um cluster AKS que usa o *kubenet* e conectar-se a uma sub-rede de rede virtual existente. Essa abordagem permite que os nós recebam endereços IP definidos, sem a necessidade de reservar um grande número de endereços IP antecipadamente para todo o pods potencial que poderia ser executado no cluster.

Com o *kubenet*, você pode usar um intervalo de endereços IP muito menor e ser capaz de dar suporte a clusters grandes e demandas de aplicativos. Por exemplo, mesmo com um intervalo de endereços IP */27* , você pode executar um cluster de nó de 20-25 com espaço suficiente para dimensionar ou atualizar. Esse tamanho de cluster dá suporte a até *2200 2750* pods (com um máximo padrão de 110 pods por nó). O número máximo de pods por nó que você pode configurar com *kubenet* em AKS é 110.

Os seguintes cálculos básicos comparam a diferença nos modelos de rede:

- **kubenet** -um intervalo de endereços IP simples */24* pode dar suporte a até *251* nós no cluster (cada sub-rede da rede virtual do Azure reserva os três primeiros endereços IP para operações de gerenciamento)
  - Esta contagem de nós pode dar suporte a até *27.610* pods (com um máximo padrão de 110 pods por nó com *kubenet*)
- **Azure CNI** -esse mesmo intervalo de sub-rede básico */24* poderia dar suporte apenas a um máximo de *8* nós no cluster
  - Essa contagem de nós só pode dar suporte a até *240* pods (com um máximo padrão de 30 pods por nó com o *Azure CNI*)

> [!NOTE]
> Esses máximos não levam em conta as operações de atualização ou de escala. Na prática, você não pode executar o número máximo de nós aos quais o intervalo de endereços IP de sub-rede dá suporte. Você deve deixar alguns endereços IP disponíveis para uso durante a escala de operações de atualização.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Conexões de emparelhamento de rede virtual e ExpressRoute

Para fornecer conectividade local, as abordagens de rede *kubenet* e *Azure-CNI* podem usar o [emparelhamento de rede virtual do Azure][vnet-peering] ou [as conexões de ExpressRoute][express-route]. Planeje seus intervalos de endereços IP com cuidado para evitar sobreposição e roteamento de tráfego incorreto. Por exemplo, muitas redes locais usam um intervalo de endereços *10.0.0.0/8* que é anunciado na conexão do ExpressRoute. É recomendável criar seus clusters do AKS em sub-redes da rede virtual do Azure fora desse intervalo de endereços, como *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Escolha um modelo de rede a ser usado

A escolha de qual plug-in de rede usar para o cluster AKS é geralmente um equilíbrio entre a flexibilidade e as necessidades de configuração avançadas. As considerações a seguir ajudam a descrever quando cada modelo de rede pode ser o mais apropriado.

Use *kubenet* quando:

- Você tem espaço de endereço IP limitado.
- A maior parte da comunicação Pod está dentro do cluster.
- Você não precisa de recursos avançados do AKS, como nós virtuais ou política de rede do Azure.  Use [as políticas de rede do Calico][calico-network-policies].

Use o *CNI do Azure* quando:

- Você tem espaço de endereço IP disponível.
- A maior parte da comunicação pod é para os recursos fora do cluster.
- Você não deseja gerenciar o UDRs.
- Você precisa de recursos avançados do AKS, como nós virtuais ou política de rede do Azure.  Use [as políticas de rede do Calico][calico-network-policies].

Para obter mais informações para ajudá-lo a decidir qual modelo de rede usar, consulte [comparar modelos de rede e seu escopo de suporte][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

Para começar a usar o *kubenet* e sua própria sub-rede de rede virtual, primeiro crie um grupo de recursos usando o comando [AZ Group Create][az-group-create] . O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se você não tiver uma rede virtual e uma sub-rede existentes para usar, crie esses recursos de rede usando o comando [AZ Network vnet Create][az-network-vnet-create] . No exemplo a seguir, a rede virtual é nomeada *myVnet* com o prefixo de endereço de *192.168.0.0/16*. Uma sub-rede é criada chamada *myAKSSubnet* com o prefixo de endereço *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Criar uma entidade de serviço e atribuir permissões

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. A entidade de serviço precisa ter permissões para gerenciar a rede virtual e a sub-rede que os nós AKS usam. Para criar uma entidade de serviço, use o comando [AZ ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] :

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída de exemplo a seguir mostra a ID do aplicativo e a senha para sua entidade de serviço. Esses valores são usados em etapas adicionais para atribuir uma função à entidade de serviço e, em seguida, criar o cluster AKS:

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

Para atribuir as delegações corretas nas etapas restantes, use os comandos [AZ Network vnet show][az-network-vnet-show] e [AZ Network vnet subnet show][az-network-vnet-subnet-show] para obter as IDs de recurso necessárias. Essas IDs de recurso são armazenadas como variáveis e referenciadas nas etapas restantes:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Agora, atribua a entidade de serviço para as permissões de *colaborador* do cluster AKs na rede virtual usando o comando [AZ role Assignment Create][az-role-assignment-create] . Forneça seu próprio *\<appId >* conforme mostrado na saída do comando anterior para criar a entidade de serviço:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster AKS na rede virtual

Agora você criou uma rede virtual e uma sub-rede, e criou e atribuiu permissões para uma entidade de serviço usar esses recursos de rede. Agora, crie um cluster AKS em sua rede virtual e sub-rede usando o comando [AZ AKs Create][az-aks-create] . Defina sua própria entidade de serviço *\<appId >* e *\<senha >* , conforme mostrado na saída do comando anterior para criar a entidade de serviço.

Os seguintes intervalos de endereços IP também são definidos como parte do processo de criação do cluster:

* O *--Service-CIDR* é usado para atribuir serviços internos no cluster AKs um endereço IP. Esse intervalo de endereços IP deve ser um espaço de endereço que não esteja em uso em outro lugar em seu ambiente de rede. Esse intervalo inclui todos os intervalos de rede local se você se conectar ou se pretende conectar-se a suas redes virtuais do Azure usando a rota expressa ou uma conexão VPN site a site.

* O endereço *--DNS-Service-IP* deve ser o endereço *.10* do intervalo de endereços IP do seu serviço.

* O *--Pod-CIDR* deve ser um espaço de endereço grande que não está em uso em outro lugar em seu ambiente de rede. Esse intervalo inclui todos os intervalos de rede local se você se conectar ou se pretende conectar-se a suas redes virtuais do Azure usando a rota expressa ou uma conexão VPN site a site.
    * Esse intervalo de endereços deve ser grande o suficiente para acomodar o número de nós que você espera escalar verticalmente. Você não poderá alterar esse intervalo de endereços depois que o cluster for implantado se precisar de mais endereços para nós adicionais.
    * O intervalo de endereços IP de Pod é usado para atribuir um espaço de endereço */24* a cada nó no cluster. No exemplo a seguir, o *--Pod-CIDR* de *10.244.0.0/16* atribui o primeiro nó *10.244.0.0/24*, o segundo nó *10.244.1.0/24*e o terceiro nó *10.244.2.0/24*.
    * À medida que o cluster é dimensionado ou atualizado, a plataforma do Azure continua a atribuir um intervalo de endereços IP de Pod a cada novo nó.
    
* O *--Docker-Bridge-address* permite que os nós AKs se comuniquem com a plataforma de gerenciamento subjacente. Esse endereço IP não deve estar dentro do intervalo de endereços IP da rede virtual do cluster e não deve se sobrepor a outros intervalos de endereços em uso na sua rede.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Se você quiser habilitar um cluster AKS para incluir uma [política de rede Calico][calico-network-policies] , poderá usar o comando a seguir.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Quando você cria um cluster AKS, um grupo de segurança de rede e uma tabela de rotas são criados. Esses recursos de rede são gerenciados pelo plano de controle AKS. O grupo de segurança de rede é associado automaticamente às NICs virtuais em seus nós. A tabela de rotas é associada automaticamente à sub-rede da rede virtual. Regras de grupo de segurança de rede e tabelas de rotas e são atualizadas automaticamente conforme você cria e expõe serviços.

## <a name="next-steps"></a>Passos seguintes

Com um cluster AKS implantado em sua sub-rede de rede virtual existente, agora você pode usar o cluster normalmente. Introdução à [criação de aplicativos usando o Azure dev Spaces][dev-spaces] ou o uso de [rascunho][use-draft]ou a [implantação de aplicativos usando o Helm][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

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
[network-comparisons]: concepts-network.md#compare-network-models
