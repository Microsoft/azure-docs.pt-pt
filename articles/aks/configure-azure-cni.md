---
title: Configure Rede Azure CNI no Serviço Azure Kubernetes (AKS)
description: Saiba como configurar a rede Azure CNI (avançada) no Serviço Azure Kubernetes (AKS), incluindo a implantação de um cluster AKS numa rede virtual existente e subnet.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: 6f194cb97850fcb24e4789ac0ba39b6f03d99e6e
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617391"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configure Rede Azure CNI no Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS usam [kubenet][kubenet], e uma rede virtual e subnet são criados para si. Com *kubenet,* os nós obtêm um endereço IP de uma subnet de rede virtual. A tradução de endereços de rede (NAT) é então configurada nos nós, e as cápsulas recebem um endereço IP "escondido" atrás do IP do nó. Esta abordagem reduz o número de endereços IP que precisa de reservar no seu espaço de rede para que as cápsulas possam utilizar.

Com interface de rede de [contentores Azure (CNI),][cni-networking]cada cápsula recebe um endereço IP da subnet e pode ser acedida diretamente. Estes endereços IP devem ser únicos em todo o espaço da sua rede, e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de cápsulas que suporta. O número equivalente de endereços IP por nó é então reservado à frente para esse nó. Esta abordagem requer mais planeamento, e muitas vezes leva à exaustão do endereço IP ou à necessidade de reconstruir clusters numa subrede maior à medida que a sua aplicação exige crescer.

