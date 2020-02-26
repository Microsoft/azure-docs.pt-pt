---
title: Conceitos - Networking in Azure Kubernetes Services (AKS)
description: Saiba mais sobre networking no Serviço Azure Kubernetes (AKS), incluindo rede kubenet e Azure CNI, controladores de ingresso, equilibradores de carga e endereços IP estáticos.
ms.topic: conceptual
ms.date: 02/28/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5800254ab44b5b0f1048ce2200f90c06a8d1666a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596169"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicações no Serviço Azure Kubernetes (AKS)

Numa abordagem de microserviços baseada em contentores para o desenvolvimento de aplicações, os componentes da aplicação devem trabalhar em conjunto para processar as suas tarefas. A Kubernetes fornece vários recursos que permitem esta comunicação de aplicação. Pode ligar-se e expor aplicações interna ou externamente. Para construir aplicações altamente disponíveis, pode carregar o equilíbrio das suas aplicações. Aplicações mais complexas podem exigir a configuração do tráfego de ingresso para a rescisão ou encaminhamento de sisl/TLS de vários componentes. Por razões de segurança, também pode ser necessário restringir o fluxo de tráfego de rede dentro ou entre cápsulas e nós.

Este artigo introduz os conceitos fundamentais que fornecem networking às suas aplicações no AKS:

- [Serviços](#services)
- [Redes virtuais Azure](#azure-virtual-networks)
- [Controladores de ingressos](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso às suas aplicações, ou para que os componentes da aplicação se comuniquem entre si, a Kubernetes fornece uma camada de abstração ao networking virtual. Os nós kubernetes estão ligados a uma rede virtual, e podem fornecer conectividade de entrada e saída para as cápsulas. O componente *de procuração de kube* funciona em cada nó para fornecer estas funcionalidades de rede.

Em Kubernetes, *os Serviços* logicamente agrupam cápsulas para permitir o acesso direto através de um endereço IP ou nome DNS e numa porta específica. Também pode distribuir o tráfego utilizando um *equilibrador*de carga. O encaminhamento mais complexo do tráfego de aplicações também pode ser alcançado com *controladores De entrada.* A segurança e a filtragem do tráfego de rede para cápsulas é possível com *as políticas*de rede kubernetes.

A plataforma Azure também ajuda a simplificar a rede virtual para clusters AKS. Quando se cria um equilibrador de carga Kubernetes, o recurso de equilíbrio de carga Azure subjacente é criado e configurado. À medida que abre portas de rede a cápsulas, as regras correspondentes do grupo de segurança da rede Azure estão configuradas. Para o encaminhamento de aplicações HTTP, o Azure também pode configurar *dNS externos à* medida que novas rotas de ingresso estão configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração da rede para cargas de trabalho de aplicação, a Kubernetes utiliza os *Serviços* para agrupar logicamente um conjunto de cápsulas e fornecer conectividade de rede. Estão disponíveis os seguintes tipos de Serviço:

- **Cluster IP** - Cria um endereço IP interno para utilização dentro do cluster AKS. Bom para aplicações internas que suportam outras cargas de trabalho dentro do cluster.

    ![Diagrama mostrando fluxo de tráfego IP cluster em um cluster AKS][aks-clusterip]

- **NodePort** - Cria um mapeamento portuário no nó subjacente que permite que a aplicação seja acedida diretamente com o endereço IP do nó e porta.

    ![Diagrama mostrando fluxo de tráfego nono deporto em um cluster AKS][aks-nodeport]

- **LoadBalancer** - Cria um recurso de equilíbrio de carga Azure, configura um endereço IP externo e liga as cápsulas solicitadas ao conjunto de suporte de suporte do equilíbrio de carga. Para permitir que o tráfego dos clientes chegue à aplicação, são criadas regras de equilíbrio de carga nas portas desejadas. 

    ![Diagrama mostrando fluxo de tráfego de balanceadores de carga em um cluster AKS][aks-loadbalancer]

    Para um controlo adicional e encaminhamento do tráfego de entrada, pode utilizar um [controlador Ingress](#ingress-controllers).

- **ExternalName** - Cria uma entrada específica de DNS para facilitar o acesso à aplicação.

O endereço IP para equilibradores e serviços de carga pode ser atribuído dinamicamente, ou pode especificar um endereço IP estático existente para usar. Podem ser atribuídos endereços IP estáticos internos e externos. Este endereço IP estático existente está ligado frequentemente a uma entrada DNS.

Podem ser criados equilibradores de carga *internos* e *externos.* Os equilibradores internos de carga só têm um endereço IP privado, pelo que não podem ser acedidos a partir da Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais Azure

No AKS, pode implementar um cluster que utiliza um dos dois modelos de rede seguintes:

- *Rede Kubenet* - Os recursos de rede são tipicamente criados e configurados à medida que o cluster AKS é implantado.
- Rede de rede de *contentores Azure (CNI)* - O cluster AKS está ligado aos recursos e configurações de rede virtuais existentes.

### <a name="kubenet-basic-networking"></a>Rede Kubenet (básica)

A opção de rede *kubenet* é a configuração padrão para a criação de cluster AKS. Com *kubenet,* os nós obtêm um endereço IP da subnet da rede virtual Azure. As cápsulas recebem um endereço IP de um espaço de endereço logicamente diferente para a subnet de rede virtual Azure dos nódosos. A tradução de endereços de rede (NAT) é então configurada para que as cápsulas possam chegar a recursos na rede virtual Azure. O endereço IP de origem do tráfego é NAT'd para o endereço IP primário do nó.

Os nódosos usam o plugin [kubenet][kubenet] Kubernetes. Pode permitir que a plataforma Azure crie e configure as redes virtuais para si, ou opte por implantar o seu cluster AKS numa subnet de rede virtual existente. Mais uma vez, apenas os nós recebem um endereço IP resaída, e as cápsulas usam NAT para comunicar com outros recursos fora do cluster AKS. Esta abordagem reduz consideravelmente o número de endereços IP que precisa de reservar no seu espaço de rede para que as cápsulas possam utilizar.

Para mais informações, consulte a rede de rede De Modo Desinformação [para um cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Rede Azure CNI (avançada)

Com o Azure CNI, cada cápsula recebe um endereço IP da subnet e pode ser acedida diretamente. Estes endereços IP devem ser únicos em todo o espaço da sua rede, e devem ser planeados com antecedência. Cada nó tem um parâmetro de configuração para o número máximo de cápsulas que suporta. O número equivalente de endereços IP por nó é então reservado à frente para esse nó. Esta abordagem requer mais planeamento, como pode levar à exaustão do endereço IP ou à necessidade de reconstruir clusters numa subrede maior à medida que a sua aplicação exige crescer.

Os nós utilizam a interface de rede de [contentores Azure (CNI)][cni-networking] plugin Kubernetes.

![Diagrama mostrando dois nós com pontes que ligam cada um a um único Azure VNet][advanced-networking-diagram]

Para mais informações, consulte [configure Azure CNI para um cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Comparar modelos de rede

Tanto kubenet como Azure CNI fornecem conectividade de rede para os seus clusters AKS. No entanto, há vantagens e desvantagens para cada um. A um nível elevado, aplicam-se as seguintes considerações:

* **kubenet**
    * Conserva espaço de endereço IP.
    * Utiliza o equilíbrio de carga interna ou externa kubernetes para chegar a cápsulas de fora do cluster.
    * Deve gerir e manter manualmente as rotas definidas pelo utilizador (UDRs).
    * Máximo de 400 nós por aglomerado.
* **Azure CNI**
    * Os pods obtêm conectividade virtual completa da rede e podem ser diretamente alcançados através do seu endereço IP privado a partir de redes conectadas.
    * Requer mais espaço de endereço IP.

Existem as seguintes diferenças de comportamento entre kubenet e Azure CNI:

| Capacidade                                                                                   | Rio Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Implementar cluster na rede virtual existente ou nova                                            | Suportado - UDRs aplicados manualmente | Suportado |
| Conectividade pod                                                                         | Suportado | Suportado |
| Conectividade Pod-VM; VM na mesma rede virtual                                          | Funciona quando iniciado por pod | Funciona nos dois sentidos |
| Conectividade Pod-VM; VM em rede virtual peered                                            | Funciona quando iniciado por pod | Funciona nos dois sentidos |
| Acesso no local utilizando VPN ou Rota Expresso                                                | Funciona quando iniciado por pod | Funciona nos dois sentidos |
| Acesso aos recursos garantidos por pontos finais de serviço                                             | Suportado | Suportado |
| Expor os serviços kubernetes usando um serviço de balanceadores de carga, App Gateway ou controlador de ingresso | Suportado | Suportado |
| DNS e Zonas Privadas De PredefinidoS Azure                                                          | Suportado | Suportado |

No que diz respeito ao DNS, com os plugins DNS kubenet e Azure CNI é oferecido pela CoreDNS, um conjunto de daemon em execução no AKS. Para obter mais informações sobre o CoreDNS em Kubernetes consulte [Personalização do Serviço DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). O CoreDNS está configurado por padrão para enviar domínios desconhecidos para os servidores DNS do nó, por outras palavras, para a funcionalidade DNS da Rede Virtual Azure onde o cluster AKS é implantado. Assim, o Azure DNS e as Zonas Privadas trabalharão para cápsulas em AKS.

### <a name="support-scope-between-network-models"></a>Âmbito de suporte entre modelos de rede

Independentemente do modelo de rede que utiliza, tanto kubenet como Azure CNI podem ser implantados de uma das seguintes formas:

* A plataforma Azure pode criar e configurar automaticamente os recursos de rede virtuais quando criar um cluster AKS.
* Pode criar e configurar manualmente os recursos de rede virtuais e anexar-se a esses recursos quando criar o seu cluster AKS.

Embora capacidades como pontos finais de serviço ou UDRs sejam suportadas tanto com kubenet como Azure CNI, as políticas de [apoio para AKS][support-policies] definem as mudanças que você pode fazer. Por exemplo:

* Se criar manualmente os recursos de rede virtuais para um cluster AKS, é suportado ao configurar os seus próprios UDRs ou pontos finais de serviço.
* Se a plataforma Azure criar automaticamente os recursos de rede virtuais para o seu cluster AKS, não é suportado para alterar manualmente esses recursos geridos pela AKS para configurar os seus próprios UDRs ou pontos finais de serviço.

## <a name="ingress-controllers"></a>Controladores de ingressos

Quando se cria um Serviço de Balancer tipo LoadBalancer, é criado um recurso de equilíbrio de carga Azure subjacente. O equilibrista de carga está configurado para distribuir o tráfego nas cápsulas do seu Serviço numa determinada porta. O LoadBalancer só funciona na camada 4 - o Serviço desconhece as aplicações reais, e não pode fazer quaisquer considerações adicionais de encaminhamento.

Os controladores de *ingresso* funcionam na camada 7, e podem usar regras mais inteligentes para distribuir o tráfego de aplicações. Um uso comum de um controlador Ingress é direcionar o tráfego HTTP para diferentes aplicações com base no URL de entrada.

![Diagrama mostrando fluxo de tráfego de Ingress em um cluster AKS][aks-ingress]

No AKS, pode criar um recurso Ingress utilizando algo como NGINX, ou utilizar a funcionalidade de encaminhamento de aplicações AKS HTTP. Quando ativa o encaminhamento de aplicações HTTP para um cluster AKS, a plataforma Azure cria o controlador Ingress e um controlador *External-DNS.* À medida que os novos recursos Ingress são criados em Kubernetes, os registos DNS A necessários são criados numa zona dNS específica do cluster. Para mais informações, consulte [o encaminhamento][aks-http-routing]de aplicações HTTP .

Outra característica comum da Ingress é a rescisão ssl/TLS. Nas grandes aplicações web acedidas via HTTPS, a rescisão de TLS pode ser tratada pelo recurso Ingress e não dentro da própria aplicação. Para fornecer a geração e configuração automáticas de certificação TLS, pode configurar o recurso Ingress para utilizar fornecedores como o Let's Encrypt. Para obter mais informações sobre a configuração de um controlador NGINX Ingress com Let's Encrypt, consulte [Ingress e TLS][aks-ingress-tls].

Também pode configurar o seu controlador de ingresso para preservar o IP de origem do cliente em pedidos de contentores no seu cluster AKS. Quando o pedido de um cliente é encaminhado para um contentor no seu cluster AKS através do seu controlador de entrada, o IP original de origem desse pedido não estará disponível para o recipiente-alvo. Quando ativa a preservação ip de *origem do cliente,* o IP de origem do cliente está disponível no cabeçalho de pedido sob *X-Forwarded-For*. Se estiver a utilizar a preservação ip de origem do cliente no seu controlador de ingresso, não pode utilizar a passagem da SSL. A preservação ip de origem do cliente e a passagem do SSL podem ser utilizadas com outros serviços, como o tipo *LoadBalancer.*

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para VMs, como os nós AKS. À medida que cria Serviços, como um LoadBalancer, a plataforma Azure confunde automaticamente quaisquer regras do grupo de segurança da rede que sejam necessárias. Não configure manualmente as regras do grupo de segurança da rede para filtrar o tráfego de cápsulas num cluster AKS. Defina quaisquer portas e reencaminhamento seleções necessárias como parte dos manifestos do Serviço Kubernetes e deixe a plataforma Azure criar ou atualizar as regras apropriadas. Também pode utilizar políticas de rede, como discutido na secção seguinte, para aplicar automaticamente as regras dos filtros de tráfego em cápsulas.

## <a name="network-policies"></a>Políticas de rede

Por padrão, todas as cápsulas de um cluster AKS podem enviar e receber tráfego sem limitações. Para uma maior segurança, pode querer definir regras que controlam o fluxo de tráfego. As aplicações backend são frequentemente apenas expostas a serviços frontend necessários, ou os componentes da base de dados só são acessíveis aos níveis de aplicação que lhes ligam.

A política de rede é uma funcionalidade Kubernetes disponível no AKS que permite controlar o fluxo de tráfego entre cápsulas. Pode optar por permitir ou negar o tráfego com base em configurações como etiquetas atribuídas, espaço de nome ou porta de tráfego. Os grupos de segurança da rede são mais para os nós aks, não para as cápsulas. A utilização das políticas de rede é uma forma mais adequada e nativa de nuvem para controlar o fluxo de tráfego. Como as cápsulas são criadas dinamicamente num cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente.

Para obter mais informações, consulte o tráfego seguro entre as cápsulas utilizando políticas de [rede no Serviço Azure Kubernetes (AKS)][use-network-policies].

## <a name="next-steps"></a>Passos seguintes

Para começar com a rede AKS, crie e configure um cluster AKS com as suas próprias gamas de endereços IP utilizando [kubenet][aks-configure-kubenet-networking] ou [Azure CNI][aks-configure-advanced-networking].

Para as melhores práticas associadas, consulte [as melhores práticas para conectividade e segurança da rede em AKS][operator-best-practices-network].

Para obter informações adicionais sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

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
