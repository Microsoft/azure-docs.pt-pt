---
title: Configurar a rede CNI do Azure no serviço kubernetes do Azure (AKS)
description: Saiba como configurar a rede do Azure CNI (avançado) no AKS (serviço kubernetes do Azure), incluindo a implantação de um cluster AKS em uma rede virtual e sub-rede existentes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: a0da8b932d2efe88391991286ede2858440e4465
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68232647"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurar a rede CNI do Azure no serviço kubernetes do Azure (AKS)

Por padrão, os clusters AKS usam [kubenet][kubenet], e uma rede virtual e uma sub-rede são criadas para você. Com o *kubenet*, os nós obtêm um endereço IP de uma sub-rede de rede virtual. A conversão de endereços de rede (NAT) é então configurada nos nós e pods recebe um endereço IP "oculto" por trás do nó IP. Essa abordagem reduz o número de endereços IP que você precisa reservar em seu espaço de rede para que os pods usem.

Com a [CNI (interface de rede de contêiner do Azure)][cni-networking], cada pod Obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o espaço de rede e devem ser planejados antecipadamente. Cada nó tem um parâmetro de configuração para o número máximo de pods que oferece suporte. O número equivalente de endereços IP por nó é então reservado antecipadamente para esse nó. Essa abordagem requer mais planejamento e, muitas vezes, leva a esgotamento de endereço IP ou a necessidade de recriar clusters em uma sub-rede maior conforme as demandas do seu aplicativo crescem.