Este artigo mostra-lhe como usar a rede *Azure CNI* para criar e usar uma subnet de rede virtual para um cluster AKS. Para obter mais informações sobre opções e considerações de rede, consulte os conceitos de [rede para Kubernetes e AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual para o cluster AKS deve permitir a conectividade de acesso à Internet.
* Os clusters AKS `169.254.0.0/16` `172.30.0.0/16`não `172.31.0.0/16`podem `192.0.2.0/24` utilizar, ou para a gama de endereços de serviço Kubernetes.
* O principal de serviço utilizado pelo cluster AKS deve ter pelo menos permissões de Colaborador de [Rede](../role-based-access-control/built-in-roles.md#network-contributor) na subrede dentro da sua rede virtual. Se desejar definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de utilizar a função de Colaborador de Rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Em vez de um diretor de serviço, pode utilizar o sistema de identidade gerida para obter permissões. Para mais informações, consulte [Use identidades geridas](use-managed-identity.md).
* A sub-rede atribuída ao conjunto de nósaks não pode ser uma [sub-rede delegada.](../virtual-network/subnet-delegation-overview.md)

## <a name="plan-ip-addressing-for-your-cluster"></a>Plano ip endereçamento para o seu cluster

Os clusters configurados com a rede Azure CNI requerem planeamento adicional. O tamanho da sua rede virtual e da sua subnet deve acomodar o número de cápsulas que planeia executar e o número de nós para o cluster.

Os endereços IP para as cápsulas e os nós do cluster são atribuídos a partir da sub-rede especificada dentro da rede virtual. Cada nó é configurado com um endereço IP primário. Por predefinição, 30 endereços IP adicionais são pré-configurados pelo Azure CNI que são atribuídos a cápsulas programadas no nó. Quando escala o seu cluster, cada nó é configurado de forma semelhante com endereços IP da subnet. Também pode ver as [cápsulas máximas por nó](#maximum-pods-per-node).

> [!IMPORTANT]
> O número de endereços IP necessários deve incluir considerações para operações de atualização e escalação. Se definir o intervalo de endereçoIP para suportar apenas um número fixo de nós, não pode atualizar ou escalar o seu cluster.
>
> - Ao **atualizar** o seu cluster AKS, um novo nó é implantado no cluster. Os serviços e cargas de trabalho começam a funcionar no novo nó, e um nó mais antigo é removido do cluster. Este processo de atualização de rolamento requer um mínimo de um bloco adicional de endereços IP para estar disponível. Sua contagem de `n + 1`nó é então.
>   - Esta consideração é particularmente importante quando utiliza piscinas de nó do Windows Server (atualmente em pré-visualização no AKS). Os nós do Windows Server no AKS não aplicam automaticamente as Atualizações do Windows, em vez disso, realiza uma atualização na piscina do nó. Esta atualização implementa novos nós com os mais recentes patches de imagem e segurança do nó base do Window Server 2019. Para obter mais informações sobre a atualização de um conjunto de nós do Windows Server, consulte [a atualização de um conjunto][nodepool-upgrade]de nós em AKS .
>
> - Quando **escalaum** aglomerado AKS, um novo nó é implantado no cluster. Os serviços e as cargas de trabalho começam a funcionar no novo nó. O seu intervalo de endereçoIP tem de ter em consideração a forma como pretende aumentar o número de nós e pods que o seu cluster pode suportar. Deve também ser incluído um nó adicional para operações de atualização. Sua contagem de `n + number-of-additional-scaled-nodes-you-anticipate + 1`nó é então.

Se espera que os seus nós executem o número máximo de cápsulas e destrua e desloque regularmente as cápsulas, também deverá ter em conta alguns endereços IP adicionais por nó. Estes endereços IP adicionais têm em conta que pode levar alguns segundos para que um serviço seja eliminado e o endereço IP lançado para um novo serviço a ser implantado e adquirir o endereço.

O plano de endereçoIP para um cluster AKS consiste numa rede virtual, pelo menos uma subnet para nós e cápsulas, e uma gama de endereços de serviço Kubernetes.

| Gama de endereços / recurso Azure | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A rede virtual Azure pode ser tão grande como /8, mas está limitada a 65.536 endereços IP configurados. |
| Subrede | Deve ser grande o suficiente para acomodar os nós, as cápsulas e todos os recursos kubernetes e azure que podem ser aprovisionados no seu cluster. Por exemplo, se implementar um Equilíbrio de Carga Azure interno, os seus IPs frontais são atribuídos a partir da subnet cluster, e não de IPs públicos. O tamanho da subrede também deve ter em conta as operações de atualização ou as necessidades futuras de escala.<p />Para calcular o tamanho *mínimo* da sub-rede, incluindo um nó adicional para operações de atualização:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemplo para um aglomerado de `(51) + (51  * 30 (default)) = 1,581` 50 nós: (/21 ou maior)<p/>Exemplo para um cluster de 50 nós que também inclui disposições `(61) + (61 * 30 (default)) = 1,891` para escalar mais 10 nós: (/21 ou maior)<p>Se não especificar um número máximo de cápsulas por nó quando criar o seu cluster, o número máximo de cápsulas por nó está definido para *30*. O número mínimo de endereços IP necessários baseia-se nesse valor. Se calcular os requisitos mínimos de endereço IP num valor máximo diferente, consulte [como configurar o número máximo de cápsulas por nó](#configure-maximum---new-clusters) para definir este valor quando implementar o seu cluster. |
| Intervalo de endereços do serviço Kubernetes | Esta gama não deve ser utilizada por nenhum elemento de rede ligado ou ligado a esta rede virtual. O endereço de serviço CIDR deve ser inferior a /12. Pode reutilizar esta gama em diferentes clusters AKS. |
| Endereço IP do serviço DNS do Kubernetes | Endereço IP dentro da gama de endereços de serviço Kubernetes que será usado pela descoberta do serviço de cluster (kube-dns). Não utilize o primeiro endereço IP na sua gama de endereços, como .1. O primeiro endereço na sua gama de subnetés é utilizado para o *endereço kubernetes.default.svc.cluster.local.* |
| Endereço de bridge do Docker | O endereço de rede de bridge do Docker representa o endereço de rede de bridge*docker0* predefinido presente em todas as instalações do Docker. Embora a ponte *Docker0* não seja utilizada por aglomerados AKS ou pelas próprias cápsulas, deve definir este endereço para continuar a apoiar cenários como *a construção* de dockers dentro do cluster AKS. É necessário selecionar um CIDR para o endereço da rede de pontes Docker, porque caso contrário, o Docker escolherá automaticamente uma sub-rede que possa entrar em conflito com outros CIDRs. Deve escolher um espaço de endereço que não colida com o resto dos CIDRs nas suas redes, incluindo o CIDR de serviço do cluster e o CIDR do grupo. Incumprimento de 172.17.0.1/16. Pode reutilizar esta gama em diferentes clusters AKS. |

## <a name="maximum-pods-per-node"></a>Cápsulas máximas por nó

O número máximo de cápsulas por nó num aglomerado AKS é de 250. O número máximo de cápsulas *por* nó varia entre a rede *Kubenet* e *a Rede Azure CNI* e o método de implantação do cluster.

| Método de implantação | Padrão kubenet | Padrão Azure CNI | Configurável na implantação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | Sim (até 250) |
| Modelo do Resource Manager | 110 | 30 | Sim (até 250) |
| Portal | 110 | 30 | Não |

### <a name="configure-maximum---new-clusters"></a>Configurar máximo - novos clusters

É capaz de configurar o número máximo de cápsulas por nó *apenas no tempo*de implantação do cluster . Se implantar com o Azure CLI ou com um modelo de Gestor de Recursos, pode definir as cápsulas máximas por valor de nó até 250.

É aplicado um valor mínimo para cápsulas máximas por nó para garantir espaço para as cápsulas do sistema críticas à saúde do cluster. O valor mínimo que pode ser definido para cápsulas máximas por nó é de 10 se e apenas se a configuração de cada piscina de nó tiver espaço para um mínimo de 30 cápsulas. Por exemplo, a fixação das cápsulas máximas por nó no mínimo de 10 requer que cada piscina individual de nó tenha um mínimo de 3 nós. Este requisito aplica-se também a cada novo conjunto de nós criado, pelo que se 10 for definido como cápsulas máximas por nó cada piscina de nó adicionado deve ter pelo menos 3 nós.

| Redes | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Rio Kubenet | 10 | 110 |

> [!NOTE]
> O valor mínimo na tabela acima é estritamente aplicado pelo serviço AKS. Não é possível definir um valor maxPods inferior ao mínimo demonstrado, pois fazê-lo pode impedir que o cluster arranque.

* **Azure CLI**: `--max-pods` Especifique o argumento quando implementar um cluster com as [aks az criar][az-aks-create] comando. O valor máximo é de 250.
* **Modelo de Gestor** `maxPods` de Recursos : Especifique a propriedade no objeto [ManagedClusterAgentPoolProfile] quando implementar um cluster com um modelo de Gestor de Recursos. O valor máximo é de 250.
* **Portal Azure:** Não é possível alterar o número máximo de cápsulas por nó quando se implanta um cluster com o portal Azure. Os clusters de rede Azure CNI estão limitados a 30 cápsulas por nó quando se implanta utilizando o portal Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar máximo - aglomerados existentes

Não se pode alterar as cápsulas máximas por nó num aglomerado AKS existente. Só é possível ajustar o número quando inicialmente implanta o cluster.

## <a name="deployment-parameters"></a>Parâmetros de implantação

Quando cria um cluster AKS, os seguintes parâmetros são configuráveis para a rede Azure CNI:

**Rede virtual**: A rede virtual na qual pretende implantar o cluster Kubernetes. Se quiser criar uma nova rede virtual para o seu cluster, selecione *Criar nova* e siga os passos na secção de rede *virtual Create.* Para obter informações sobre os limites e quotas de uma rede virtual Azure, consulte [os limites de subscrição e serviço do Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)

**Sub-rede**: A sub-rede dentro da rede virtual onde pretende implantar o cluster. Se pretender criar uma nova subrede na rede virtual para o seu cluster, selecione *Criar novo* e siga os passos na secção *de sub-rede Create.* Para a conectividade híbrida, a gama de endereços não deve sobrepor-se a quaisquer outras redes virtuais no seu ambiente.

Gama de endereços de **serviço Kubernetes**: Este é o conjunto de IPs virtuais que kubernetes atribui aos [serviços internos][services] no seu cluster. Pode utilizar qualquer gama de endereços privados que satisfaça os seguintes requisitos:

* Não deve estar dentro da gama de endereços IP da rede virtual do seu cluster
* Não deve sobrepor-se a quaisquer outras redes virtuais com as quais os pares da rede virtual cluster
* Não deve sobrepor-se a quaisquer IPs no local
* Não deve estar dentro `169.254.0.0/16` `172.30.0.0/16`dos `172.31.0.0/16`intervalos, ou`192.0.2.0/24`

Embora seja tecnicamente possível especificar um intervalo de endereços de serviço dentro da mesma rede virtual que o seu cluster, não é recomendado fazê-lo. Um comportamento imprevisível pode resultar se forem utilizadas gamas IP sobrepostas. Para mais informações, consulte a secção [DE PERGUNTAS FREQUENTES](#frequently-asked-questions) deste artigo. Para obter mais informações sobre os serviços da Kubernetes, consulte [os Serviços][services] na documentação kubernetes.

Endereço IP do **serviço DNS kubernetes**: O endereço IP para o serviço DNS do cluster. Este endereço tem de estar dentro do *intervalo de endereços do serviço Kubernetes*. Não utilize o primeiro endereço IP na sua gama de endereços, como .1. O primeiro endereço na sua gama de subnetés é utilizado para o *endereço kubernetes.default.svc.cluster.local.*

**Endereço da Ponte Docker**: O endereço da rede de ponte Docker representa o endereço de rede de ponte *estivador* padrão 0presente em todas as instalações do Docker. Embora a ponte *Docker0* não seja utilizada por aglomerados AKS ou pelas próprias cápsulas, deve definir este endereço para continuar a apoiar cenários como *a construção* de dockers dentro do cluster AKS. É necessário selecionar um CIDR para o endereço da rede de pontes Docker, porque caso contrário, o Docker escolherá automaticamente uma sub-rede que possa entrar em conflito com outros CIDRs. Deve escolher um espaço de endereço que não colida com o resto dos CIDRs nas suas redes, incluindo o CIDR de serviço do cluster e o CIDR do grupo.

## <a name="configure-networking---cli"></a>Configurar a rede - CLI

Quando cria um cluster AKS com o Azure CLI, também pode configurar a rede Azure CNI. Utilize os seguintes comandos para criar um novo cluster AKS com rede Azure CNI ativada.

Em primeiro lugar, obtenha o ID de recurso da sub-rede para a sub-rede existente em que o cluster AKS será aderido:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Use o [az aks][az-aks-create] `--network-plugin azure` criar comando com o argumento para criar um cluster com networking avançado. Atualize `--vnet-subnet-id` o valor com o ID da sub-rede recolhido na etapa anterior:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Configurar networking - portal

A seguinte imagem do portal Azure mostra um exemplo de configuração destas configurações durante a criação do cluster AKS:

![Configuração avançada de networking no portal Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

As seguintes perguntas e respostas aplicam-se à configuração de rede **Azure CNI.**

* *Posso colocar VMs na minha subnet de cluster?*

  Não. A implantação de VMs na subnet utilizada pelo seu cluster Kubernetes não é suportada. Os VMs podem ser implantados na mesma rede virtual, mas numa subrede diferente.

* *Posso configurar as políticas de rede por pod?*

  Sim, a política de rede Kubernetes está disponível no AKS. Para começar, consulte [o tráfego seguro entre cápsulas utilizando políticas][network-policy]de rede no AKS .

* *O número máximo de cápsulas é implantável para um nó configurável?*

  Sim, quando se implanta um cluster com o Azure CLI ou um modelo de Gestor de Recursos. Ver [Cápsulas máximas por nó](#maximum-pods-per-node).

  Não se pode alterar o número máximo de cápsulas por nó num aglomerado existente.

* *Como configurar propriedades adicionais para a subnet que criei durante a criação do cluster AKS? Por exemplo, pontos finais de serviço.*

  A lista completa de propriedades para a rede virtual e subredes que cria durante a criação de cluster AKS pode ser configurada na página de configuração de rede virtual padrão no portal Azure.

* *Posso usar uma subrede diferente dentro* da minha rede virtual cluster para a gama de endereços de **serviço Kubernetes?**

  Não é recomendado, mas esta configuração é possível. A gama de endereços de serviço é um conjunto de IPs virtuais (VIPs) que kubernetes atribui aos serviços internos no seu cluster. O Azure Networking não tem visibilidade na gama IP de serviço do cluster Kubernetes. Devido à falta de visibilidade na gama de endereços de serviço do cluster, é possível criar mais tarde uma nova subrede na rede virtual cluster que se sobrepõe à gama de endereços de serviço. Se tal sobreposição ocorrer, kubernetes podem atribuir um serviço a um IP que já está em uso por outro recurso na subrede, causando comportamentos ou falhas imprevisíveis. Ao garantir que utiliza uma gama de endereços fora da rede virtual do cluster, pode evitar este risco de sobreposição.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre networking em AKS nos seguintes artigos:

- [Utilize um endereço IP estático com o equilibrador de carga do Serviço Azure Kubernetes (AKS)](static-ip.md)
- [Utilize um equilibrante de carga interna com o Serviço de Contentores Azure (AKS)](internal-lb.md)

- [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
- [Ativar o add-on de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Crie um controlador de ingresso com um IP público dinâmico e configure Vamos encriptar para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Crie um controlador de ingresso com um IP público estático e configure Vamos encriptar para gerar automaticamente certificados TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Motor AKS

O Motor de [Serviço Azure Kubernetes (AKS Engine)][aks-engine] é um projeto de código aberto que gera modelos de Gestor de Recursos Azure que pode utilizar para implantar clusters Kubernetes no Azure.

Os clusters Kubernetes criados com o motor AKS suportam os plugins [kubenet][kubenet] e [Azure CNI.][cni-networking] Como tal, ambos os cenários de networking são suportados pelo AKS Engine.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[Perfil gerido clusterAgentPool]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
