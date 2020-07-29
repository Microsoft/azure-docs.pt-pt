---
title: Configurar a rede CNI Azure no Serviço Azure Kubernetes (AKS)
description: Aprenda a configurar a rede Azure CNI (avançada) no Serviço Azure Kubernetes (AKS), incluindo a implantação de um cluster AKS numa rede virtual e sub-rede existentes.
services: container-service
ms.topic: article
ms.date: 06/03/2019
ms.openlocfilehash: d025bcddfdee25cddac311ac9a201b7f3afebd22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416856"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurar a rede CNI Azure no Serviço Azure Kubernetes (AKS)

Por padrão, os clusters AKS utilizam [kubenet][kubenet], e uma rede virtual e sub-redes são criadas para si. Com *kubenet,* os nós obtêm um endereço IP a partir de uma sub-rede de rede virtual. A tradução de endereços de rede (NAT) é então configurada nos nós, e as cápsulas recebem um endereço IP "escondido" atrás do nó IP. Esta abordagem reduz o número de endereços IP que precisa de reservar no espaço da sua rede para as cápsulas utilizarem.

Com [a Interface de Rede de Contentores Azure (CNI),][cni-networking]cada pod obtém um endereço IP a partir da sub-rede e pode ser acedido diretamente. Estes endereços IP devem ser únicos em todo o seu espaço de rede, e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de cápsulas que suporta. O número equivalente de endereços IP por nó é então reservado à frente para esse nó. Esta abordagem requer mais planeamento, e muitas vezes leva à exaustão do endereço IP ou à necessidade de reconstruir clusters numa sub-rede maior à medida que as suas exigências de aplicação aumentam.

