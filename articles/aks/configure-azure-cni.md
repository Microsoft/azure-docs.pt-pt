---
title: Configurar redes do Azure CNI no Azure Kubernetes Service (AKS)
description: Saiba como configurar redes de CNI do Azure (avançado) no Azure Kubernetes Service (AKS), incluindo a implementação de um cluster do AKS numa rede virtual existente e a sub-rede.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 4bd934c710d6300e95c60742d5873f5b71bdae59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002197"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurar redes do Azure CNI no Azure Kubernetes Service (AKS)

Por predefinição, o AKS clusters uso [kubenet][kubenet], e uma rede virtual e uma sub-rede são criados por si. Com o *kubenet*, nós obtém um endereço IP a partir de uma sub-rede de rede virtual. Tradução de endereços de rede (NAT), em seguida, está configurada em nós e pods recebem um endereço IP "ocultado" por trás o IP do nó. Essa abordagem reduz o número de endereços IP que tem de reservar no seu espaço de rede para os pods utilizar.

Com o [Interface de rede contentor do Azure (CNI)][cni-networking], cada pod obtém um endereço IP da sub-rede e podem ser acedido diretamente. Estes endereços IP tem de ser exclusivos em seu espaço de rede e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods que suporta. O número equivalente de endereços IP por nó, em seguida, é reservado com antecedência para esse nó. Esta abordagem requer um planejamento mais e, muitas vezes conduzem à exaustão de endereço IP ou a necessidade de recriar clusters numa sub-rede maior à medida que aumentam as suas exigências de aplicativo.

Este artigo mostra-lhe como utilizar *Azure CNI* rede a fim de criar e utilizar uma sub-rede de rede virtual para um cluster do AKS. Para obter mais informações sobre as opções de rede e considerações, consulte [conceitos de rede para o Kubernetes e o AKS][aks-network-concepts].

## <a name="prerequisites"></a>Pré-requisitos

