---
title: Configure rede kubenet no Serviço Azure Kubernetes (AKS)
description: Aprenda a configurar a rede kubenet (básica) no Serviço Azure Kubernetes (AKS) para implantar um cluster AKS numa rede virtual e subnet existentes.
services: container-service
ms.topic: article
ms.date: 06/26/2019
ms.reviewer: nieberts, jomore
ms.openlocfilehash: 09fd5326c2532e115dbab0752af31a809488f04c
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559686"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Utilize a rede kubenet com as suas próprias gamas de endereços IP no Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS usam [kubenet][kubenet], e uma rede virtual Azure e subnet são criados para si. Com *kubenet,* os nós obtêm um endereço IP da subnet da rede virtual Azure. Os pods recebem um endereço IP de um espaço de endereços logicamente diferente para a sub-rede da rede virtual do Azure dos nós. A tradução de endereços de rede (NAT) é configurada para que os pods possam chegar aos recursos na rede virtual do Azure. O endereço IP de origem do tráfego é NAT'd para o endereço IP primário do nó. Esta abordagem reduz consideravelmente o número de endereços IP que precisa de reservar no seu espaço de rede para que as cápsulas possam utilizar.

Com interface de rede de [contentores Azure (CNI),][cni-networking]cada cápsula recebe um endereço IP da subnet e pode ser acedida diretamente. Estes endereços IP devem ser únicos em todo o espaço da sua rede, e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de cápsulas que suporta. O número equivalente de endereços IP por nó é então reservado à frente para esse nó. Esta abordagem requer mais planeamento, e muitas vezes leva à exaustão do endereço IP ou à necessidade de reconstruir clusters numa subrede maior à medida que a sua aplicação exige crescer.