Este artigo mostra-lhe como utilizar a rede *CNI Azure* para criar e utilizar uma sub-rede de rede virtual para um cluster AKS. Para obter mais informações sobre opções e considerações de rede, consulte [conceitos de rede para Kubernetes e AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual para o cluster AKS deve permitir a conectividade de saída da Internet.
* Os clusters AKS não podem utilizar , , ou para a gama de `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` endereços de serviço Kubernetes.
* O principal de serviço utilizado pelo cluster AKS deve ter pelo menos permissões [de Contribuinte de Rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede dentro da sua rede virtual. Se desejar definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de utilizar a função de contribuinte de rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`
* Em vez de um principal de serviço, pode utilizar o sistema atribuído à identidade gerida para permissões. Para obter mais informações, consulte [utilização de identidades geridas.](use-managed-identity.md)
* A sub-rede atribuída ao conjunto de nó de nó AKS não pode ser uma [sub-rede delegada](../virtual-network/subnet-delegation-overview.md).

## <a name="plan-ip-addressing-for-your-cluster"></a>Endereço IP do plano para o seu cluster

Os agrupamentos configurados com a rede CNI Azure requerem um planeamento adicional. O tamanho da sua rede virtual e a sua sub-rede devem acomodar o número de cápsulas que pretende executar e o número de nós para o cluster.

Os endereços IP para as cápsulas e os nós do cluster são atribuídos a partir da sub-rede especificada dentro da rede virtual. Cada nó é configurado com um endereço IP primário. Por predefinição, 30 endereços IP adicionais são pré-configurados pela Azure CNI que são atribuídos a cápsulas programadas no nó. Quando escala o seu cluster, cada nó é igualmente configurado com endereços IP da sub-rede. Também pode ver as [cápsulas máximas por nó.](#maximum-pods-per-node)

> [!IMPORTANT]
> O número de endereços IP necessários deve incluir considerações para operações de atualização e escala. Se definir o intervalo de endereços IP para suportar apenas um número fixo de nós, não poderá atualizar ou escalar o seu cluster.
>
> - Quando **atualiza o** seu cluster AKS, um novo nó é implantado no cluster. Os serviços e cargas de trabalho começam a funcionar no novo nó, e um nó mais velho é removido do cluster. Este processo de atualização contínua requer um mínimo de um bloco adicional de endereços IP para estar disponível. A sua contagem de nós é `n + 1` então.
>   - Esta consideração é particularmente importante quando utiliza piscinas de nó do Windows Server. Os nós do Windows Server em AKS não aplicam automaticamente atualizações do Windows, em vez disso executa uma atualização no conjunto de nós. Esta atualização implementa novos nós com os mais recentes patches de imagem e patches de segurança do nó de base do Window Server 2019. Para obter mais informações sobre a atualização de um conjunto de nós do Windows Server, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].
>
> - Quando **escala** um cluster AKS, um novo nó é implantado no cluster. Os serviços e cargas de trabalho começam a funcionar no novo nó. O intervalo de endereços IP tem de ter em conta a forma como pretende aumentar o número de nós e cápsulas que o cluster pode suportar. Deve também ser incluído um nó adicional para as operações de atualização. A sua contagem de nós é `n + number-of-additional-scaled-nodes-you-anticipate + 1` então.

Se espera que os seus nós corram o número máximo de cápsulas e destrua e implemente regularmente cápsulas, também deve ter em conta alguns endereços IP adicionais por nó. Estes endereços IP adicionais têm em conta que pode levar alguns segundos para que um serviço seja eliminado e o endereço IP lançado para um novo serviço a ser implantado e adquirir o endereço.

O plano de endereço IP para um cluster AKS é composto por uma rede virtual, pelo menos uma sub-rede para nós e cápsulas e uma gama de endereços de serviço Kubernetes.

| Intervalo de endereços / recurso Azure | Limites e dimensionamentos |
| --------- | ------------- |
| Rede virtual | A rede virtual Azure pode ser tão grande como /8, mas está limitada a 65.536 endereços IP configurados. |
| Subrede | Deve ser grande o suficiente para acomodar os nós, cápsulas e todos os recursos Kubernetes e Azure que podem ser ateados no seu cluster. Por exemplo, se implementar um Balançador de Carga Azure interno, os seus IPs frontais são atribuídos a partir da sub-rede de cluster, e não de IPs públicos. O tamanho da sub-rede também deve ter em conta as operações de atualização ou as futuras necessidades de escala.<p />Para calcular o tamanho *mínimo* da sub-rede, incluindo um nó adicional para operações de atualização:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemplo para um aglomerado de nó de 50: `(51) + (51  * 30 (default)) = 1,581` (/21 ou maior)<p/>Exemplo para um cluster de 50 nós que também inclui disposições para escalar mais 10 nós: `(61) + (61 * 30 (default)) = 1,891` (/21 ou maior)<p>Se não especificar um número máximo de cápsulas por nó quando criar o seu cluster, o número máximo de cápsulas por nó é definido para *30*. O número mínimo de endereços IP necessários baseia-se nesse valor. Se calcular os seus requisitos mínimos de endereço IP num valor máximo diferente, consulte [como configurar o número máximo de cápsulas por nó](#configure-maximum---new-clusters) para definir este valor quando implementar o seu cluster. |
| Intervalo de endereços do serviço Kubernetes | Este intervalo não deve ser utilizado por nenhum elemento de rede ligado ou ligado a esta rede virtual. O endereço de serviço CIDR deve ser menor que /12. Você pode reutilizar esta gama em diferentes clusters AKS. |
| Endereço IP do serviço DNS do Kubernetes | Endereço IP dentro da gama de endereços de serviço Kubernetes que será usado pela descoberta do serviço de cluster (kube-dns). Não utilize o primeiro endereço IP na gama de endereços, como .1. O primeiro endereço da sua sub-rede é utilizado para o *endereço kubernetes.default.svc.cluster.local.* |
| Endereço de bridge do Docker | O endereço de rede de bridge do Docker representa o endereço de rede de bridge*docker0* predefinido presente em todas as instalações do Docker. Embora a ponte *Docker0* não seja usada por clusters AKS ou pelas próprias cápsulas, você deve definir este endereço para continuar a apoiar cenários como a *construção de estivadores* dentro do cluster AKS. É necessário selecionar um CIDR para o endereço de rede de ponte Docker, pois caso contrário, o Docker escolherá automaticamente uma sub-rede que possa entrar em conflito com outros CIDRs. Tem de escolher um espaço de endereço que não colida com o resto dos CIDRs nas suas redes, incluindo o serviço de serviço CIDR do cluster e o POD CIDR. Padrão de 172.17.0.1/16. Você pode reutilizar esta gama em diferentes clusters AKS. |

## <a name="maximum-pods-per-node"></a>Cápsulas máximas por nó

O número máximo de cápsulas por nó num cluster AKS é de 250. O número máximo *predefinido* de cápsulas por nó varia entre a rede *kubenet* e a rede *CNI Azure,* e o método de implantação do cluster.

| Método de implantação | Padrão kubenet | Padrão Azure CNI | Configurável na implantação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | Sim (até 250) |
| Modelo do Resource Manager | 110 | 30 | Sim (até 250) |
| Portal | 110 | 30 | Não |

### <a name="configure-maximum---new-clusters"></a>Configurar máximo - novos clusters

Você é capaz de configurar o número máximo de cápsulas por nó no tempo de implantação do cluster ou à medida que adiciona novas piscinas de nós. Se implementar com o CLI Azure ou com um modelo de Gestor de Recursos, pode definir o valor máximo de cápsulas por nó até 250.

Se não especificar maxPods ao criar novos conjuntos de nós, recebe um valor padrão de 30 para O Azure CNI.

É aplicado um valor mínimo para as cápsulas máximas por nó para garantir espaço para as cápsulas do sistema críticas à saúde do cluster. O valor mínimo que pode ser definido para os pods máximos por nó é de 10 se e somente se a configuração de cada piscina de nós tiver espaço para um mínimo de 30 cápsulas. Por exemplo, definir as cápsulas máximas por nó no mínimo de 10 requer que cada piscina individual de nó tenha um mínimo de 3 nós. Este requisito aplica-se também a cada novo conjunto de nós criado, pelo que se 10 for definido como cápsulas máximas por nó, cada piscina de nó adicionado deve ter pelo menos 3 nós.

| Redes | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 10 | 250 |
| Kubenet | 10 | 110 |

> [!NOTE]
> O valor mínimo na tabela acima é estritamente aplicado pelo serviço AKS. Não é possível definir um valor maxPods inferior ao mínimo indicado, pois fazê-lo pode impedir que o cluster arranque.

* **Azure CLI**: Especifique o `--max-pods` argumento quando implantar um cluster com os [az aks criar][az-aks-create] comando. O valor máximo é de 250.
* **Modelo de Gestor de Recursos**: Especifique a propriedade no objeto `maxPods` [ManagedClusterAgentPoolProfile] quando implementar um cluster com um modelo de Gestor de Recursos. O valor máximo é de 250.
* **Portal Azure**: Não é possível alterar o número máximo de cápsulas por nó quando se implanta um cluster com o portal Azure. Os clusters de rede Azure CNI estão limitados a 30 cápsulas por nó quando se implanta usando o portal Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar máximo - aglomerados existentes

A definição maxPod por nó pode ser definida quando se cria uma nova piscina de nós. Se precisar de aumentar a definição de maxPod por nó num cluster existente, adicione uma nova piscina de nós com a nova contagem de maxPod desejada. Depois de migrar as suas cápsulas para a nova piscina, apague a piscina mais antiga. Para eliminar qualquer piscina mais antiga num cluster, certifique-se de que está a configurar os modos de piscina de nó definidos no sistema de conjunto de nós do sistema de node do sistema [sistema[de node-pool do sistema de node do sistema de node do sistema].

## <a name="deployment-parameters"></a>Parâmetros de implantação

Quando cria um cluster AKS, os seguintes parâmetros são configuráveis para a rede CNI Azure:

**Rede virtual**: A rede virtual na qual pretende implantar o cluster Kubernetes. Se pretender criar uma nova rede virtual para o seu cluster, selecione *Criar novos* e siga os passos na secção de rede *virtual Create.* Para obter informações sobre os limites e quotas para uma rede virtual Azure, consulte [os limites de subscrição e serviços, quotas e restrições da Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)

**Sub-rede**: A sub-rede dentro da rede virtual onde pretende implantar o cluster. Se pretender criar uma nova sub-rede na rede virtual para o seu cluster, selecione *Criar novos* e siga os passos na secção *sub-redes Criar.* Para a conectividade híbrida, a gama de endereços não deve sobrepor-se a quaisquer outras redes virtuais no seu ambiente.

Gama de endereços de **serviço Kubernetes**: Este é o conjunto de IPs virtuais que kubernetes atribui a [serviços][services] internos no seu cluster. Pode utilizar qualquer gama de endereços privados que satisfaça os seguintes requisitos:

* Não deve estar dentro da gama de endereços IP de rede virtual do seu cluster
* Não deve sobrepor-se a quaisquer outras redes virtuais com as quais o cluster de rede virtual pares
* Não deve sobrepor-se a quaisquer IPs no local
* Não deve estar dentro dos `169.254.0.0/16` `172.30.0.0/16` intervalos, `172.31.0.0/16` ou`192.0.2.0/24`

Embora seja tecnicamente possível especificar um intervalo de endereços de serviço dentro da mesma rede virtual que o seu cluster, não é recomendado. Um comportamento imprevisível pode resultar se forem utilizados intervalos IP sobrepostos. Para mais informações, consulte a secção [faq](#frequently-asked-questions) deste artigo. Para obter mais informações sobre os serviços da Kubernetes, consulte [serviços][services] na documentação de Kubernetes.

**Endereço IP de serviço DE Serviço De Kubernetes**: O endereço IP para o serviço DNS do cluster. Este endereço tem de estar dentro do *intervalo de endereços do serviço Kubernetes*. Não utilize o primeiro endereço IP na gama de endereços, como .1. O primeiro endereço da sua sub-rede é utilizado para o *endereço kubernetes.default.svc.cluster.local.*

**Endereço da Ponte Docker**: O endereço da rede da ponte Docker representa o endereço de rede de ponte *padrão 0* presente em todas as instalações do Docker. Embora a ponte *Docker0* não seja usada por clusters AKS ou pelas próprias cápsulas, você deve definir este endereço para continuar a apoiar cenários como a *construção de estivadores* dentro do cluster AKS. É necessário selecionar um CIDR para o endereço de rede de ponte Docker, pois caso contrário, o Docker escolherá automaticamente uma sub-rede que possa entrar em conflito com outros CIDRs. Tem de escolher um espaço de endereço que não colida com o resto dos CIDRs nas suas redes, incluindo o serviço de serviço CIDR do cluster e o POD CIDR.

## <a name="configure-networking---cli"></a>Configure rede - CLI

Quando criar um cluster AKS com o Azure CLI, também pode configurar a rede CNI Azure. Utilize os seguintes comandos para criar um novo cluster AKS com rede CNI Azure ativada.

Em primeiro lugar, obtenha o ID do recurso sub-rede para a sub-rede existente na qual o cluster AKS será associado:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query "[0].id" --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Use os [az aks criar][az-aks-create] comando com o `--network-plugin azure` argumento para criar um cluster com rede avançada. Atualizar o `--vnet-subnet-id` valor com o ID da sub-rede recolhido no passo anterior:

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

## <a name="configure-networking---portal"></a>Configurar rede - portal

A imagem a seguir do portal Azure mostra um exemplo de configuração destas definições durante a criação do cluster AKS:

![Configuração avançada de rede no portal Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas frequentes

As seguintes perguntas e respostas aplicam-se à configuração de rede **Azure CNI.**

* *Posso colocar VMs na minha sub-rede de cluster?*

  Sim.

* *Posso configurar as políticas de rede por vagem?*

  Sim, a política de rede kubernetes está disponível em AKS. Para começar, consulte [o tráfego seguro entre cápsulas utilizando as políticas de rede em AKS][network-policy].

* *O número máximo de cápsulas é implantável para um nó configurável?*

  Sim, quando implementa um cluster com o CLI Azure ou um modelo de Gestor de Recursos. Ver [cápsulas máximas por nó](#maximum-pods-per-node).

  Não é possível alterar o número máximo de cápsulas por nó num aglomerado existente.

* *Como posso configurar propriedades adicionais para a sub-rede que criei durante a criação do cluster AKS? Por exemplo, pontos finais de serviço.*

  A lista completa de propriedades para a rede virtual e sub-redes que cria durante a criação do cluster AKS pode ser configurada na página de configuração de rede virtual padrão no portal Azure.

* *Posso utilizar uma sub-rede diferente dentro da minha rede virtual de cluster para a* gama de **endereços de serviço Kubernetes?**

  Não é recomendado, mas esta configuração é possível. A gama de endereços de serviço é um conjunto de IPs virtuais (VIPs) que a Kubernetes atribui a serviços internos no seu cluster. O Azure Networking não tem visibilidade na gama ip de serviço do cluster Kubernetes. Devido à falta de visibilidade na gama de endereços de serviço do cluster, é possível criar mais tarde uma nova sub-rede na rede virtual do cluster que se sobreponha à gama de endereços de serviço. Se tal sobreposição ocorrer, kubernetes poderia atribuir um serviço um IP que já está em uso por outro recurso na sub-rede, causando comportamentos ou falhas imprevisíveis. Ao garantir que utiliza um intervalo de endereços fora da rede virtual do cluster, pode evitar este risco de sobreposição.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre networking em AKS nos seguintes artigos:

- [Utilize um endereço IP estático com o equilibrador de carga Azure Kubernetes (AKS)](static-ip.md)
- [Utilize um equilibrador interno de carga com serviço de contentores Azure (AKS)](internal-lb.md)

- [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
- [Ativar o addon de encaminhamento de aplicações HTTP][aks-http-app-routing]
- [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada com um IP público dinâmico e configurar Let's Encrypt para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Criar um controlador de entrada com um IP público estático e configurar Vamos Encriptar para gerar automaticamente certificados TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Motor AKS

[O Motor de Serviço Azure Kubernetes (Motor AKS)][aks-engine] é um projeto de código aberto que gera modelos de Gestor de Recursos Azure que pode usar para implantar clusters Kubernetes no Azure.

Os clusters Kubernetes criados com suporte a motor AKS suportam os plugins [kubenet][kubenet] e [Azure CNI.][cni-networking] Como tal, ambos os cenários de networking são suportados pelo Motor AKS.

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
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
[sistema-nó-piscinas]: use-system-pools.md