* A rede virtual para o cluster do AKS têm de permitir conectividade de internet de saída.
* Não crie mais de um cluster do AKS na mesma sub-rede.
* Não podem utilizar clusters do AKS `169.254.0.0/16`, `172.30.0.0/16`, ou `172.31.0.0/16` intervalo de endereços do serviço para o Kubernetes.
* O principal de serviço utilizado pelo cluster do AKS tem de ter, pelo menos, [contribuinte de rede](../role-based-access-control/built-in-roles.md#network-contributor) permissões na sub-rede na sua rede virtual. Se pretender definir um [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de contribuinte de rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Planear o endereçamento IP para o seu cluster

Clusters configurados com a rede de Azure CNI requerem planeamento adicional. O tamanho da sua rede virtual e a respetiva sub-rede deve se acomodar o número de pods que pretende executar e o número de nós do cluster.

Endereços IP para os pods e nós do cluster são atribuídos a partir da sub-rede especificada dentro da rede virtual. Cada nó está configurado com um endereço IP primário. Por predefinição, 30 endereços IP adicionais são pré-configurado pelo CNI do Azure que estão atribuídos a pods agendadas no nó. Ao aumentar horizontalmente o seu cluster, cada nó da mesma forma é configurado com endereços IP da sub-rede. Também pode ver o [pods máximas por nó](#maximum-pods-per-node).

> [!IMPORTANT]
> O número de endereços IP necessários deve incluir considerações sobre a atualização e operações de dimensionamento. Se definir o intervalo de endereços IP para suportar apenas um número fixo de nós, não é possível atualizar ou dimensionar o seu cluster.
>
> - Quando **atualizar** seu cluster do AKS, um novo nó é implementada num cluster. Serviços e cargas de trabalho começam a ser executados no novo nó e um nó mais antigo é removido do cluster. Este processo de atualização sem interrupção requer um mínimo de um bloco adicional de endereços IP para estar disponível. A contagem de nó é, em seguida, `n + 1`.
>
> - Quando **dimensionamento** um cluster do AKS, um novo nó é implementado num cluster. Serviços e cargas de trabalho começam a executar no novo nó. O intervalo de endereços IP deve considerar considerações sobre como pode querer aumentar verticalmente o número de nós e pods que seu cluster pode suportar. Um nó adicional para operações de atualização também deve ser incluído. A contagem de nó é, em seguida, `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Se pretender que os nós para executar o número máximo de pods e destruir regularmente e implementar pods, deve também avaliar alguns endereços IP adicionais por nó. Estes endereços IP adicionais levam em consideração poderá demorar alguns segundos para um serviço a ser eliminado e o endereço IP é libertado para um novo serviço deve ser implementado e adquirir o endereço.

O plano de endereço IP para um cluster do AKS consiste num virtual de rede, pelo menos uma sub-rede para nós e pods e um intervalo de endereços do serviço de Kubernetes.

| Intervalo de endereços / Azure recursos | Limites e dimensionamento |
| --------- | ------------- |
| Rede virtual | A rede virtual do Azure pode ser tão grande quanto /8, mas está limitada a 65.536 endereços IP configurados. |
| Subrede | Tem de ser suficientemente grande para acomodar os nós, pods e recursos de todos os Kubernetes e do Azure que podem ser aprovisionados no seu cluster. Por exemplo, se implementar um balanceador de carga interno do Azure, o IPs de front-end são alocadas a partir da sub-rede de cluster, IPs públicos não. O tamanho da sub-rede também deve levar em operações de atualização de contas ou futuras necessidades de dimensionamento.<p />Para calcular a *mínima* tamanho de sub-rede, incluindo um nó adicional para operações de atualização: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Exemplo para um cluster de 50 nó: `(51) + (51  * 30 (default)) = 1,581` (/ 21 ou superior)<p/>Exemplo para um cluster de 50 nó que também inclui o aprovisionamento para aumentar verticalmente uma 10 nós adicionais: `(61) + (61 * 30 (default)) = 1,891` (/ 21 ou superior)<p>Se não especificar um número máximo de pods por nó, ao criar o cluster, o número máximo de pods por nó é definido como *30*. O número mínimo de endereços IP necessários baseia-se nesse valor. Se calcular seus requisitos de endereço IP mínimos num valor máximo diferente, veja [como configurar o número máximo de pods por nó](#configure-maximum---new-clusters) definir este valor quando implementar o cluster. |
| Intervalo de endereços do serviço Kubernetes | Não deve ser utilizado pelo qualquer elemento de rede no ou ligado a esta rede virtual neste intervalo. Endereço do serviço CIDR tem de ser menor do que /12. |
| Endereço IP do serviço de DNS do Kubernetes | Intervalo de endereços que será utilizado pela deteção de serviço de cluster (kube-dns) do serviço de endereço IP dentro do Kubernetes. Não utilize o primeiro endereço IP no seu intervalo de endereços, como.1. O primeiro endereço no seu intervalo de sub-rede é utilizado para o *kubernetes.default.svc.cluster.local* endereço. |
| Endereço de bridge do docker | Endereço IP (em notação CIDR) utilizado como a ponte de Docker endereço IP em nós. Predefinição de 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Pods máximas por nó

O número máximo de pods por nó num cluster do AKS é 110. O *predefinição* número máximo de pods por nó varia entre *kubenet* e *Azure CNI* funcionamento em rede e o método de implementação de cluster.

| Método de implementação | Padrão de Kubenet | Padrão CNI do Azure | Configurável nas implementação |
| -- | :--: | :--: | -- |
| CLI do Azure | 110 | 30 | Sim (até 110) |
| Modelo do Resource Manager | 110 | 30 | Sim (até 110) |
| Portal | 110 | 30 | Não |

### <a name="configure-maximum---new-clusters"></a>Configurar máximo - novos clusters

Pode configurar o número máximo de pods por nó *apenas no momento da implementação de cluster*. Se implementar com a CLI do Azure ou com um modelo do Resource Manager, pode definir os pods máximos por valor do nó tão elevada como 110.

* **CLI do Azure**: Especifique a `--max-pods` argumento ao implementar um cluster com o [criar az aks] [ az-aks-create] comando. O valor máximo é de 110.
* **Modelo do Resource Manager**: Especifique a `maxPods` propriedade o [ManagedClusterAgentPoolProfile] objeto quando implementar um cluster com um modelo do Resource Manager. O valor máximo é de 110.
* **Portal do Azure**: Não é possível alterar o número máximo de pods por nó, ao implementar um cluster com o portal do Azure. Clusters de rede CNI do Azure estão limitados a 30 pods por nó, ao implementar no portal do Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurar máximo - clusters existentes

Não é possível alterar os pods máximos por nó num cluster do AKS existente. Pode ajustar o número, apenas quando inicialmente a implementar o cluster.

## <a name="deployment-parameters"></a>Parâmetros de implementação

Quando cria um cluster do AKS, os seguintes parâmetros são configuráveis para funcionamento em rede do Azure CNI:

**Rede virtual**: A rede virtual no qual pretende implementar o cluster de Kubernetes. Se quiser criar uma nova rede virtual para o seu cluster, selecione *criar novo* e siga os passos a *criar rede virtual* secção. Para obter informações sobre os limites e quotas para uma rede virtual do Azure, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Sub-rede**: A sub-rede na rede virtual onde pretende implementar o cluster. Se quiser criar uma nova sub-rede na rede virtual para o seu cluster, selecione *criar novo* e siga os passos a *criar a sub-rede* secção. Para conectividade híbrida, o intervalo de endereços não deve se sobrepõe a outras redes virtuais no seu ambiente.

**Intervalo de endereços de serviço do Kubernetes**: Este é o conjunto de IPs virtuais que Kubernetes atribui a interno [serviços] [ services] no seu cluster. Pode utilizar qualquer intervalo de endereços privados que satisfaça os seguintes requisitos:

* Não tem de estar no intervalo de endereços IP de rede virtual do cluster
* Não pode sobrepor com outras redes virtuais com as quais elementos da rede virtual do cluster
* Não pode sobrepor com qualquer IPs no local
* Não tem de estar dentro dos intervalos `169.254.0.0/16`, `172.30.0.0/16`, ou `172.31.0.0/16`

Embora seja tecnicamente possível especificar um intervalo de endereços do serviço dentro da mesma rede virtual como o seu cluster, ao fazê-lo por isso, não é recomendado. Um comportamento imprevisível poderá resultar se os intervalos IP sobrepostos são utilizados. Para obter mais informações, consulte a [FAQ](#frequently-asked-questions) seção deste artigo. Para obter mais informações sobre serviços do Kubernetes, consulte [serviços] [ services] na documentação do Kubernetes.

**Endereço IP do serviço de DNS do Kubernetes**:  O endereço IP para o serviço DNS do cluster. Este endereço tem de estar dentro de *intervalo de endereços de serviço do Kubernetes*. Não utilize o primeiro endereço IP no seu intervalo de endereços, como.1. O primeiro endereço no seu intervalo de sub-rede é utilizado para o *kubernetes.default.svc.cluster.local* endereço.

**Endereço de Bridge do docker**: O endereço IP e máscara de rede para atribuir a bridge do Docker. A ponte de Docker permite que nós do AKS comunicar com a plataforma de gestão subjacente. Este endereço IP não tem de estar no intervalo de endereços IP de rede virtual do cluster e não deve se sobrepõe a outros intervalos de endereços em utilização na sua rede.

## <a name="configure-networking---cli"></a>Configurar redes - CLI

Quando cria um cluster do AKS com a CLI do Azure, também pode configurar redes de CNI do Azure. Utilize os seguintes comandos para criar um novo cluster do AKS com a rede de Azure CNI ativada.

Em primeiro lugar, obtenha o ID de recurso de sub-rede para a sub-rede existente no qual vai ser associado um cluster do AKS:

```console
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Utilize o [criar az aks] [ az-aks-create] comando com o `--network-plugin azure` argumento para criar um cluster com a rede avançada. Atualização do `--vnet-subnet-id` valor com o ID de sub-rede recolhido no passo anterior:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Configurar redes - portal

A seguinte captura de ecrã do portal do Azure mostra um exemplo de configurar estas definições durante a criação de cluster do AKS:

![Avançadas de configuração de rede no portal do Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

As perguntas e respostas seguintes aplicam-se para o **Azure CNI** configuração da rede.

* *Pode implementar VMs na sub-rede meu cluster?*

  Não. Não é suportada a implementação de VMs na sub-rede utilizada pelo seu cluster do Kubernetes. As VMs podem ser implementadas na mesma rede virtual, mas numa sub-rede diferente.

* *Pode configurar políticas de rede por pod?*

  Política de rede do Kubernetes está atualmente disponível como uma funcionalidade de pré-visualização do AKS. Para começar a utilizar, veja [proteger o tráfego entre pods ao utilizar políticas de rede no AKS][network-policy].

* *É o número máximo de pods implementável para um nó configurável?*

  Sim, quando implementar um cluster com a CLI do Azure ou um modelo do Resource Manager. Ver [pods máximo por nó](#maximum-pods-per-node).

  Não é possível alterar o número máximo de pods por nó num cluster existente.

* *Como posso configurar propriedades adicionais para a sub-rede que criei durante a criação de cluster do AKS? Por exemplo, pontos finais de serviço.*

  A lista completa de propriedades para a rede virtual e sub-redes que criou durante a criação de cluster do AKS pode ser configurada na página de configuração de rede virtual padrão no portal do Azure.

* *Pode utilizar uma sub-rede diferente dentro da minha rede virtual do cluster para o* **intervalo de endereços de serviço do Kubernetes**?

  Não é recomendável, mas esta configuração é possível. O intervalo de endereços do serviço é um conjunto de IPs virtuais (VIPs) Kubernetes atribui a serviços internos no seu cluster. Redes do Azure tem sem visibilidade para o intervalo IP do serviço de cluster de Kubernetes. Devido à falta de visibilidade do intervalo de endereços do serviço do cluster, é possível criar mais tarde uma nova sub-rede na rede virtual de cluster que sobrepõe-se com o intervalo de endereços do serviço. Se ocorrer uma sobreposição desse tipo, o Kubernetes pode atribuir um serviço de um IP já está em utilização por outro recurso na sub-rede, fazendo com que um comportamento imprevisível ou falhas. Ao garantir que usar um intervalo de endereços fora da rede virtual do cluster, é possível evitar este risco de sobreposição.

## <a name="next-steps"></a>Passos Seguintes

### <a name="networking-in-aks"></a>Funcionamento em rede no AKS

Saiba mais sobre o funcionamento em rede no AKS nos seguintes artigos:

- [Utilizar um endereço IP estático com o Balanceador de carga do Azure Kubernetes Service (AKS)](static-ip.md)
- [Utilizar um balanceador de carga interno com o Azure Container Service (AKS)](internal-lb.md)

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Ativar o suplemento de encaminhamento de aplicação de HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
- [Criar um controlador de entrada com um IP público dinâmico e configurar a encriptar vamos para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Criar um controlador de entrada com um IP público estático e configurar a encriptar vamos para gerar automaticamente certificados TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Motor do AKS

[Motor do serviço Kubernetes do Azure (AKS motor)] [ aks-engine] é um projeto de código-fonte aberto que gera os modelos Azure Resource Manager, pode utilizar para implementar clusters do Kubernetes no Azure.

Clusters de Kubernetes criados com o AKS motor de suportar o [kubenet] [ kubenet] e [Azure CNI] [ cni-networking] plug-ins. Como tal, ambos os cenários de redes são suportados pelo mecanismo de AKS.

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
