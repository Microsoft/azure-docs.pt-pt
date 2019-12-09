---
title: Conceitos – rede nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre a rede no AKS (serviço kubernetes do Azure), incluindo o kubenet e o Azure CNI Networking, controladores de entrada, balanceadores de carga e endereços IP estáticos.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 429205d1df91b5a63679d1189903e5340ab837f8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913885"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicativos no serviço kubernetes do Azure (AKS)

Em uma abordagem de microserviços baseada em contêiner para o desenvolvimento de aplicativos, os componentes do aplicativo devem trabalhar juntos para processar suas tarefas. O kubernetes fornece vários recursos que habilitam essa comunicação do aplicativo. Você pode se conectar e expor aplicativos interna ou externamente. Para criar aplicativos altamente disponíveis, você pode balancear a carga de seus aplicativos. Aplicativos mais complexos podem exigir a configuração do tráfego de entrada para término de SSL/TLS ou roteamento de vários componentes. Por motivos de segurança, talvez você também precise restringir o fluxo do tráfego de rede em ou entre os pods e os nós.

Este artigo apresenta os principais conceitos que fornecem rede para seus aplicativos no AKS:

- [Serviços](#services)
- [Redes virtuais do Azure](#azure-virtual-networks)
- [Controladores de entrada](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso aos seus aplicativos ou para que os componentes do aplicativo se comuniquem entre si, o kubernetes fornece uma camada de abstração para a rede virtual. Os nós kubernetes são conectados a uma rede virtual e podem fornecer conectividade de entrada e saída para pods. O componente *Kube-proxy* é executado em cada nó para fornecer esses recursos de rede.

No kubernetes, os *Serviços* logicamente agrupam pods para permitir o acesso direto por meio de um endereço IP ou nome DNS e em uma porta específica. Você também pode distribuir o tráfego usando um *balanceador de carga*. O roteamento mais complexo de tráfego de aplicativos também pode ser obtido com *controladores de entrada*. A segurança e a filtragem do tráfego de rede para pods são possíveis com *as políticas de rede*kubernetes.

A plataforma Azure também ajuda a simplificar a rede virtual para clusters AKS. Quando você cria um balanceador de carga kubernetes, o recurso de balanceador de carga do Azure subjacente é criado e configurado. À medida que você abre portas de rede para pods, as regras de grupo de segurança de rede do Azure correspondentes são configuradas. Para o roteamento de aplicativos HTTP, o Azure também pode configurar o *DNS externo* à medida que novas rotas de entrada são configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração de rede para cargas de trabalho de aplicativo, o kubernetes usa *Serviços* para agrupar logicamente um conjunto de pods e fornecer conectividade de rede. Os seguintes tipos de serviço estão disponíveis:

- **IP do cluster** – cria um endereço IP interno para uso no cluster AKs. Bom para aplicativos somente internos que dão suporte a outras cargas de trabalho no cluster.

    ![Diagrama mostrando o fluxo de tráfego IP do cluster em um cluster AKS][aks-clusterip]

- **NodePort** – cria um mapeamento de porta no nó subjacente que permite que o aplicativo seja acessado diretamente com o endereço IP do nó e a porta.

    ![Diagrama mostrando o fluxo de tráfego NodePort em um cluster AKS][aks-nodeport]

- **Balancer** – cria um recurso do Azure Load Balancer, configura um endereço IP externo e conecta o pods solicitado ao pool de back-end do balanceador de carga. Para permitir que o tráfego de clientes chegue ao aplicativo, as regras de balanceamento de carga são criadas nas portas desejadas. 

    ![Diagrama mostrando Load Balancer fluxo de tráfego em um cluster AKS][aks-loadbalancer]

    Para controle adicional e roteamento do tráfego de entrada, você pode usar um controlador de [entrada](#ingress-controllers).

- **Externalname** – cria uma entrada DNS específica para facilitar o acesso ao aplicativo.

O endereço IP para balanceadores de carga e serviços podem ser atribuídos dinamicamente ou você pode especificar um endereço IP estático existente a ser usado. Os endereços IP estáticos internos e externos podem ser atribuídos. Esse endereço IP estático existente geralmente está vinculado a uma entrada DNS.

Os balanceadores de carga *internos* e *externos* podem ser criados. Os balanceadores de carga internos só recebem um endereço IP privado, portanto, eles não podem ser acessados pela Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais do Azure

No AKS, você pode implantar um cluster que usa um dos dois modelos de rede a seguir:

- Rede *Kubenet* – os recursos de rede normalmente são criados e configurados à medida que o cluster AKs é implantado.
- Rede *CNI (interface de rede de contêiner do Azure)* -o cluster AKs está conectado a recursos e configurações de rede virtual existentes.

### <a name="kubenet-basic-networking"></a>Rede Kubenet (básica)

A opção de rede *kubenet* é a configuração padrão para a criação de cluster AKs. Com o *kubenet*, os nós obtêm um endereço IP da sub-rede da rede virtual do Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente para a sub-rede da rede virtual do Azure dos nós. A conversão de endereços de rede (NAT) é então configurada para que o pods possa alcançar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é NAT para o endereço IP primário do nó.

Os nós usam o plug-in [kubenet][kubenet] kubernetes. Você pode deixar a plataforma Azure criar e configurar as redes virtuais para você ou optar por implantar o cluster AKS em uma sub-rede de rede virtual existente. Novamente, somente os nós recebem um endereço IP roteável e o pods usam NAT para se comunicar com outros recursos fora do cluster AKS. Essa abordagem reduz consideravelmente o número de endereços IP que você precisa reservar em seu espaço de rede para que os pods usem.

Para obter mais informações, consulte [Configurar a rede kubenet para um cluster AKs][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Rede CNI do Azure (avançado)

Com o Azure CNI, cada pod Obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o espaço de rede e devem ser planejados antecipadamente. Cada nó tem um parâmetro de configuração para o número máximo de pods que oferece suporte. O número equivalente de endereços IP por nó é então reservado antecipadamente para esse nó. Essa abordagem requer mais planejamento, como pode levar ao esgotamento de endereço IP ou à necessidade de recriar clusters em uma sub-rede maior conforme as demandas do seu aplicativo crescem.

Os nós usam o plug-in kubernetes do [CNI (interface de rede de contêiner do Azure)][cni-networking] .

![Diagrama mostrando dois nós com pontes conectando cada um a uma única VNet do Azure][advanced-networking-diagram]

Para obter mais informações, consulte [Configurar o Azure CNI para um cluster AKs][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Comparar modelos de rede

O kubenet e o Azure CNI fornecem conectividade de rede para seus clusters AKS. No entanto, há vantagens e desvantagens para cada uma delas. Em um alto nível, as seguintes considerações se aplicam:

* **kubenet**
    * Conserva o espaço de endereço IP.
    * Usa o balanceador de carga interno ou externo do kubernetes para alcançar pods fora do cluster.
    * Você deve gerenciar e manter manualmente as rotas definidas pelo usuário (UDRs).
    * Máximo de 400 nós por cluster.
* **CNI do Azure**
    * Os pods obtêm conectividade total de rede virtual e podem ser diretamente acessados por meio de seu endereço IP privado de redes conectadas.
    * Requer mais espaço de endereço IP.

Existem as seguintes diferenças de comportamento entre kubenet e CNI do Azure:

| Capacidade                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Implantar cluster em uma rede virtual nova ou existente                                            | Com suporte-UDRs aplicado manualmente | Suportadas |
| Conectividade de pod de Pod                                                                         | Suportadas | Suportadas |
| Pod-conectividade da VM; VM na mesma rede virtual                                          | Funciona quando iniciado pelo Pod | Funciona de ambas as maneiras |
| Pod-conectividade da VM; VM na rede virtual emparelhada                                            | Funciona quando iniciado pelo Pod | Funciona de ambas as maneiras |
| Acesso local usando VPN ou rota expressa                                                | Funciona quando iniciado pelo Pod | Funciona de ambas as maneiras |
| Acesso a recursos protegidos por pontos de extremidade de serviço                                             | Suportadas | Suportadas |
| Expor serviços Kubernetess usando um serviço de balanceador de carga, um gateway de aplicativo ou um controlador de entrada | Suportadas | Suportadas |
| Zonas privadas e DNS do Azure padrão                                                          | Suportadas | Suportadas |

### <a name="support-scope-between-network-models"></a>Escopo de suporte entre modelos de rede

Independentemente do modelo de rede que você usa, tanto o kubenet quanto o CNI do Azure podem ser implantados de uma das seguintes maneiras:

* A plataforma Azure pode criar e configurar automaticamente os recursos de rede virtual quando você cria um cluster AKS.
* Você pode criar e configurar manualmente os recursos da rede virtual e anexá-los a esses recursos ao criar o cluster AKS.

Embora haja suporte para recursos como pontos de extremidade de serviço ou UDRs com kubenet e CNI do Azure, as [políticas de suporte para AKs][support-policies] definem quais alterações você pode fazer. Por exemplo:

* Se você criar manualmente os recursos de rede virtual para um cluster AKS, terá suporte ao configurar seus próprios pontos de extremidade de serviço ou UDRs.
* Se a plataforma Azure cria automaticamente os recursos de rede virtual para o cluster AKS, não há suporte para alterar manualmente os recursos gerenciados por AKS para configurar seus próprios pontos de extremidade UDRs ou de serviço.

## <a name="ingress-controllers"></a>Controladores de entrada

Quando você cria um serviço de tipo de balanceador de carga, um recurso subjacente do Azure Load Balancer é criado. O balanceador de carga é configurado para distribuir o tráfego para o pods em seu serviço em uma determinada porta. O Balancer só funciona na camada 4-o serviço não reconhece os aplicativos reais e não pode fazer nenhuma outra consideração de roteamento.

*Controladores de entrada* funcionam na camada 7 e podem usar regras mais inteligentes para distribuir o tráfego do aplicativo. Um uso comum de um controlador de entrada é rotear o tráfego HTTP para aplicativos diferentes com base na URL de entrada.

![Diagrama mostrando o fluxo de tráfego de entrada em um cluster AKS][aks-ingress]

No AKS, você pode criar um recurso de entrada usando algo como NGINX ou usar o recurso de roteamento de aplicativo HTTP AKS. Quando você habilita o roteamento de aplicativos HTTP para um cluster AKS, a plataforma Azure cria o controlador de entrada e um controlador *DNS externo* . À medida que novos recursos de entrada são criados no kubernetes, os registros de DNS A necessários são criados em uma zona DNS específica do cluster. Para obter mais informações, consulte [implantar o roteamento de aplicativos http][aks-http-routing].

Outro recurso comum de entrada é a terminação de SSL/TLS. Em aplicativos Web grandes acessados via HTTPS, a terminação TLS pode ser tratada pelo recurso de entrada em vez de dentro do próprio aplicativo. Para fornecer geração e configuração automáticas de certificação TLS, você pode configurar o recurso de entrada para usar provedores como, por exemplo, criptografar. Para obter mais informações sobre como configurar um controlador de entrada NGINX com vamos criptografar, consulte [entrada e TLS][aks-ingress-tls].

Você também pode configurar o controlador de entrada para preservar o IP de origem do cliente em solicitações para contêineres no cluster AKS. Quando a solicitação de um cliente é roteada para um contêiner em seu cluster do AKS por meio do controlador de entrada, o IP de origem dessa solicitação não estará disponível para o contêiner de destino. Quando você habilita a *preservação de IP de origem do cliente*, o IP de origem do cliente está disponível no cabeçalho da solicitação em *X-forwardd-for*. Se você estiver usando a preservação de IP de origem do cliente em seu controlador de entrada, não poderá usar passagem SSL. A preservação de IP de origem do cliente e a passagem SSL podem ser usadas com outros serviços, como o tipo de *balanceador de carga* .

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para VMs, como os nós AKS. À medida que você cria serviços, como um balanceador de carga, a plataforma do Azure configura automaticamente quaisquer regras de grupo de segurança de rede necessárias. Não configure manualmente as regras do grupo de segurança de rede para filtrar o tráfego de pods em um cluster AKS. Defina todas as portas e encaminhamento necessários como parte de seus manifestos do serviço kubernetes e permita que a plataforma do Azure crie ou atualize as regras apropriadas. Você também pode usar as políticas de rede, conforme discutido na próxima seção, para aplicar automaticamente as regras de filtro de tráfego a pods.

## <a name="network-policies"></a>Diretivas de rede

Por padrão, todos os pods em um cluster AKS podem enviar e receber tráfego sem limitações. Para aumentar a segurança, talvez você queira definir regras que controlam o fluxo de tráfego. Os aplicativos de back-end geralmente são expostos apenas aos serviços necessários de front-end, ou os componentes de banco de dados só são acessíveis para as camadas de aplicativo que se conectam a eles.

A política de rede é um recurso kubernetes disponível no AKS que permite controlar o fluxo de tráfego entre pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos, namespace ou porta de tráfego atribuídos. Os grupos de segurança de rede são mais para os nós AKS, não com pods. O uso de políticas de rede é uma maneira mais adequada, nativa da nuvem, de controlar o fluxo de tráfego. Como os pods são criados dinamicamente em um cluster AKS, as diretivas de rede necessárias podem ser aplicadas automaticamente.

Para obter mais informações, consulte [proteger o tráfego entre pods usando as políticas de rede no serviço de kubernetes do Azure (AKs)][use-network-policies].

## <a name="next-steps"></a>Passos seguintes

Para começar a usar a rede AKS, crie e configure um cluster AKS com seus próprios intervalos de endereços IP usando [kubenet][aks-configure-kubenet-networking] ou [CNI do Azure][aks-configure-advanced-networking].

Para obter as práticas recomendadas associadas, consulte [práticas recomendadas para conectividade de rede e segurança no AKs][operator-best-practices-network].

Para obter informações adicionais sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Clusters e cargas de trabalho do kubernetes/AKS][aks-concepts-clusters-workloads]
- [Acesso e identidade de kubernetes/AKS][aks-concepts-identity]
- [Segurança do kubernetes/AKS][aks-concepts-security]
- [Armazenamento kubernetes/AKS][aks-concepts-storage]
- [Escala de kubernetes/AKS][aks-concepts-scale]

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
[support-policies]: support-policies.md
