---
title: Conceitos - sistema de rede nos serviços de Kubernetes do Azure (AKS)
description: Saiba mais sobre o funcionamento em rede no Azure Kubernetes Service (AKS), incluindo kubenet e funcionamento em rede de Azure CNI, controladores de entrada, balanceadores de carga e endereços IP estáticos.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: iainfou
ms.openlocfilehash: 360caaec0033136ffa250d636864fbed8359b8ef
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244252"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicações no Azure Kubernetes Service (AKS)

Numa abordagem de microsserviços baseada em contentor ao desenvolvimento de aplicativos, os componentes da aplicação devem trabalhar em conjunto para processar as suas tarefas. O Kubernetes dispõe de vários recursos que permitem a comunicação do aplicativo. Pode ligar a e expor os aplicativos internamente ou externamente. Para criar aplicações de elevada disponibilidade, pode carregar equilibrar seus aplicativos. Aplicações mais complexas podem exigir configuração de tráfego de entrada para a terminação de SSL/TLS ou roteamento de vários componentes. Por motivos de segurança, também poderá restringir o fluxo de tráfego de rede para dentro ou entre nós e os pods.

Este artigo apresenta os conceitos principais que fornecem o funcionamento em rede para seus aplicativos no AKS:

- [Serviços](#services)
- [Redes virtuais do Azure](#azure-virtual-networks)
- [Controladores de entrada](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso às suas aplicações ou de componentes da aplicação comunicar entre si, o Kubernetes fornece uma camada de abstração para redes virtuais. Nós do Kubernetes estão ligados a uma rede virtual e podem fornecer conectividade de entrada e saída para pods. O *kube proxy* componente é executado em cada nó para fornecer esses recursos de rede.

No Kubernetes, *serviços* agrupem os pods para permitir o acesso direto por meio de um endereço IP ou nome DNS e uma porta específica. Também pode distribuir o tráfego utilizando um *Balanceador de carga*. Mais complexa de encaminhamento de tráfego de aplicativo também pode ser obtida com *controladores de entrada*. É possível com o Kubernetes, segurança e a filtragem do tráfego de rede para pods *as políticas de rede*.

A plataforma do Azure também ajuda a simplificar a rede virtual para os clusters do AKS. Quando cria um balanceador de carga do Kubernetes, o recurso do Balanceador de carga do Azure subjacente é criado e configurado. Como abrir portas de rede para pods, são configuradas as regras de grupo de segurança de rede do Azure correspondente. Para o encaminhamento de aplicações de HTTP, o Azure também pode configurar *DNS externo* como entrada nova rotas estão configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração de rede para cargas de trabalho de aplicação, utiliza o Kubernetes *serviços* logicamente agrupar um conjunto de pods e fornecer conectividade de rede. Estão disponíveis os seguintes tipos de serviço:

- **IP do cluster** -cria um endereço IP interno para uso dentro do cluster do AKS. Ideal para aplicativos apenas internos que oferecem suporte a outras cargas de trabalho dentro do cluster.

    ![Diagrama que mostra o fluxo de tráfego de IP do Cluster num cluster do AKS][aks-clusterip]

- **NodePort** -cria um mapeamento de portas no nó subjacente que permite que o aplicativo seja acessado diretamente com o endereço IP do nó e a porta.

    ![Diagrama que mostra o fluxo de tráfego NodePort num cluster do AKS][aks-nodeport]

- **LoadBalancer** - cria um recurso de Balanceador de carga do Azure, configura um endereço IP externo e liga-se os pods pedidos para o conjunto de back-end de Balanceador de carga. Para permitir tráfego de clientes para alcance o aplicativo, as regras de balanceamento de carga são criados nas portas desejadas. 

    ![Diagrama que mostra o fluxo de tráfego do Balanceador de carga num cluster do AKS][aks-loadbalancer]

    Para controle adicional e encaminhamento de tráfego de entrada, em vez disso, pode utilizar um [controlador de entradas](#ingress-controllers).

- **ExternalName** -cria uma entrada DNS específica para facilitar o acesso de aplicação.

O endereço IP para balanceadores de carga e serviços pode ser atribuído dinamicamente ou pode especificar um endereço IP estático existente para utilizar. É possível atribuir endereços IP estáticos internos e externos. Este endereço IP estático existente, muitas vezes, está associado a uma entrada DNS.

Ambos *interno* e *externo* balanceadores de carga podem ser criados. Balanceadores de carga internos apenas têm atribuídas um endereço IP privado, pelo que não podem ser acedidos a partir da Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais do Azure

No AKS, pode implementar um cluster que utiliza um dos seguintes modelos de duas redes:

- *Kubenet* networking - rede de recursos, normalmente, são criados e configurados, conforme o cluster do AKS é implementado.
- *Interface de rede de contentor do Azure (CNI)* redes - cluster do AKS a está ligado a recursos de rede virtual existente e configurações.

### <a name="kubenet-basic-networking"></a>Redes de Kubenet (básico)

O *kubenet* opção de rede é a configuração predefinida para a criação de cluster do AKS. Com o *kubenet*, nós obtém um endereço IP da sub-rede da rede virtual do Azure. Pods recebem um endereço IP a partir de um espaço de endereço logicamente diferente para a sub-rede de rede virtual do Azure de nós. Tradução de endereços de rede (NAT), em seguida, está configurada para que os pods podem chegar a recursos na rede virtual do Azure. O endereço IP de origem do tráfego é que NAT para IP primária o nó o resolveria.

Nós de utilizar o [kubenet] [ kubenet] Plug-in do Kubernetes. Pode permitir que a plataforma do Azure, criar e configurar as redes virtuais para ou optar por implementar o seu cluster do AKS para uma sub-rede de rede virtual existente. Mais uma vez, apenas os nós recebem um endereço IP encaminhável e os pods utilizam NAT para comunicar com outros recursos fora do cluster do AKS. Essa abordagem reduz significativamente o número de endereços IP que tem de reservar no seu espaço de rede para os pods utilizar.

Para obter mais informações, consulte [configurar kubenet de rede para um cluster do AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Redes de CNI (avançado) do Azure

Com o Azure CNI, cada pod obtém um endereço IP da sub-rede e pode ser acedido diretamente. Estes endereços IP tem de ser exclusivos em seu espaço de rede e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de pods que suporta. O número equivalente de endereços IP por nó, em seguida, é reservado com antecedência para esse nó. Esta abordagem requer um planejamento mais e, muitas vezes conduzem à exaustão de endereço IP ou a necessidade de recriar clusters numa sub-rede maior à medida que aumentam as suas exigências de aplicativo.

Nós de utilizar o [Interface de rede contentor do Azure (CNI)] [ cni-networking] Plug-in do Kubernetes.

![Diagrama que mostra dois nós com pontes ligar cada uma a uma VNet do Azure única][advanced-networking-diagram]

CNI do Azure fornece as seguintes funcionalidades ao longo do sistema de rede kubenet:

- Cada pod do cluster é atribuído um endereço IP na rede virtual. Os pods podem comunicar diretamente com outros pods no cluster e a outros nós na rede virtual.
- Pods numa sub-rede que têm pontos finais de serviço ativados podem ligar com segurança a serviços do Azure, como o armazenamento do Azure e SQL DB.
- Pode criar rotas definidas pelo utilizador (UDR) para encaminhar o tráfego de pods para uma aplicação Virtual de rede.

Para obter mais informações, consulte [configurar o Azure CNI para um cluster do AKS][aks-configure-advanced-networking].

## <a name="ingress-controllers"></a>Controladores de entrada

Quando cria um tipo de LoadBalancer serviço, é criado um recurso do Balanceador de carga do Azure subjacente. O Balanceador de carga está configurado para distribuir o tráfego para os pods no seu serviço numa determinada porta. A LoadBalancer só funciona na camada 4 - o serviço não está ciente de que os aplicativos propriamente ditos e não pode fazer qualquer considerações adicionais de encaminhamento.

*Controladores de entrada* funciona na camada 7 e pode utilizar regras mais inteligentes para distribuir o tráfego de aplicativo. Um uso comum de um controlador de entrada é encaminhar o tráfego HTTP para diferentes aplicações com base no URL de entrada.

![Diagrama que mostra o fluxo de tráfego de entrada num cluster do AKS][aks-ingress]

No AKS, pode criar um recurso de entrada através de algo como NGINX ou utilizar a funcionalidade de encaminhamento de aplicação de HTTP do AKS. Quando ativar o encaminhamento de aplicações de HTTP para um cluster do AKS, a plataforma do Azure cria o controlador de entrada e um *DNS externo* controlador. À medida que novos recursos de entrada são criados no Kubernetes, são criados os necessários registos DNS numa zona DNS específicos do cluster. Para obter mais informações, consulte [implementar o encaminhamento de aplicações de HTTP][aks-http-routing].

Outra funcionalidade comum de entrada é a terminação de SSL/TLS. Em aplicativos da web de grandes acedidos através de HTTPS, a terminação de TLS pode ser tratada pelo recurso de entrada, em vez de dentro da própria aplicação. Para fornecer a geração automática a certificação TLS e a configuração, pode configurar o recurso de entrada a utilizar fornecedores, como vamos encriptar. Para obter mais informações sobre como configurar um controlador de entrada do NGINX com vamos encriptar, consulte [entrada e TLS][aks-ingress-tls].

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para as VMs, por exemplo, os nós do AKS. Criar serviços, como um LoadBalancer, a plataforma do Azure configura automaticamente quaisquer regras de grupo de segurança de rede que são necessários. Não configure manualmente as regras de grupo de segurança de rede para filtrar o tráfego para pods num cluster do AKS. Definir todas as portas necessárias e o encaminhamento como parte de seus manifestos de serviço do Kubernetes e deixe a plataforma do Azure para criar ou atualizar as regras adequadas. Também pode utilizar políticas de rede, tal como explicado na próxima seção, para aplicar automaticamente regras de filtragem de tráfego para os pods.

## <a name="network-policies"></a>Políticas de rede

Por predefinição, todos os pods num cluster do AKS podem enviar e receber tráfego sem limitações. Para obter mais segurança, pode querer definir regras que controlam o fluxo de tráfego. Aplicações de back-end, muitas vezes, só são expostas aos serviços de front-end necessário ou componentes da base de dados só estão acessíveis para os escalões de aplicação que se ligar aos mesmos.

Política de rede é uma funcionalidade de Kubernetes, que permite-lhe controlar o fluxo de tráfego entre os pods. Pode optar por permitir ou negar o tráfego com base em etiquetas de definições de atribuídas como, espaço de nomes ou porta de tráfego. Grupos de segurança de rede são mais para os nós do AKS, não os pods. O uso de diretivas de rede é uma forma mais adequada, nativas da cloud para controlar o fluxo de tráfego. À medida pods são criados dinamicamente num cluster do AKS, as políticas de rede necessária podem ser aplicadas automaticamente.

Para obter mais informações, consulte [proteger o tráfego entre pods através de políticas de rede no Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="next-steps"></a>Passos Seguintes

Introdução ao AKS de rede, criar e configurar um cluster do AKS com seus próprios intervalos de endereços IP usando [kubenet] [ aks-configure-kubenet-networking] ou [Azure CNI] [ aks-configure-advanced-networking].

Para as práticas recomendadas associadas, consulte [melhores práticas para a conectividade de rede e segurança no AKS][operator-best-practices-network].

Para obter mais informações sobre principais Kubernetes e conceitos do AKS, consulte os artigos seguintes:

- [Kubernetes / clusters do AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / AKS acesso e identidade][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]
- [Kubernetes / aumentar do AKS][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