Este artigo mostra-lhe como usar a rede *kubenet* para criar e usar uma subnet de rede virtual para um cluster AKS. Para obter mais informações sobre opções e considerações de rede, consulte os conceitos de [rede para Kubernetes e AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual para o cluster AKS deve permitir a conectividade de acesso à Internet.
* Não crie mais do que um aglomerado AKS na mesma sub-rede.
* Os clusters AKS `169.254.0.0/16` `172.30.0.0/16`não `172.31.0.0/16`podem `192.0.2.0/24` utilizar, ou para a gama de endereços de serviço Kubernetes.
* O principal de serviço utilizado pelo cluster AKS deve ter pelo menos uma função de Colaborador de [Rede](../role-based-access-control/built-in-roles.md#network-contributor) na subrede dentro da sua rede virtual. Se desejar definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de utilizar a função de Colaborador de Rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Para utilizar piscinas de nó do Windows Server, tem de utilizar o Azure CNI. A utilização do kubenet uma vez que o modelo de rede não está disponível para os recipientes do Windows Server.

## <a name="before-you-begin"></a>Antes de começar

Precisa da versão Azure CLI 2.0.65 ou posteriormente instalada e configurada. Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, consulte [Instalar o Azure CLI][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Visão geral da rede kubenet com a sua própria subnet

Em muitos ambientes, definiu redes virtuais e subredes com gamas de endereços IP atribuídos. Estes recursos de rede virtuais são utilizados para suportar múltiplos serviços e aplicações. Para fornecer conectividade de rede, os clusters AKS podem usar *kubenet* (networking básico) ou Azure CNI *(networking avançado).*

Com *kubenet*, apenas os nós recebem um endereço IP na subnet da rede virtual. As cápsulas não conseguem comunicar-se diretamente umas com as outras. Em vez disso, o encaminhamento definido pelo utilizador (UDR) e o encaminhamento IP são utilizados para a conectividade entre as cápsulas através dos nós. Também pode implantar cápsulas atrás de um serviço que receba um endereço IP atribuído e equilibra o tráfego de carga para a aplicação. O diagrama que se segue mostra como os nós AKS recebem um endereço IP na subnet da rede virtual, mas não as cápsulas:

![Modelo de rede Kubenet com um cluster AKS](media/use-kubenet/kubenet-overview.png)

O Azure suporta um máximo de 400 rotas num UDR, por isso não pode ter um cluster AKS superior a 400 nós. Os [nós virtuais aks][virtual-nodes] e as políticas de rede Azure não são suportados com *kubenet*.  Você pode usar [as Políticas de Rede Calico][calico-network-policies], pois são suportadas com kubenet.

Com o *Azure CNI,* cada cápsula recebe um endereço IP na subnet IP e pode comunicar diretamente com outras cápsulas e serviços. Os seus clusters podem ser tão grandes quanto o intervalo de endereçoIP que especifica. No entanto, a gama de endereços IP deve ser planeada com antecedência e todos os endereços IP são consumidos pelos nós AKS com base no número máximo de cápsulas que podem suportar. As funcionalidades e cenários avançados da rede, tais como [Nóes virtuais][virtual-nodes] ou políticas de rede (azure ou Calico) são suportados com *O Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilidade e exaustão de endereçoIP

Com *o Azure CNI,* uma questão comum é que a gama de endereços IP atribuído é demasiado pequena para depois adicionar nós adicionais quando escala ou atualiza um cluster. A equipa de rede também pode não ser capaz de emitir uma gama de endereços IP suficientemente grande para suportar as suas exigências de aplicação esperadas.

Como um compromisso, você pode criar um cluster AKS que usa *kubenet* e conectar-se a uma subnet de rede virtual existente. Esta abordagem permite que os nós recebam endereços IP definidos, sem a necessidade de reservar um grande número de endereços IP na frente para todos os potenciais casulos que poderiam ser executados no cluster.

Com *kubenet,* você pode usar uma gama de endereços IP muito menor e ser capaz de suportar grandes clusters e exigências de aplicação. Por exemplo, mesmo com uma gama de endereços IP */27,* você poderia executar um cluster de nó de 20-25 com espaço suficiente para escalar ou atualizar. Este tamanho do cluster suportaria até *2.200-2.750* cápsulas (com um máximo de 110 cápsulas por nó). O número máximo de cápsulas por nó que pode configurar com *kubenet* em AKS é de 110.

Os seguintes cálculos básicos comparam a diferença nos modelos de rede:

- **kubenet** - uma gama de endereços IP simples */24* pode suportar até *251* nós no cluster (cada subnet de rede virtual Azure reserva os três primeiros endereços IP para operações de gestão)
  - Esta contagem de nós poderia suportar até *27.610* cápsulas (com um máximo de 110 cápsulas por nó com *kubenet)*
- **Azure CNI** - a mesma gama básica de sub-rede */24* só poderia suportar um máximo de *8* nós no cluster
  - Esta contagem de nós só poderia suportar até *240* cápsulas (com um máximo de 30 cápsulas por nó com *Azure CNI)*

> [!NOTE]
> Estes máximos não têm em conta operações de atualização ou escala. Na prática, não é possível executar o número máximo de nós que a gama de endereços IP da sub-rede suporta. Deve deixar alguns endereços IP disponíveis para utilização durante a escala de operações de atualização.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Ligações de rede virtual e expressRoute

Para fornecer conectividade no local, tanto as abordagens da rede *Kubenet* como *da Rede Azure-CNI* podem utilizar ligações [de rede virtual Azure][vnet-peering] ou [ExpressRoute.][express-route] Planeie cuidadosamente os seus intervalos de endereçoIP para evitar sobreposições e encaminhamento de tráfego incorreto. Por exemplo, muitas redes no local utilizam uma gama de endereços *10.0.0.0/8* que é publicitada sobre a ligação ExpressRoute. É aconselhável criar os seus clusters AKS em subredes de rede virtual Azure fora desta gama de endereços, tais como *172.16.0.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Escolha um modelo de rede para usar

A escolha do plugin de rede a utilizar para o seu cluster AKS é geralmente um equilíbrio entre flexibilidade e necessidades avançadas de configuração. As seguintes considerações ajudam a delinear quando cada modelo de rede pode ser o mais adequado.

Use *kubenet* quando:

- Tem espaço limitado de endereçoIP.
- A maior parte da comunicação da cápsula está dentro do aglomerado.
- Não precisa de funcionalidades avançadas de AKS, tais como nódos os virtuais ou a Política de Rede Azure.  Utilize as políticas de [rede Calico.][calico-network-policies]

Utilize *o Azure CNI* quando:

- Tem espaço de endereço IP disponível.
- A maior parte da comunicação da cápsula é para recursos fora do aglomerado.
- Não queres gerir os UDRs.
- Precisa de funcionalidades avançadas AKS, como nódos os virtuais ou a Política de Rede Azure.  Utilize as políticas de [rede Calico.][calico-network-policies]

Para obter mais informações para o ajudar a decidir qual o modelo de rede a utilizar, consulte [a Compare os modelos de rede e o seu âmbito][network-comparisons]de suporte .

## <a name="create-a-virtual-network-and-subnet"></a>Criar uma rede virtual e uma sub-rede

Para começar com o uso de *kubenet* e sua própria rede virtual subnet, primeiro crie um grupo de recursos usando o [grupo az criar][az-group-create] comando. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Se não tiver uma rede virtual e uma subrede existentes para utilizar, crie estes recursos de rede utilizando o comando da [rede az.][az-network-vnet-create] No exemplo seguinte, a rede virtual é nomeada *myVnet* com o prefixo de endereço de *192.168.0.0.0.0/16*. É criada uma sub-rede denominada *myAKSSubnet* com o prefixo de endereço *192.168.1.0/24*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Criar um diretor de serviço e atribuir permissões

Para permitir que um cluster do AKS interaja com outros recursos do Azure, é utilizado um principal de serviço do Azure Active Directory. O diretor de serviço precisa de ter permissões para gerir a rede virtual e sub-rede que os nódosos AKS usam. Para criar um diretor de serviço, utilize o comando [az ad sp create-for-rbac:][az-ad-sp-create-for-rbac]

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída de exemplo seguinte mostra o ID da aplicação e a palavra-passe para o seu diretor de serviço. Estes valores são utilizados em passos adicionais para atribuir uma função ao diretor de serviço e, em seguida, criar o cluster AKS:

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

Para atribuir as delegações corretas nas etapas restantes, utilize o [programa vnet da rede Az][az-network-vnet-show] e a [rede az vnet subnet mostrar][az-network-vnet-subnet-show] comandos para obter os IDs de recursos necessários. Estes IDs de recurso são armazenados como variáveis e referenciados nas etapas restantes:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Agora atribua o principal de serviço para as permissões do seu cluster AKS *Colaborador* na rede virtual usando a atribuição de [funções az criar][az-role-assignment-create] comando. Forneça o seu próprio * \<appId>* como mostrado na saída do comando anterior para criar o diretor de serviço:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Criar um cluster AKS na rede virtual

Criou agora uma rede virtual e uma subnet, e criou e atribuiu permissões para um diretor de serviço utilizar esses recursos de rede. Agora crie um cluster AKS na sua rede virtual e subnet usando as [aks az criar][az-aks-create] comando. Defina o seu próprio aplicativo principal * \<de serviçoId>* e * \<palavra-passe>, *como mostrado na saída do comando anterior para criar o diretor de serviço.

As seguintes gamas de endereços IP são também definidas como parte do processo de criação do cluster:

* O *--serviço-cidr* é usado para atribuir serviços internos no cluster AKS um endereço IP. Esta gama de endereços IP deve ser um espaço de endereço que não esteja a ser utilizado em outros lugares do ambiente da sua rede. Esta gama inclui quaisquer gamas de rede no local se ligar, ou planear ligar, as suas redes virtuais Azure utilizando a Rota Expresso ou uma ligação VPN site-to-site.

* O endereço *-dns-service-ip* deve ser o endereço *.10* do seu endereço IP de serviço.

* O *-pod-cidr* deve ser um grande espaço de endereço que não está a ser usado em outros lugares do ambiente da sua rede. Esta gama inclui quaisquer gamas de rede no local se ligar, ou planear ligar, as suas redes virtuais Azure utilizando a Rota Expresso ou uma ligação VPN site-to-site.
    * Esta gama de endereços deve ser suficientemente grande para acomodar o número de nós que espera escalar. Não pode alterar esta gama de endereços uma vez que o cluster é implantado se precisar de mais endereços para nós adicionais.
    * A gama de endereços IP da cápsula é utilizada para atribuir um espaço de endereço */24* a cada nó do cluster. No exemplo seguinte, o *--pod-cidr* de *10.244.0.0/16* atribui o primeiro nó *10.244.0.0.0/24*, o segundo nó *10.244.1.0/24*, e o terceiro nó *10.244.2.0/24*.
    * À medida que o cluster escala ou atualiza, a plataforma Azure continua a atribuir uma gama de endereços IP a cada novo nó.
    
* O *endereço --docker-bridge* permite que os nódosos AKS se comuniquem com a plataforma de gestão subjacente. Este endereço IP não deve estar dentro da gama de endereços IP da rede virtual do seu cluster, e não deve sobrepor-se a outras gamas de endereços utilizadas na sua rede.

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
> Se desejar permitir que um cluster AKS inclua uma política de [rede Calico,][calico-network-policies] pode utilizar o seguinte comando.

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

Quando se cria um cluster AKS, é criado um grupo de segurança de rede e uma tabela de rotas. Estes recursos de rede são geridos pelo plano de controlo AKS. O grupo de segurança da rede está automaticamente associado aos NICs virtuais nos seus nódosos. A tabela de rotas está automaticamente associada à sub-rede virtual. As regras do grupo de segurança da rede e as tabelas de rotas são automaticamente atualizadas à medida que cria e expõe serviços.

## <a name="next-steps"></a>Passos seguintes

Com um cluster AKS implantado na sua subnet de rede virtual existente, pode agora usar o cluster normalmente. Inicie-se com a construção de [apps utilizando o Azure Dev Spaces,][dev-spaces] [implemente aplicações existentes usando helm][use-helm], ou [crie novas aplicações usando helm][develop-helm].

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
