---
title: Conceitos – rede nos serviços Kubernetess do Azure (AKS)
description: Saiba mais sobre a rede no AKS (serviço kubernetes do Azure), incluindo o kubenet e o Azure CNI Networking, controladores de entrada, balanceadores de carga e endereços IP estáticos.
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.author: mlearned
ms.openlocfilehash: 06825f184365cfc439167be15580eb19bf5ecb38
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084277"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicativos no serviço kubernetes do Azure (AKS)

Em uma abordagem de microserviços baseada em contêiner para o desenvolvimento de aplicativos, os componentes do aplicativo devem trabalhar juntos para processar suas tarefas. O kubernetes fornece vários recursos que habilitam essa comunicação do aplicativo. Você pode se conectar e expor aplicativos interna ou externamente. Para criar aplicativos altamente disponíveis, você pode balancear a carga de seus aplicativos. Aplicativos mais complexos podem exigir a configuração do tráfego de entrada para término de SSL/TLS ou roteamento de vários componentes. Por motivos de segurança, talvez você também precise restringir o fluxo do tráfego de rede em ou entre os pods e os nós.

Este artigo apresenta os principais conceitos que fornecem rede para seus aplicativos no AKS:

- [Serviços](#services)
- [Redes virtuais Azure](#azure-virtual-networks)
- [Controladores de ingressos](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso aos seus aplicativos ou para que os componentes do aplicativo se comuniquem entre si, o kubernetes fornece uma camada de abstração para a rede virtual. Os nós kubernetes são conectados a uma rede virtual e podem fornecer conectividade de entrada e saída para pods. O componente *de procuração de kube* funciona em cada nó para fornecer estas funcionalidades de rede.

Em Kubernetes, *os Serviços* logicamente agrupam cápsulas para permitir o acesso direto através de um endereço IP ou nome DNS e numa porta específica. Também pode distribuir o tráfego utilizando um *equilibrador*de carga. O encaminhamento mais complexo do tráfego de aplicações também pode ser alcançado com *controladores De entrada.* A segurança e a filtragem do tráfego de rede para cápsulas é possível com *as políticas*de rede kubernetes.

A plataforma Azure também ajuda a simplificar a rede virtual para clusters AKS. Quando você cria um balanceador de carga kubernetes, o recurso de balanceador de carga do Azure subjacente é criado e configurado. À medida que você abre portas de rede para pods, as regras de grupo de segurança de rede do Azure correspondentes são configuradas. Para o encaminhamento de aplicações HTTP, o Azure também pode configurar *dNS externos à* medida que novas rotas de ingresso estão configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração da rede para cargas de trabalho de aplicação, a Kubernetes utiliza os *Serviços* para agrupar logicamente um conjunto de cápsulas e fornecer conectividade de rede. Os seguintes tipos de serviço estão disponíveis:

- **Cluster IP** - Cria um endereço IP interno para utilização dentro do cluster AKS. Bom para aplicativos somente internos que dão suporte a outras cargas de trabalho no cluster.

    ![Diagrama mostrando o fluxo de tráfego IP do cluster em um cluster AKS][aks-clusterip]

- **NodePort** - Cria um mapeamento portuário no nó subjacente que permite que a aplicação seja acedida diretamente com o endereço IP do nó e porta.

    ![Diagrama mostrando o fluxo de tráfego NodePort em um cluster AKS][aks-nodeport]

- **LoadBalancer** - Cria um recurso de equilíbrio de carga Azure, configura um endereço IP externo e liga as cápsulas solicitadas ao conjunto de suporte de suporte do equilíbrio de carga. Para permitir que o tráfego dos clientes chegue ao aplicativo, as regras de balanceamento de carga são criadas nas portas desejadas. 

    ![Diagrama mostrando Load Balancer fluxo de tráfego em um cluster AKS][aks-loadbalancer]

    Para um controlo adicional e encaminhamento do tráfego de entrada, pode utilizar um [controlador Ingress](#ingress-controllers).

- **ExternalName** - Cria uma entrada específica de DNS para facilitar o acesso à aplicação.

O endereço IP para balanceadores de carga e serviços podem ser atribuídos dinamicamente ou você pode especificar um endereço IP estático existente a ser usado. Os endereços IP estáticos internos e externos podem ser atribuídos. Esse endereço IP estático existente geralmente está vinculado a uma entrada DNS.

Podem ser criados equilibradores de carga *internos* e *externos.* Os balanceadores de carga internos só recebem um endereço IP privado, portanto, eles não podem ser acessados pela Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais Azure

No AKS, você pode implantar um cluster que usa um dos dois modelos de rede a seguir:

- *Rede Kubenet* - Os recursos de rede são tipicamente criados e configurados à medida que o cluster AKS é implantado.
- Rede de rede de *contentores Azure (CNI)* - O cluster AKS está ligado aos recursos e configurações de rede virtuais existentes.

### <a name="kubenet-basic-networking"></a>Rede Kubenet (básica)

A opção de rede *kubenet* é a configuração padrão para a criação de cluster AKS. Com *kubenet,* os nós obtêm um endereço IP da subnet da rede virtual Azure. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente para a sub-rede da rede virtual do Azure dos nós. A conversão de endereços de rede (NAT) é então configurada para que o pods possa alcançar recursos na rede virtual do Azure. O endereço IP de origem do tráfego é NAT para o endereço IP primário do nó.

Os nódosos usam o plugin [kubenet][kubenet] Kubernetes. Você pode deixar a plataforma Azure criar e configurar as redes virtuais para você ou optar por implantar o cluster AKS em uma sub-rede de rede virtual existente. Novamente, somente os nós recebem um endereço IP roteável e o pods usam NAT para se comunicar com outros recursos fora do cluster AKS. Essa abordagem reduz consideravelmente o número de endereços IP que você precisa reservar em seu espaço de rede para que os pods usem.

Para mais informações, consulte a rede de rede De Modo Desinformação [para um cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Rede CNI do Azure (avançado)

Com o Azure CNI, cada pod Obtém um endereço IP da sub-rede e pode ser acessado diretamente. Esses endereços IP devem ser exclusivos em todo o espaço de rede e devem ser planejados antecipadamente. Cada nó tem um parâmetro de configuração para o número máximo de pods que oferece suporte. O número equivalente de endereços IP por nó é então reservado antecipadamente para esse nó. Essa abordagem requer mais planejamento, como pode levar ao esgotamento de endereço IP ou à necessidade de recriar clusters em uma sub-rede maior conforme as demandas do seu aplicativo crescem.

Os nós utilizam a interface de rede de [contentores Azure (CNI)][cni-networking] plugin Kubernetes.

![Diagrama mostrando dois nós com pontes conectando cada um a uma única VNet do Azure][advanced-networking-diagram]

Para mais informações, consulte [configure Azure CNI para um cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Comparar modelos de rede

O kubenet e o Azure CNI fornecem conectividade de rede para seus clusters AKS. No entanto, há vantagens e desvantagens para cada uma delas. Em um alto nível, as seguintes considerações se aplicam:

* **kubenet**
    * Conserva o espaço de endereço IP.
    * Usa o balanceador de carga interno ou externo do kubernetes para alcançar pods fora do cluster.
    * Você deve gerenciar e manter manualmente as rotas definidas pelo usuário (UDRs).
    * Máximo de 400 nós por cluster.
* **Azure CNI**
    * Os pods obtêm conectividade total de rede virtual e podem ser diretamente acessados por meio de seu endereço IP privado de redes conectadas.
    * Requer mais espaço de endereço IP.

Existem as seguintes diferenças de comportamento entre kubenet e CNI do Azure:

| Capacidade                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Implantar cluster em uma rede virtual nova ou existente                                            | Com suporte-UDRs aplicado manualmente | Suportado |
| Conectividade de pod de Pod                                                                         | Suportado | Suportado |
| Pod-conectividade da VM; VM na mesma rede virtual                                          | Funciona quando iniciado pelo Pod | Funciona de ambas as maneiras |
| Pod-conectividade da VM; VM na rede virtual emparelhada                                            | Funciona quando iniciado pelo Pod | Funciona de ambas as maneiras |
| Acesso local usando VPN ou rota expressa                                                | Funciona quando iniciado pelo Pod | Funciona de ambas as maneiras |
| Acesso a recursos protegidos por pontos de extremidade de serviço                                             | Suportado | Suportado |
| Expor serviços Kubernetess usando um serviço de balanceador de carga, um gateway de aplicativo ou um controlador de entrada | Suportado | Suportado |
| Zonas privadas e DNS do Azure padrão                                                          | Suportado | Suportado |

No que diz respeito ao DNS, com os plugins DNS kubenet e Azure CNI é oferecido pela CoreDNS, um conjunto de daemon em execução no AKS. Para obter mais informações sobre o CoreDNS em Kubernetes consulte [Personalização do Serviço DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). O CoreDNS está configurado por padrão para enviar domínios desconhecidos para os servidores DNS do nó, por outras palavras, para a funcionalidade DNS da Rede Virtual Azure onde o cluster AKS é implantado. Assim, o Azure DNS e as Zonas Privadas trabalharão para cápsulas em AKS.

### <a name="support-scope-between-network-models"></a>Escopo de suporte entre modelos de rede

Independentemente do modelo de rede que você usa, tanto o kubenet quanto o CNI do Azure podem ser implantados de uma das seguintes maneiras:

* A plataforma Azure pode criar e configurar automaticamente os recursos de rede virtual quando você cria um cluster AKS.
* Você pode criar e configurar manualmente os recursos da rede virtual e anexá-los a esses recursos ao criar o cluster AKS.

Embora capacidades como pontos finais de serviço ou UDRs sejam suportadas tanto com kubenet como Azure CNI, as políticas de [apoio para AKS][support-policies] definem as mudanças que você pode fazer. Por exemplo:

* Se criar manualmente os recursos de rede virtuais para um cluster AKS, é suportado ao configurar os seus próprios UDRs ou pontos finais de serviço.
* Se a plataforma Azure criar automaticamente os recursos de rede virtuais para o seu cluster AKS, não é suportado para alterar manualmente esses recursos geridos pela AKS para configurar os seus próprios UDRs ou pontos finais de serviço.

## <a name="ingress-controllers"></a>Controladores de entrada

Quando você cria um serviço de tipo de balanceador de carga, um recurso subjacente do Azure Load Balancer é criado. O balanceador de carga é configurado para distribuir o tráfego para o pods em seu serviço em uma determinada porta. O Balancer só funciona na camada 4-o serviço não reconhece os aplicativos reais e não pode fazer nenhuma outra consideração de roteamento.

Os controladores de *ingresso* funcionam na camada 7, e podem usar regras mais inteligentes para distribuir o tráfego de aplicações. Um uso comum de um controlador de entrada é rotear o tráfego HTTP para aplicativos diferentes com base na URL de entrada.

![Diagrama mostrando o fluxo de tráfego de entrada em um cluster AKS][aks-ingress]

No AKS, você pode criar um recurso de entrada usando algo como NGINX ou usar o recurso de roteamento de aplicativo HTTP AKS. Quando ativa o encaminhamento de aplicações HTTP para um cluster AKS, a plataforma Azure cria o controlador Ingress e um controlador *External-DNS.* À medida que novos recursos de entrada são criados no kubernetes, os registros de DNS A necessários são criados em uma zona DNS específica do cluster. Para mais informações, consulte [o encaminhamento][aks-http-routing]de aplicações HTTP .

Outro recurso comum de entrada é a terminação de SSL/TLS. Em aplicativos Web grandes acessados via HTTPS, a terminação TLS pode ser tratada pelo recurso de entrada em vez de dentro do próprio aplicativo. Para fornecer geração e configuração automáticas de certificação TLS, você pode configurar o recurso de entrada para usar provedores como, por exemplo, criptografar. Para obter mais informações sobre a configuração de um controlador NGINX Ingress com Let's Encrypt, consulte [Ingress e TLS][aks-ingress-tls].

Você também pode configurar o controlador de entrada para preservar o IP de origem do cliente em solicitações para contêineres no cluster AKS. Quando o pedido de um cliente é encaminhado para um contentor no seu cluster AKS através do seu controlador de entrada, o IP original de origem desse pedido não estará disponível para o recipiente-alvo. Quando ativa a preservação ip de *origem do cliente,* o IP de origem do cliente está disponível no cabeçalho de pedido sob *X-Forwarded-For*. Se estiver a utilizar a preservação ip de origem do cliente no seu controlador de ingresso, não pode utilizar a passagem da SSL. A preservação ip de origem do cliente e a passagem do SSL podem ser utilizadas com outros serviços, como o tipo *LoadBalancer.*

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para VMs, como os nós AKS. À medida que você cria serviços, como um balanceador de carga, a plataforma do Azure configura automaticamente quaisquer regras de grupo de segurança de rede necessárias. Não configure manualmente as regras do grupo de segurança de rede para filtrar o tráfego de pods em um cluster AKS. Defina todas as portas e encaminhamento necessários como parte de seus manifestos do serviço kubernetes e permita que a plataforma do Azure crie ou atualize as regras apropriadas. Você também pode usar as políticas de rede, conforme discutido na próxima seção, para aplicar automaticamente as regras de filtro de tráfego a pods.

## <a name="network-policies"></a>Políticas de rede

Por padrão, todos os pods em um cluster AKS podem enviar e receber tráfego sem limitações. Para aumentar a segurança, talvez você queira definir regras que controlam o fluxo de tráfego. Os aplicativos de back-end geralmente são expostos apenas aos serviços necessários de front-end, ou os componentes de banco de dados só são acessíveis para as camadas de aplicativo que se conectam a eles.

A política de rede é um recurso kubernetes disponível no AKS que permite controlar o fluxo de tráfego entre pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos, namespace ou porta de tráfego atribuídos. Os grupos de segurança de rede são mais para os nós AKS, não com pods. O uso de políticas de rede é uma maneira mais adequada, nativa da nuvem, de controlar o fluxo de tráfego. Como os pods são criados dinamicamente em um cluster AKS, as diretivas de rede necessárias podem ser aplicadas automaticamente.

Para obter mais informações, consulte o tráfego seguro entre as cápsulas utilizando políticas de [rede no Serviço Azure Kubernetes (AKS)][use-network-policies].

## <a name="next-steps"></a>Passos seguintes

Para começar com a rede AKS, crie e configure um cluster AKS com as suas próprias gamas de endereços IP utilizando [kubenet][aks-configure-kubenet-networking] ou [Azure CNI][aks-configure-advanced-networking].

Para as melhores práticas associadas, consulte [as melhores práticas para conectividade e segurança da rede em AKS][operator-best-practices-network].

Para obter informações adicionais sobre os principais conceitos de kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / ACESSO aks e identidade][aks-concepts-identity]
- [Kubernetes / Segurança AKS][aks-concepts-security]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala kubernetes / AKS][aks-concepts-scale]

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
