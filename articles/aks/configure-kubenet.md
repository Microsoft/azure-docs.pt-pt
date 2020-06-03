---
title: Configurar a rede kubenet no Serviço Azure Kubernetes (AKS)
description: Aprenda a configurar a rede kubenet (básica) no Serviço Azure Kubernetes (AKS) para implantar um cluster AKS numa rede virtual e numa sub-rede existentes.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: a393e87963eabf2e3cf41148233c0e350dc6e380
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309673"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Utilize a rede kubenet com as suas próprias gamas de endereços IP no Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS utilizam [kubenet][kubenet], e uma rede virtual Azure e sub-rede são criadas para si. Com *kubenet,* os nós obtêm um endereço IP da sub-rede virtual Azure. Os pods recebem um endereço IP de um espaço de endereços logicamente diferente para a sub-rede da rede virtual do Azure dos nós. A tradução de endereços de rede (NAT) é configurada para que os pods possam chegar aos recursos na rede virtual do Azure. O endereço IP de origem do tráfego é NAT'd para o endereço IP primário do nó. Esta abordagem reduz consideravelmente o número de endereços IP que precisa de reservar no espaço da sua rede para as cápsulas utilizarem.

Com [a Interface de Rede de Contentores Azure (CNI),][cni-networking]cada pod obtém um endereço IP a partir da sub-rede e pode ser acedido diretamente. Estes endereços IP devem ser únicos em todo o seu espaço de rede, e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de cápsulas que suporta. O número equivalente de endereços IP por nó é então reservado à frente para esse nó. Esta abordagem requer mais planeamento, e muitas vezes leva à exaustão do endereço IP ou à necessidade de reconstruir clusters numa sub-rede maior à medida que as suas exigências de aplicação aumentam. Pode configurar as cápsulas máximas desdobráveis para um nó no cluster criar tempo ou ao criar novas piscinas de nó. Se não especificar maxPods ao criar novos conjuntos de nós, receberá um valor padrão de 110 para kubenet.

