---
title: Personalize as rotas definidas pelo utilizador (UDR) no Serviço Azure Kubernetes (AKS)
description: Saiba como definir uma rota de saída personalizada no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 72ba90510afb00ee001c97612e88f452039f53a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182133"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Personalize a saída do cluster com uma rota User-Defined

A Egress de um cluster AKS pode ser personalizada para se adaptar a cenários específicos. Por predefinição, a AKS irá providenciar um Balanceador de Carga Standard SKU para ser configurado e utilizado para a saída. No entanto, a configuração padrão pode não satisfazer os requisitos de todos os cenários se os IPs públicos forem proibidos ou se forem necessários lúpulos adicionais para a saída.

Este artigo percorre a forma de personalizar a rota de saída de um cluster para suportar cenários de rede personalizados, como aqueles que não permite iPs públicos e exige que o cluster se sente atrás de um aparelho virtual de rede (NVA).

## <a name="prerequisites"></a>Pré-requisitos
* Versão Azure CLI 2.0.81 ou maior
* Versão API de `2020-01-01` ou maior


## <a name="limitations"></a>Limitações
* O OutboundType só pode ser definido no cluster criar tempo e não pode ser atualizado posteriormente.
* A definição `outboundType` requer clusters AKS com um `vm-set-type` de `VirtualMachineScaleSets` . `load-balancer-sku` `Standard`
* Definir `outboundType` um valor requer uma rota definida pelo utilizador com conectividade de saída válida para o `UDR` cluster.
* Definir `outboundType` um valor de implica que o IP de `UDR` origem ingresso encaminhado para o balanceador de carga pode **não corresponder** ao endereço de destino de saída do cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Visão geral dos tipos de saída em AKS

Um cluster AKS pode ser personalizado com um tipo único `outboundType` `loadBalancer` ou `userDefinedRouting` .

> [!IMPORTANT]
> O tipo de saída só afeta o tráfego de saída do seu cluster. Para obter mais informações, consulte [a criação de controladores de entrada.](ingress-basic.md)

> [!NOTE]
> Pode utilizar a sua própria [tabela de rotas][byo-route-table] com rede UDR e kubenet. Certifique-se de que a identidade do cluster (principal de serviço ou identidade gerida) tem permissões do Contribuinte para a tabela de rotas personalizada.

### <a name="outbound-type-of-loadbalancer"></a>Tipo de saída de loadBalancer

Se `loadBalancer` estiver definido, o AKS completa automaticamente a seguinte configuração. O equilibrador de carga é utilizado para a saída através de um IP público atribuído pela AKS. Um tipo de saída `loadBalancer` suporta serviços de tipo Kubernetes, `loadBalancer` que esperam saída do equilibrador de carga criado pelo fornecedor de recursos AKS.

A seguinte configuração é feita por AKS.
   * Um endereço IP público é previsto para a saída de cluster.
   * O endereço IP público é atribuído ao recurso do balançador de carga.
   * As piscinas de backend para o balançador de carga são configuradas para os nós de agente no cluster.

Abaixo está uma topologia de rede implantada em clusters AKS por padrão, que usam um `outboundType` de `loadBalancer` .

![O diagrama mostra a entrada I P e a saída I P, onde a entrada I P direciona o tráfego para um equilibrador de carga, que direciona o tráfego de e para um cluster interno e outro tráfego para a saída I P, que direciona o tráfego para a Internet, M C R, Azure exigiu serviços, e o Avião de Controlo A K S.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo de saída de userDefinedRouting

> [!NOTE]
> A utilização do tipo de saída é um cenário avançado de rede e requer uma configuração adequada da rede.

Se `userDefinedRouting` estiver definido, a AKS não configurará automaticamente caminhos de saída. A configuração da saída tem de ser feita por si.

O cluster AKS deve ser implantado numa rede virtual existente com uma sub-rede previamente configurada porque quando não se utiliza a arquitetura do balanceador de carga padrão (SLB), deve estabelecer uma saída explícita. Como tal, esta arquitetura requer o envio explícito de tráfego de saída para um aparelho como uma firewall, gateway, proxy ou para permitir que a Tradução de Endereços de Rede (NAT) seja feita por um IP público atribuído ao balanceador de carga padrão ou ao aparelho.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Criação do balançador de carga com o userDefinedRouting

Os clusters AKS com um tipo de UDR de saída recebem um balanceador de carga padrão (SLB) apenas quando o primeiro serviço Kubernetes do tipo 'loadBalancer' for implantado. O equilibrador de carga está configurado com um endereço IP público para pedidos *de entrada* e um pool de backend para pedidos *de entrada.* As regras de entrada são configuradas pelo fornecedor de nuvem Azure, mas **nenhum endereço IP público de saída ou regras** de saída são configurados como resultado de ter um tipo de UDR de saída. A sua UDR continuará a ser a única fonte de tráfego de saídas.

Os equilibradores de carga Azure [não incorrem numa carga até que uma regra seja colocada](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Implementar um cluster com tipo de saída de UDR e Azure Firewall

Para ilustrar a aplicação de um cluster com tipo de saída utilizando uma rota definida pelo utilizador, um cluster pode ser configurado numa rede virtual com uma Firewall Azure na sua própria sub-rede. Veja este exemplo no [tráfego de saídas restritas com o exemplo da firewall Azure](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> O tipo de saída de UDR requer uma rota para 0.0.0.0/0 e próximo destino de lúpulo de NVA (Aparelho Virtual de Rede) na tabela de rotas.
> A tabela de rotas já tem um padrão 0.0.0.0/0 para a Internet, sem um IP público para sNAT apenas adicionando esta rota não lhe fornecerá saída. A AKS validará que não cria uma rota 0.0.0.0/0 que aponta para a Internet, mas sim para NVA ou gateway, etc. Ao utilizar um tipo de UDR de saída, não é criado um endereço IP público de carregamento para **pedidos de entrada,** a menos que um serviço de *loadbalancer* tipo esteja configurado. Um endereço IP público para **pedidos de saída** nunca é criado pela AKS se um tipo de UDR de saída for definido.

## <a name="next-steps"></a>Passos seguintes

Consulte [a visão geral da UDR em rede Azure](../virtual-network/virtual-networks-udr-overview.md).

Ver [como criar, alterar ou apagar uma tabela de rotas](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
