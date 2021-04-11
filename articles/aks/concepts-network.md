---
title: Conceitos - Networking in Azure Kubernetes Services (AKS)
description: Saiba mais sobre networking no Serviço Azure Kubernetes (AKS), incluindo rede kubenet e Azure CNI, controladores de entrada, equilibradores de carga e endereços IP estáticos.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105345"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Conceitos de rede para aplicações no Serviço Azure Kubernetes (AKS)

Numa abordagem baseada em contentores, os microserviços para o desenvolvimento de aplicações, os componentes da aplicação trabalham em conjunto para processar as suas tarefas. A Kubernetes fornece vários recursos que permitem esta cooperação:
* Pode ligar e expor aplicações interna ou externamente. 
* Pode construir aplicações altamente disponíveis, equilibrando as suas aplicações. 
* Para as suas aplicações mais complexas, pode configurar o tráfego de entrada para a rescisão de SSL/TLS ou encaminhamento de vários componentes. 
* Por razões de segurança, pode restringir o fluxo de tráfego de rede para dentro ou entre cápsulas e nós.

Este artigo introduz os conceitos fundamentais que fornecem networking às suas aplicações em AKS:

- [Serviços](#services)
- [Redes virtuais do Azure](#azure-virtual-networks)
- [Controladores de entrada](#ingress-controllers)
- [Políticas de rede](#network-policies)

## <a name="kubernetes-basics"></a>Noções básicas do Kubernetes

Para permitir o acesso às suas aplicações ou entre componentes de aplicações, a Kubernetes fornece uma camada de abstração para o networking virtual. Os nós kubernetes conectam-se a uma rede virtual, proporcionando conectividade de entrada e saída para cápsulas. O componente *kube-proxy* funciona em cada nó para fornecer estas funcionalidades de rede.

Em Kubernetes:
* *Os serviços* logicamente agrupam cápsulas para permitir o acesso direto a uma porta específica através de um endereço IP ou nome DNS. 
* Pode distribuir o tráfego através de um *equilibrador de carga.* 
* O encaminhamento mais complexo do tráfego de aplicações também pode ser alcançado com *controladores ingress*. 
* A segurança e a filtragem do tráfego de rede para cápsulas é possível com *as políticas de rede* Kubernetes .

A plataforma Azure também simplifica a rede virtual para clusters AKS. Quando cria um equilibrador de carga Kubernetes, também cria e configura o recurso de balançador de carga Azure subjacente. À medida que abre portas de rede a cápsulas, as respetivas regras do grupo de segurança da rede Azure estão configuradas. Para o encaminhamento de aplicações HTTP, o Azure também pode configurar *DNS externos* à medida que novas rotas de entrada são configuradas.

## <a name="services"></a>Serviços

Para simplificar a configuração da rede para cargas de trabalho de aplicação, a Kubernetes utiliza *serviços* para agrupar logicamente um conjunto de cápsulas e fornecer conectividade de rede. Estão disponíveis os seguintes tipos de Serviço:

- **Cluster IP** 
  
  Cria um endereço IP interno para utilização dentro do cluster AKS. Bom para aplicações internas que suportam outras cargas de trabalho dentro do cluster.

    ![Diagrama mostrando fluxo de tráfego IP cluster em um cluster AKS][aks-clusterip]

- **NodePort** 

  Cria um mapeamento de porta no nó subjacente que permite que a aplicação seja acedida diretamente com o endereço IP do nó e a porta.

    ![Diagrama mostrando fluxo de tráfego nodePort em um cluster AKS][aks-nodeport]

- **LoadBalancer** 

  Cria um recurso de balançador de carga Azure, configura um endereço IP externo e liga as cápsulas solicitadas ao pool de backend do balançador de carga. Para permitir que o tráfego dos clientes atinja a aplicação, são criadas regras de equilíbrio de carga nas portas desejadas. 

    ![Diagrama mostrando fluxo de tráfego do balanceador de carga em um cluster AKS][aks-loadbalancer]

    Para um controlo extra e encaminhamento do tráfego de entrada, pode utilizar um [controlador Ingress](#ingress-controllers).

- **Nome externo** 

  Cria uma entrada específica de DNS para facilitar o acesso à aplicação.

Ou os equilibradores de carga e o endereço IP dos serviços podem ser atribuídos dinamicamente, ou pode especificar um endereço IP estático existente. Pode atribuir endereços IP estáticos internos e externos. Os endereços IP estáticos existentes estão frequentemente ligados a uma entrada de DNS.

Pode criar equilibradores de carga *internos* e *externos.* Os equilibradores internos de carga são apenas atribuídos a um endereço IP privado, pelo que não podem ser acedidos a partir da Internet.

## <a name="azure-virtual-networks"></a>Redes virtuais do Azure

No AKS, pode implementar um cluster que utilize um dos dois modelos de rede seguintes:

- *Rede Kubenet*

  Os recursos de rede são tipicamente criados e configurados à medida que o cluster AKS é implantado.

- *Rede de rede de rede de contentores Azure (CNI)*
 
  o cluster do AKS está associado aos recursos e configurações existentes da rede virtual.

### <a name="kubenet-basic-networking"></a>Rede Kubenet (básica)

A opção de rede *kubenet* é a configuração padrão para a criação de cluster AKS. Com *kubenet:*
1. Os nós recebem um endereço IP da sub-rede virtual Azure. 
1. Os pods recebem um endereço IP de um espaço de endereço logicamente diferente do sub-rede de rede virtual Azure dos nós. 
1. A tradução de endereços de rede (NAT) é configurada para que os pods possam chegar aos recursos na rede virtual do Azure. 
1. O endereço IP de origem do tráfego é traduzido para o endereço IP primário do nó.

Os nós usam o [plugin kubenetes][kubenet] kubernetes. Pode:
* Deixe a plataforma Azure criar e configurar as redes virtuais para si, ou 
* Opte por implantar o seu cluster AKS numa sub-rede de rede virtual existente. 

Lembre-se, apenas os nós recebem um endereço IP roteável. As cápsulas usam o NAT para comunicar com outros recursos fora do cluster AKS. Esta abordagem reduz o número de endereços IP que precisa de reservar no espaço da sua rede para as cápsulas utilizarem.

Para obter mais informações, consulte [a rede de kubenet Configure para um cluster AKS][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Rede Azure CNI (avançada)

Com o Azure CNI, cada pod obtém um endereço IP da sub-rede e pode ser acedido diretamente. Estes endereços IP devem ser planeados com antecedência e únicos em todo o seu espaço de rede. Cada nó tem um parâmetro de configuração para o número máximo de cápsulas que suporta. O número equivalente de endereços IP por nó é então reservado à frente. Sem planeamento, esta abordagem pode levar à exaustão do endereço IP ou à necessidade de reconstruir clusters numa sub-rede maior à medida que as suas exigências de aplicação aumentam.

Ao contrário do kubenet, o tráfego para pontos finais na mesma rede virtual não é NAT'd para o IP primário do nó. O endereço de origem para o tráfego dentro da rede virtual é o pod IP. O tráfego externo à rede virtual ainda é DETs para o IP primário do nó.

Os nós usam o plugin [Azure CNI][cni-networking] Kubernetes.

![Diagrama mostrando dois nós com pontes conectando cada um a um único Azure VNet][advanced-networking-diagram]

Para obter mais informações, consulte [o Configure Azure CNI para um cluster AKS][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Comparar modelos de rede

Tanto o Kubenet como o Azure CNI fornecem conectividade de rede para os seus clusters AKS. No entanto, há vantagens e desvantagens para cada um. A um nível elevado, aplicam-se as seguintes considerações:

* **kubenet**
    * Conserva o espaço de endereço IP.
    * Utiliza o equilibrador de carga interno ou externo kubernetes para chegar a cápsulas de fora do cluster.
    * Gere manualmente e mantém rotas definidas pelo utilizador (UDRs).
    * Máximo de 400 nós por aglomerado.
* **Azure CNI**
    * Os pods obtêm conectividade de rede virtual completa e podem ser alcançados diretamente através do seu endereço IP privado a partir de redes conectadas.
    * Requer mais espaço para endereço IP.

Existem as seguintes diferenças de comportamento entre kubenet e Azure CNI:

| Funcionalidade                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Implementar cluster na rede virtual existente ou nova                                            | Suportado - UDRs aplicados manualmente | Suportado |
| Conectividade pod-pod                                                                         | Suportado | Suportado |
| Conectividade Pod-VM; VM na mesma rede virtual                                          | Funciona quando iniciado por vagem | Funciona para os dois lados |
| Conectividade Pod-VM; VM em rede virtual espreitada                                            | Funciona quando iniciado por vagem | Funciona para os dois lados |
| Acesso no local através da VPN ou Rota Expressa                                                | Funciona quando iniciado por vagem | Funciona para os dois lados |
| Acesso a recursos garantidos por pontos finais de serviço                                             | Suportado | Suportado |
| Expor serviços Kubernetes usando um serviço de balanceador de carga, App Gateway ou controlador de entrada | Suportado | Suportado |
| DNS e Zonas Privadas de Azure Padrão                                                          | Suportado | Suportado |

No que diz respeito ao DNS, com plugins de Kubenet e Azure CNI, os plugins DNS são oferecidos pela CoreDNS, uma implementação em execução em AKS com o seu próprio autoescalador. Para obter mais informações sobre o CoreDNS em Kubernetes, consulte [o Serviço DNS de Personalização](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). O CoreDNS por padrão está configurado para encaminhar domínios desconhecidos para a funcionalidade DNS da Rede Virtual Azure onde o cluster AKS é implantado. Assim, O Azure DNS e Private Zones trabalharão para cápsulas que funcionam em AKS.

### <a name="support-scope-between-network-models"></a>Âmbito de suporte entre modelos de rede

Qualquer que seja o modelo de rede que utilize, tanto o Kubenet como o Azure CNI podem ser implementados de uma das seguintes formas:

* A plataforma Azure pode criar e configurar automaticamente os recursos de rede virtuais quando cria um cluster AKS.
* Pode criar e configurar manualmente os recursos de rede virtuais e anexar-se a esses recursos quando criar o seu cluster AKS.

Embora capacidades como pontos finais de serviço ou UDRs sejam suportadas tanto com kubenet como com Azure CNI, as [políticas de suporte para AKS][support-policies] definem as mudanças que pode fazer. Por exemplo:

* Se criar manualmente os recursos de rede virtual para um cluster AKS, é apoiado ao configurar as suas próprias UDRs ou pontos finais de serviço.
* Se a plataforma Azure criar automaticamente os recursos de rede virtual para o seu cluster AKS, não pode alterar manualmente esses recursos geridos pela AKS para configurar os seus próprios UDRs ou pontos finais de serviço.

## <a name="ingress-controllers"></a>Controladores de entrada

Quando cria um Serviço do tipo LoadBalancer, também cria um recurso de balançador de carga Azure subjacente. O balançador de carga está configurado para distribuir o tráfego para as cápsulas do seu Serviço numa determinada porta. 

O LoadBalancer só funciona na camada 4. Na camada 4, o Serviço desconhece as aplicações reais, e não pode fazer mais considerações de encaminhamento.

*Os controladores ingress* funcionam na camada 7, e podem usar regras mais inteligentes para distribuir o tráfego de aplicações. Os controladores ingress normalmente encaminham o tráfego HTTP para diferentes aplicações com base no URL de entrada.

![Diagrama mostrando fluxo de tráfego ingress num cluster AKS][aks-ingress]

### <a name="create-an-ingress-resource"></a>Criar um recurso ingresss
Em AKS, pode criar um recurso Ingress utilizando o NGINX, uma ferramenta semelhante ou a função de encaminhamento de aplicações AKS HTTP. Quando ativa o encaminhamento de aplicações HTTP para um cluster AKS, a plataforma Azure cria o controlador Ingress e um controlador *de DNS Externos.* À medida que novos recursos Ingress são criados em Kubernetes, os registos DNS A necessários são criados numa zona de DNS específica para clusters. 

Para obter mais informações, consulte [implementar o encaminhamento de aplicações HTTP][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Controlador de Entrada de Gateway de Aplicação (AGIC)

Com o addon do Controlador de Entrada de Gateway de Aplicação (AGIC), os clientes da AKS aproveitam o equilibrador de carga de nível 7 do Gateway de Aplicação da Azure para expor o software em nuvem à Internet. A AGIC monitoriza o cluster Kubernetes anfitrião e atualiza continuamente um Gateway de Aplicações, expondo serviços selecionados à Internet. 

Para saber mais sobre o addon AGIC para AKS, veja [o que é o Controlador de Entradas de Gateway de Aplicação?][agic-overview]

### <a name="ssltls-termination"></a>Terminação SSL/TLS

A rescisão de SSL/TLS é outra característica comum da Ingress. Nas grandes aplicações web acedidas via HTTPS, o recurso Ingress trata da rescisão do TLS e não dentro da própria aplicação. Para fornecer a geração e configuração automáticas de certificação TLS, pode configurar o recurso Ingress para utilizar fornecedores como "Vamos Encriptar". 

Para obter mais informações sobre a configuração de um controlador NGINX Ingress com Let's Encrypt, consulte [Ingress e TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>Preservação IP fonte de cliente

Configure o seu controlador de entrada para preservar o IP de origem do cliente em pedidos de contentores no seu cluster AKS. Quando o seu controlador de entrada encaminha o pedido de um cliente para um contentor no seu cluster AKS, o IP de origem original desse pedido não está disponível para o contentor-alvo. Quando ativa a *preservação ip de fonte* de cliente, o IP de origem para o cliente está disponível no cabeçalho de pedido sob *X-Forwarded-For*. 

Se estiver a utilizar a preservação IP de fonte de cliente no seu controlador de entrada, não pode utilizar a passagem do TLS. A preservação IP de fonte de cliente e o pass-through TLS podem ser utilizados com outros serviços, como o tipo *LoadBalancer.*

## <a name="network-security-groups"></a>Grupos de segurança de rede

Um grupo de segurança de rede filtra o tráfego para VMs como os nós AKS. Ao criar Serviços, como um LoadBalancer, a plataforma Azure configura automaticamente quaisquer regras necessárias do grupo de segurança de rede. 

Não é necessário configurar manualmente as regras do grupo de segurança da rede para filtrar o tráfego de cápsulas num cluster AKS. Basta definir quaisquer portas necessárias e encaminhamento como parte dos seus manifestos de Serviço Kubernetes. Deixe a plataforma Azure criar ou atualizar as regras apropriadas. 

Também pode utilizar políticas de rede para aplicar automaticamente as regras do filtro de tráfego em cápsulas.

## <a name="network-policies"></a>Políticas de rede

Por padrão, todas as cápsulas de um cluster AKS podem enviar e receber tráfego sem limitações. Para uma maior segurança, defina regras que controlem o fluxo de tráfego, como:
* As aplicações de backend só são expostas aos serviços frontais necessários. 
* Os componentes da base de dados só estão acessíveis aos níveis de aplicação que lhes se ligam.

A política de rede é uma funcionalidade kubernetes disponível em AKS que permite controlar o fluxo de tráfego entre cápsulas. Você permite ou nega tráfego para o pod com base em configurações como etiquetas atribuídas, espaço de nome ou porta de tráfego. Embora os grupos de segurança da rede sejam melhores para os nós AKS, as políticas de rede são uma forma mais adequada, nativa da nuvem para controlar o fluxo de tráfego para as cápsulas. Como as cápsulas são criadas dinamicamente num cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente.

Para obter mais informações, consulte [o tráfego seguro entre cápsulas utilizando as políticas de rede no Serviço Azure Kubernetes (AKS)][use-network-policies].

## <a name="next-steps"></a>Passos seguintes

Para começar com a rede AKS, crie e configuure um cluster AKS com as suas próprias gamas de endereços IP utilizando [kubenet][aks-configure-kubenet-networking] ou [Azure CNI][aks-configure-advanced-networking].

Para as melhores práticas associadas, consulte [as melhores práticas para a conectividade da rede e segurança em AKS][operator-best-practices-network].

Para obter mais informações sobre os conceitos core Kubernetes e AKS, consulte os seguintes artigos:

- [Aglomerados de Kubernetes / AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Acesso e identidade kubernetes / AKS][aks-concepts-identity]
- [Segurança Kubernetes / AKS][aks-concepts-security]
- [Armazenamento Kubernetes / AKS][aks-concepts-storage]
- [Escala de Kubernetes / AKS][aks-concepts-scale]

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
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