Este artigo mostra como usar a rede *CNI do Azure* para criar e usar uma sub-rede de rede virtual para um cluster AKs. Para obter mais informações sobre opções e considerações de rede, consulte [conceitos de rede para kubernetes e AKs][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual para o cluster AKS deve permitir a conectividade de Internet de saída.
* Não crie mais de um cluster AKS na mesma sub-rede.
* Os clusters AKs não podem `169.254.0.0/16`usar `172.30.0.0/16` `172.31.0.0/16`,, ou `192.0.2.0/24` para o intervalo de endereços do serviço kubernetes.
* A entidade de serviço usada pelo cluster AKS deve ter pelo menos permissões de [colaborador de rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede em sua rede virtual. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de colaborador de rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planejar o endereçamento IP para o cluster

Os clusters configurados com a rede CNI do Azure exigem planejamento adicional. O tamanho da sua rede virtual e sua sub-rede devem acomodar o número de pods que você planeja executar e o número de nós do cluster.

Os endereços IP para o pods e os nós do cluster são atribuídos da sub-rede especificada dentro da rede virtual. Cada nó é configurado com um endereço IP primário. Por padrão, 30 endereços IP adicionais são pré-configurados pelo Azure CNI que são atribuídos ao pods agendado no nó. Ao escalar horizontalmente o cluster, cada nó é configurado da mesma forma com os endereços IP da sub-rede. Você também pode exibir o [pods máximo por nó](#maximum-pods-per-node).

> [!IMPORTANT]
> O número de endereços IP necessários deve incluir considerações para operações de atualização e dimensionamento. Se você definir o intervalo de endereços IP para dar suporte apenas a um número fixo de nós, não poderá atualizar nem dimensionar o cluster.
>
> - Quando você **atualiza** o cluster AKs, um novo nó é implantado no cluster. Os serviços e as cargas de trabalho começam a ser executados no novo nó e um nó mais antigo é removido do cluster. Esse processo de atualização sem interrupção exige que um mínimo de um bloco adicional de endereços IP esteja disponível. A contagem de nós é `n + 1`então.
>   - Essa consideração é particularmente importante quando você usa pools de nós do Windows Server (atualmente em visualização no AKS). Os nós do Windows Server no AKS não aplicam atualizações do Windows automaticamente, em vez disso, você executa uma atualização no pool de nós. Essa atualização implanta novos nós com a imagem mais recente do nó base do servidor do Windows 2019 e patches de segurança. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].
>
> - Quando você **dimensiona** um cluster AKs, um novo nó é implantado no cluster. Os serviços e as cargas de trabalho começam a ser executados no novo nó. Seu intervalo de endereços IP precisa levar em consideração como você pode querer escalar verticalmente o número de nós e os pods que o seu cluster pode dar suporte. Um nó adicional para operações de atualização também deve ser incluído. A contagem de nós é `n + number-of-additional-scaled-nodes-you-anticipate + 1`então.

Se você espera que seus nós executem o número máximo de pods e, regularmente, destruam e implantem pods, você também deve considerar alguns endereços IP adicionais por nó. Esses endereços IP adicionais levam em consideração pode levar alguns segundos para que um serviço seja excluído e o endereço IP liberado para que um novo serviço seja implantado e obtenha o endereço.

O plano de endereço IP para um cluster AKS consiste em uma rede virtual, pelo menos uma sub-rede para nós e pods, e um intervalo de endereços de serviço do kubernetes.

| Intervalo de endereços/recurso do Azure | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A rede virtual do Azure pode ser tão grande quanto/8, mas é limitada a 65.536 endereços IP configurados. |
| Subnet | Deve ser grande o suficiente para acomodar os nós, pods e todos os kubernetes e recursos do Azure que podem ser provisionados em seu cluster. Por exemplo, se você implantar um Azure Load Balancer interno, seus IPs de front-end serão alocados da sub-rede do cluster, não IPs públicos. O tamanho da sub-rede também deve levar em conta as operações de atualização ou necessidades futuras de dimensionamento.<p />Para calcular o tamanho *mínimo* da sub-rede, incluindo um nó adicional para operações de atualização:`(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemplo para um cluster de nó de `(51) + (51  * 30 (default)) = 1,581` 50: (/21 ou maior)<p/>Exemplo de um cluster de nó de 50 que também inclui provisionamento para escalar verticalmente 10 nós `(61) + (61 * 30 (default)) = 1,891` adicionais: (/21 ou maior)<p>Se você não especificar um número máximo de pods por nó ao criar o cluster, o número máximo de pods por nó será definido como *30*. O número mínimo de endereços IP necessários baseia-se nesse valor. Se você calcular os requisitos mínimos de endereço IP em um valor máximo diferente, consulte [como configurar o número máximo de pods por nó](#configure-maximum---new-clusters) para definir esse valor ao implantar o cluster. |
| Intervalo de endereços do serviço kubernetes | Esse intervalo não deve ser usado por nenhum elemento de rede ou conectado a essa rede virtual. O endereço do serviço CIDR deve ser menor que/12. |
| Endereço IP do serviço DNS do kubernetes | Endereço IP dentro do intervalo de endereços do serviço kubernetes que será usado pela descoberta do serviço de cluster (Kube-DNS). Não use o primeiro endereço IP em seu intervalo de endereços, como. 1. O primeiro endereço no intervalo de sub-rede é usado para o endereço *kubernetes. default. svc. cluster. local* . |
| Endereço de ponte do Docker | Endereço IP (em notação CIDR) usado como o endereço IP da ponte do Docker em nós. Esse CIDR está vinculado ao número de contêineres no nó. Padrão de 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Pods máxima por nó

O número máximo de pods por nó em um cluster AKS é de 250. O número máximo *padrão* de pods por nó varia entre *kubenet* e a rede *CNI do Azure* e o método de implantação de cluster.

| Método de implantação | Kubenet padrão | Padrão CNI do Azure | Configurável na implantação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | Sim (até 250) |
| Modelo do Resource Manager | 110 | 30 | Sim (até 250) |
| Portal | 110 | 30 | Não |

### <a name="configure-maximum---new-clusters"></a>Configurar o máximo-novos clusters

Você pode configurar o número máximo de pods por nó *somente no momento da implantação do cluster*. Se você implantar com o CLI do Azure ou com um modelo do Resource Manager, poderá definir o valor máximo de pods por nó como alto que 250.

| Redes | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

> [!NOTE]
> O valor mínimo na tabela acima é estritamente imposto pelo serviço AKS. Você não pode definir um valor de maxPods menor do que o mínimo mostrado, pois isso pode impedir que o cluster seja iniciado.

* **CLI do Azure**: Especifique o `--max-pods` argumento ao implantar um cluster com o comando [AZ AKs Create][az-aks-create] . O valor máximo é 250.
* **Modelo do Resource Manager**: Especifique a `maxPods` Propriedade no objeto [ManagedClusterAgentPoolProfile] ao implantar um cluster com um modelo do Resource Manager. O valor máximo é 250.
* **Portal do Azure**: Você não pode alterar o número máximo de pods por nó ao implantar um cluster com o portal do Azure. Os clusters de rede CNI do Azure são limitados a 30 pods por nó quando você implanta usando o portal do Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar o máximo de clusters existentes

Não é possível alterar o pods máximo por nó em um cluster AKS existente. Você pode ajustar o número somente quando implantar inicialmente o cluster.

## <a name="deployment-parameters"></a>Parâmetros de implantação

Quando você cria um cluster AKS, os seguintes parâmetros são configuráveis para a rede CNI do Azure:

**Rede virtual**: A rede virtual na qual você deseja implantar o cluster kubernetes. Se você quiser criar uma nova rede virtual para o cluster, selecione *criar nova* e siga as etapas na seção *criar rede virtual* . Para obter informações sobre os limites e cotas de uma rede virtual do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sub-rede**: A sub-rede na rede virtual em que você deseja implantar o cluster. Se você quiser criar uma nova sub-rede na rede virtual para o cluster, selecione *criar novo* e siga as etapas na seção *criar sub-rede* . Para conectividade híbrida, o intervalo de endereços não deve se sobrepor a outras redes virtuais em seu ambiente.

**Intervalo de endereços do serviço kubernetes**: Esse é o conjunto de IPs virtuais que o kubernetes atribui a [Serviços][services] internos em seu cluster. Você pode usar qualquer intervalo de endereços privado que atenda aos seguintes requisitos:

* Não deve estar dentro do intervalo de endereços IP da rede virtual do seu cluster
* Não deve se sobrepor a outras redes virtuais com as quais os pares de rede virtual de cluster
* Não deve se sobrepor a nenhum IPs local
* Não deve estar dentro dos intervalos `169.254.0.0/16`, `172.30.0.0/16`, `172.31.0.0/16`ou`192.0.2.0/24`

Embora seja tecnicamente possível especificar um intervalo de endereços de serviço na mesma rede virtual que o cluster, fazer isso não é recomendado. Um comportamento imprevisível pode ocorrer se forem usados intervalos de IP sobrepostos. Para obter mais informações, consulte a seção [perguntas frequentes](#frequently-asked-questions) deste artigo. Para obter mais informações sobre os serviços Kubernetess, consulte [Serviços][services] na documentação do kubernetes.

**Endereço IP do serviço DNS do kubernetes**:  O endereço IP do serviço DNS do cluster. Esse endereço deve estar dentro do *intervalo de endereços do serviço kubernetes*. Não use o primeiro endereço IP em seu intervalo de endereços, como. 1. O primeiro endereço no intervalo de sub-rede é usado para o endereço *kubernetes. default. svc. cluster. local* .

**Endereço de ponte**do Docker: O endereço IP e a máscara de rede a serem atribuídos à ponte do Docker. A ponte do Docker permite que os nós AKS se comuniquem com a plataforma de gerenciamento subjacente. Esse endereço IP não deve estar dentro do intervalo de endereços IP da rede virtual do cluster e não deve se sobrepor a outros intervalos de endereços em uso na sua rede.

## <a name="configure-networking---cli"></a>Configurar rede-CLI

Ao criar um cluster AKS com o CLI do Azure, você também pode configurar a rede CNI do Azure. Use os comandos a seguir para criar um novo cluster AKS com a rede CNI do Azure habilitada.

Primeiro, obtenha a ID de recurso de sub-rede para a sub-rede existente na qual o cluster AKS será Unido:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Use o comando [AZ AKs Create][az-aks-create] com o `--network-plugin azure` argumento para criar um cluster com a rede avançada. Atualize o `--vnet-subnet-id` valor com a ID de sub-rede coletada na etapa anterior:

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

## <a name="configure-networking---portal"></a>Configurar rede-portal

A seguinte captura de tela do portal do Azure mostra um exemplo de como definir essas configurações durante a criação do cluster AKS:

![Configuração de rede avançada no portal do Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

As perguntas e respostas a seguir se aplicam à configuração de rede **CNI do Azure** .

* *Posso implantar VMs em minha sub-rede de cluster?*

  Não. Não há suporte para a implantação de VMs na sub-rede usada pelo cluster kubernetes. As VMs podem ser implantadas na mesma rede virtual, mas em uma sub-rede diferente.

* *Posso configurar políticas de rede por Pod?*

  Sim, a política de rede kubernetes está disponível em AKS. Para começar, consulte [proteger o tráfego entre pods usando as políticas de rede no AKs][network-policy].

* *O número máximo de pods pode ser implantado em um nó configurável?*

  Sim, quando você implanta um cluster com o CLI do Azure ou um modelo do Resource Manager. Consulte [máximo de pods por nó](#maximum-pods-per-node).

  Não é possível alterar o número máximo de pods por nó em um cluster existente.

* *Como fazer configurar propriedades adicionais para a sub-rede que criei durante a criação do cluster AKS? Por exemplo, pontos de extremidade de serviço.*

  A lista completa de propriedades para a rede virtual e sub-redes que você cria durante a criação do cluster AKS pode ser configurada na página configuração de rede virtual padrão no portal do Azure.

* *Posso usar uma sub-rede diferente em minha rede virtual de cluster para o* **Intervalo de endereços do serviço kubernetes**?

  Isso não é recomendado, mas essa configuração é possível. O intervalo de endereços de serviço é um conjunto de VIPs (IPs virtuais) que o kubernetes atribui a serviços internos em seu cluster. A rede do Azure não tem visibilidade do intervalo IP do serviço do cluster kubernetes. Devido à falta de visibilidade no intervalo de endereços de serviço do cluster, é possível criar posteriormente uma nova sub-rede na rede virtual do cluster que se sobrepõe ao intervalo de endereços do serviço. Se essa sobreposição ocorrer, kubernetes poderá atribuir um serviço que já está em uso por outro recurso na sub-rede, causando comportamento imprevisível ou falhas. Ao garantir que você use um intervalo de endereços fora da rede virtual do cluster, você pode evitar esse risco de sobreposição.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a rede no AKS nos seguintes artigos:

- [Usar um endereço IP estático com o balanceador de carga do AKS (serviço kubernetes do Azure)](static-ip.md)
- [Usar um balanceador de carga interno com o AKS (serviço de contêiner do Azure)](internal-lb.md)

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada com um IP público dinâmico e configurar vamos criptografar para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Criar um controlador de entrada com um IP público estático e configurar vamos criptografar para gerar automaticamente os certificados TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Mecanismo de AKS

O [mecanismo de serviço kubernetes do Azure (mecanismo de AKs)][aks-engine] é um projeto de software livre que gera modelos de Azure Resource Manager que você pode usar para implantar clusters kubernetes no Azure.

Os clusters kubernetes criados com o mecanismo AKs dão suporte aos plug-ins [kubenet][kubenet] and [Azure CNI][cni-networking] . Assim, ambos os cenários de rede têm suporte do mecanismo AKS.

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