Este artigo mostra-lhe como usar a rede *kubenet* para criar e utilizar uma sub-rede de rede virtual para um cluster AKS. Para obter mais informações sobre opções e considerações de rede, consulte [conceitos de rede para Kubernetes e AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual para o cluster AKS deve permitir a conectividade de saída da Internet.
* Não crie mais do que um cluster AKS na mesma sub-rede.
* Os clusters AKS não podem utilizar , , ou para a gama de `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` endereços de serviço Kubernetes.
* O principal de serviço utilizado pelo cluster AKS deve ter pelo menos [função de Contribuinte de Rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede dentro da sua rede virtual. Se desejar definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de utilizar a função de contribuinte de rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Para utilizar as piscinas de nó do Windows Server, tem de utilizar o Azure CNI. A utilização do kubenet como modelo de rede não está disponível para recipientes windows Server.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.65 ou posteriormente instalada e configurada. Corre  `az --version` para encontrar a versão. Se necessitar de instalar ou atualizar, consulte [instalar o Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Visão geral da rede kubenet com a sua própria sub-rede

Em muitos ambientes, definiu redes virtuais e sub-redes com intervalos de endereço IP atribuídos. Estes recursos de rede virtual são usados para suportar vários serviços e aplicações. Para fornecer conectividade de rede, os clusters AKS podem usar *kubenet* (rede básica) ou Azure CNI *(networking avançado).*

Com *kubenet,* apenas os nós recebem um endereço IP na sub-rede de rede virtual. As cápsulas não se comunicam diretamente umas com as outras. Em vez disso, o encaminhamento definido do utilizador (UDR) e o encaminhamento IP é utilizado para a conectividade entre cápsulas entre nós. Também pode implantar cápsulas atrás de um serviço que recebe um endereço IP atribuído e equilibra o tráfego para a aplicação. O diagrama que se segue mostra como os nóns AKS recebem um endereço IP na sub-rede de rede virtual, mas não nas cápsulas:

![Modelo de rede Kubenet com um cluster AKS](media/use-kubenet/kubenet-overview.png)

O Azure suporta um máximo de 400 rotas numa UDR, por isso não pode ter um cluster AKS maior que 400 nós. Os [nós virtuais][virtual-nodes] AKS e as políticas de rede Azure não são suportados com *kubenet*.  Pode utilizar [as Políticas de Rede Calico,][calico-network-policies]uma vez que são suportadas com kubenet.

Com *o Azure CNI,* cada pod recebe um endereço IP na sub-rede IP, e pode comunicar diretamente com outras cápsulas e serviços. Os seus clusters podem ser tão grandes quanto o intervalo de endereços IP especificado. No entanto, o intervalo de endereços IP deve ser planeado com antecedência, e todos os endereços IP são consumidos pelos nós AKS com base no número máximo de cápsulas que podem suportar. Funcionalidades e cenários avançados de rede como [Os Nós Virtuais][virtual-nodes] ou Políticas de Rede (ou Azure ou Calico) são suportados com *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilidade e exaustão do endereço IP

Com *o Azure CNI*, uma questão comum é que a gama de endereços IP atribuída é demasiado pequena para, em seguida, adicionar nós adicionais quando escala ou atualiza um cluster. A equipa de rede também pode não ser capaz de emitir um intervalo de endereços IP suficientemente grande para suportar as suas exigências de aplicação esperadas.

Como compromisso, pode criar um cluster AKS que utiliza *kubenet* e se conecta a uma sub-rede de rede virtual existente. Esta abordagem permite que os nós recebam endereços IP definidos, sem a necessidade de reservar um grande número de endereços IP na frente para todas as cápsulas potenciais que poderiam funcionar no cluster.

Com *o kubenet,* você pode usar uma gama de endereços IP muito menor e ser capaz de suportar grandes clusters e pedidos de aplicação. Por exemplo, mesmo com uma gama de endereços IP */27,* você pode executar um cluster de nó 20-25 com espaço suficiente para escalar ou atualizar. Este tamanho de cluster suportaria até *2.200-2.750* cápsulas (com um máximo de 110 cápsulas por nó). O número máximo de cápsulas por nó que pode configurar com *kubenet* em AKS é de 110.

Os seguintes cálculos básicos comparam a diferença nos modelos de rede:

- **kubenet** - uma gama simples de endereços IP */24* pode suportar até *251* nós no cluster (cada sub-rede de rede virtual Azure reserva os três primeiros endereços IP para operações de gestão)
  - Esta contagem de nópode suportar até *27.610* cápsulas (com um máximo de 110 cápsulas por nó com *kubenet)*
- **Azure CNI** - que a mesma gama de sub-redes básica */24* só podia suportar um máximo de *8* nós no cluster
  - Esta contagem de nópoderia apenas suportar até *240* cápsulas (com um máximo de 30 cápsulas por nó com *Azure CNI)*

> [!NOTE]
> Estes máximos não têm em conta as operações de upgrade ou escala. Na prática, não é possível executar o número máximo de nós que o intervalo de endereço IP da sub-rede suporta. Deve deixar alguns endereços IP disponíveis para utilização durante a escala de operações de atualização.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Monitorização de rede virtual e conexões ExpressRoute

Para fornecer conectividade no local, tanto as abordagens de rede *kubenet* como *Azure-CNI* podem utilizar ligações [de rede virtual Azure][vnet-peering] ou [ExpressRoute][express-route]. Planeie cuidadosamente os seus intervalos de endereço IP para evitar sobreposições e encaminhamento de tráfego incorreto. Por exemplo, muitas redes no local usam uma gama de endereços *10.0.0.0/8* que é publicitada sobre a ligação ExpressRoute. É recomendado criar os seus clusters AKS em sub-redes de rede virtuais Azure fora desta gama de endereços, tais como *172.16.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Escolha um modelo de rede para usar

A escolha de qual plugin de rede utilizar para o seu cluster AKS é geralmente um equilíbrio entre a flexibilidade e as necessidades avançadas de configuração. As seguintes considerações ajudam a delinear quando cada modelo de rede pode ser o mais adequado.

Use *kubenet* quando:

- Tem um espaço limitado de endereço IP.
- A maior parte da comunicação da cápsula está dentro do aglomerado.
- Não precisa de funcionalidades avançadas da AKS, como nós virtuais ou Azure Network Policy.  Utilize [as políticas de rede Calico][calico-network-policies].

Utilize *o CNI Azure* quando:

- Tem espaço disponível para endereço IP.
- A maior parte da comunicação da cápsula é para recursos fora do cluster.
- Não queres gerir as UDRs.
- Precisa de funcionalidades avançadas da AKS, como nós virtuais ou Política de Rede Azure.  Utilize [as políticas de rede Calico][calico-network-policies].

Para obter mais informações para ajudá-lo a decidir qual o modelo de rede a utilizar, consulte [os modelos de rede Compare e o seu âmbito de suporte.][network-comparisons]

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

Para começar a usar *kubenet* e a sua própria sub-rede de rede virtual, primeiro crie um grupo de recursos usando o [comando de criação do grupo AZ.][az-group-create] O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se não tiver uma rede virtual existente e uma sub-rede para utilizar, crie estes recursos de rede utilizando o [comando de rede az.][az-network-vnet-create] No exemplo seguinte, a rede virtual é nomeada *myVnet* com o prefixo de endereço de *192.168.0.0/16*. Uma sub-rede é criada chamada *myAKSSubnet* com o prefixo de endereço *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Criar um principal de serviço e atribuir permissões

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. O responsável pelo serviço necessita de permissões para gerir a rede virtual e a sub-rede que os nós AKS utilizam. Para criar um principal de serviço, utilize o comando [ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída de exemplo a seguir mostra o ID da aplicação e a palavra-passe para o seu principal serviço. Estes valores são utilizados em etapas adicionais para atribuir uma função ao principal de serviço e, em seguida, criar o cluster AKS:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Para atribuir as delegações corretas nas restantes etapas, utilize o [show vnet de rede az][az-network-vnet-show] e os comandos [de exibição de sub-rede de rede az][az-network-vnet-subnet-show] para obter os IDs de recursos necessários. Estes IDs de recursos são armazenados como variáveis e referenciados nos passos restantes:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Agora atribua o principal de serviço para as permissões *de contribuinte de* cluster AKS na rede virtual utilizando o comando de [criação de função az.][az-role-assignment-create] Forneça o seu próprio *\<appId>* como mostrado na saída do comando anterior para criar o principal de serviço:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster AKS na rede virtual

Criou agora uma rede virtual e uma sub-rede, e criou e atribuiu permissões para um diretor de serviços usar esses recursos de rede. Agora crie um cluster AKS na sua rede virtual e sub-rede utilizando o comando [az aks.][az-aks-create] Defina o seu próprio principal de serviço *\<appId>* *\<password>* e, como mostrado na saída do comando anterior para criar o principal de serviço.

Os seguintes intervalos de endereços IP são também definidos como parte do processo de criação de cluster:

* O *cidr de serviço-serviço* é utilizado para atribuir serviços internos no cluster AKS um endereço IP. Este intervalo de endereços IP deve ser um espaço de endereço que não está em uso em outro lugar no seu ambiente de rede. Esta gama inclui quaisquer gamas de rede no local se ligar, ou planeia ligar, as suas redes virtuais Azure utilizando a Rota Expresso ou uma ligação VPN site-to-site.

* O endereço *--dns-serviço-ip* deve ser o endereço *.10* do seu intervalo de endereços IP de serviço.

* O *pod-cidr* deve ser um grande espaço de endereço que não está em uso em outro lugar no seu ambiente de rede. Esta gama inclui quaisquer gamas de rede no local se ligar, ou planeia ligar, as suas redes virtuais Azure utilizando a Rota Expresso ou uma ligação VPN site-to-site.
    * Este intervalo de endereços deve ser suficientemente grande para acomodar o número de nós que espera escalar. Não é possível alterar este intervalo de endereços uma vez que o cluster é implantado se precisar de mais endereços para nóns adicionais.
    * A gama de endereços IP do pod é utilizada para atribuir um espaço de endereço */24* a cada nó no cluster. No exemplo seguinte, o *-pod-cidr* de *10.244.0.0/16* atribui o primeiro nó *10.244.0.0/24,* o segundo nó *10.244.1.0/24*, e o terceiro nó *10.244.2.0/24*.
    * À medida que o cluster escala ou upgrade, a plataforma Azure continua a atribuir uma gama de endereços IP do pod a cada novo nó.
    
* O *endereço-ponte-estiva-ponte* permite que os nós AKS comuniquem com a plataforma de gestão subjacente. Este endereço IP não deve estar dentro do intervalo de endereços IP de rede virtual do seu cluster, e não deve sobrepor-se a outros intervalos de endereços em uso na sua rede.

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
> Se desejar ativar um cluster AKS para incluir uma [política de rede Calico,][calico-network-policies] pode utilizar o seguinte comando.

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

Quando cria um cluster AKS, um grupo de segurança de rede e uma tabela de rotas são automaticamente criados. Estes recursos de rede são geridos pelo avião de controlo AKS. O grupo de segurança da rede está automaticamente associado aos NICs virtuais nos seus nós. A tabela de rotas está automaticamente associada à sub-rede de rede virtual. As regras do grupo de segurança da rede e as tabelas de rotas são automaticamente atualizadas à medida que cria e expõe serviços.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Traga a sua própria sub-rede e mesa de rota com kubenet

Com kubenet, deve existir uma tabela de rotas na sua sub-rede de clusters. A AKS suporta trazer a sua própria sub-rede existente e tabela de rotas.

Se a sua sub-rede personalizada não contiver uma tabela de rotas, a AKS cria uma para si e adiciona-lhe regras. Se a sua sub-rede personalizada contiver uma tabela de rotas quando cria o seu cluster, a AKS reconhece a tabela de rotas existente durante as operações de cluster e atualiza as regras em conformidade para as operações do fornecedor de nuvem.

Limitações:

* As permissões devem ser atribuídas antes da criação do cluster, certifique-se de que está a usar um principal de serviço com permissões de escrita para a sua sub-rede personalizada e tabela de rotas personalizada.
* As identidades geridas não são atualmente suportadas com tabelas de rotas personalizadas em kubenet.
* Uma tabela de rotas personalizada deve ser associada à sub-rede antes de criar o cluster AKS. Esta tabela de rotas não pode ser atualizada e todas as regras de encaminhamento devem ser adicionadas ou removidas da tabela de rotas iniciais antes de criar o cluster AKS.
* Todas as sub-redes dentro de uma rede virtual AKS devem ser associadas à mesma tabela de rotas.
* Todos os clusters AKS devem utilizar uma tabela de rotas única. Não se pode reutilizar uma mesa de rotas com vários aglomerados.


## <a name="next-steps"></a>Passos seguintes

Com um cluster AKS implantado na sua sub-rede de rede virtual existente, pode agora utilizar o cluster normalmente. Começa com [a construção de apps que utilizam o Azure Dev Spaces,][dev-spaces] [implemente aplicações existentes usando o Helm,][use-helm]ou [crie novas aplicações usando o Helm.][develop-helm]

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
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
