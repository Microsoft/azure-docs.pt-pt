---
title: Práticas recomendadas do operador – conectividade de rede dos serviços de Kubernetes no Azure (AKS)
description: Conheça as práticas recomendadas de operador de cluster para recursos de rede virtual e conectividade no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: iainfou
ms.openlocfilehash: aaa16245fada7fbccdd0865d973de2fa19970989
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176587"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Melhores práticas para conectividade de rede e segurança no Azure Kubernetes Service (AKS)

Como criar e gerir clusters no Azure Kubernetes Service (AKS), fornecer conectividade de rede para os nós e as aplicações. Esses recursos de rede incluem intervalos de endereços IP, balanceadores de carga e controladores de entrada. Para manter uma elevada qualidade de serviço para as suas aplicações, terá de planear e, em seguida, configurar esses recursos.

Este artigo de melhores práticas se concentra na conectividade de rede e segurança para os operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Compare os kubenet e modos de rede do Azure CNI no AKS
> * Planear a conectividade e endereçamento de IP necessários
> * Distribuir o tráfego através de balanceadores de carga, controladores de entrada ou um firewalls de aplicações web (WAF)
> * Ligar de forma segura a nós do cluster

## <a name="choose-the-appropriate-network-model"></a>Escolha o modelo de rede adequada

**Melhores diretrizes de práticas** – para integração com redes virtuais existentes ou redes no local, utilizamos a rede de Azure CNI no AKS. Esse modelo de rede também permite maior separação de controles e recursos num ambiente empresarial.

Redes virtuais fornecem a conectividade básica para os nós do AKS e clientes para aceder às suas aplicações. Existem duas formas diferentes de implementar clusters do AKS em redes virtuais:

* **Redes de Kubenet** -Azure gere os recursos de rede virtual, à medida que o cluster é implementado e utiliza o [kubenet] [ kubenet] Plug-in do Kubernetes.
* **Redes do Azure de CNI** – implementa numa rede virtual existente e utiliza o [Interface de rede contentor do Azure (CNI)] [ cni-networking] Plug-in do Kubernetes. Pods recebem IPs individuais que possa rotear a outros serviços de rede ou a recursos no local.

A Interface de rede de contentor (CNI) é um protocolo de fornecedor independente que permite que o tempo de execução do contentor fazer pedidos para um fornecedor de rede. CNI o Azure atribui endereços IP a pods e nós e fornece recursos de gerenciamento (IPAM) de endereço IP como ligar a redes virtuais do Azure existentes. Cada recurso de nó e pod recebe um endereço IP na rede virtual do Azure e não encaminhamento adicional é necessária para comunicar com outros serviços ou recursos.

![Diagrama que mostra dois nós com pontes ligar cada uma a uma VNet do Azure única](media/operator-best-practices-network/advanced-networking-diagram.png)

Para a maioria das implementações de produção, deve usar o sistema de rede do Azure CNI. Esse modelo de rede permite a separação de controle e gerenciamento de recursos. Da perspectiva de segurança, muitas vezes, equipas diferentes para gerenciar e proteger esses recursos. CNI do Azure permite-lhe ligar a recursos do Azure existentes, a recursos no local ou outros serviços diretamente através de endereços IP atribuídos a cada pod de rede.

Quando utiliza o sistema de rede do Azure CNI, o recurso de rede virtual está num grupo de recursos separados para o cluster do AKS. Delegar permissões para o principal de serviço do AKS aceder e gerir estes recursos. O principal de serviço utilizado pelo cluster do AKS tem de ter, pelo menos, [contribuinte de rede](../role-based-access-control/built-in-roles.md#network-contributor) permissões na sub-rede na sua rede virtual. Se pretender definir um [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de contribuinte de rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Para obter mais informações sobre a delegação de principal de serviço do AKS, consulte [delegar o acesso a outros recursos do Azure][sp-delegation].

À medida que cada nó e o pod receber o seu próprio endereço IP, planear os intervalos de endereços para as sub-redes AKS. A sub-rede tem de ser suficientemente grande para fornecer endereços IP para cada nó, os pods e recursos de rede que implementar. Cada cluster do AKS deve ser colocado na sua própria sub-rede. Para permitir a conetividade no local ou em modo de peering de redes no Azure, não utilize os intervalos de endereços IP que se sobreponha com recursos de rede existentes. Existem limites predefinidos para o número de pods que cada nó é executado com kubenet e funcionamento em rede do Azure CNI. Para lidar com escala a eventos ou atualizações de cluster, também terá de endereços IP adicionais disponíveis para utilização na sub-rede atribuída.

Para calcular o endereço IP necessário, veja [CNI do Azure de configurar o funcionamento em rede no AKS][advanced-networking].

### <a name="kubenet-networking"></a>Redes de Kubenet

Embora kubenet não exige que configure as redes virtuais antes do cluster é implementado, há desvantagens:

* Nós e pods são colocados em sub-redes IP diferentes. Reencaminhamento de IP e definida pelo utilizador encaminhamento (UDR) é utilizado para encaminhar o tráfego entre pods e nós. Esse roteamento adicionais pode reduzir o desempenho de rede.
* Ligações a redes no local de existente ou peering para outras redes virtuais do Azure podem ser complexas.

Kubenet é adequado para pequenas cargas de trabalho desenvolvimento ou teste, pois não terá de criar a rede virtual e sub-redes em separado do AKS cluster. Web sites simples com tráfego reduzido, ou para lift- and -shift de cargas de trabalho em contentores, também podem beneficiar a simplicidade de clusters do AKS implementado com a rede de kubenet. Para a maioria das implementações de produção, deve planear e utilizar as redes Azure CNI. Também pode [configurar seus próprios intervalos de endereços IP e redes virtuais através de kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuir o tráfego de entrada

**Melhores diretrizes de práticas** - para distribuir o tráfego HTTP ou HTTPS para seus aplicativos, utilize os recursos de entrada e de controladores. Controladores de entrada fornecem funcionalidades adicionais ao longo de um balanceador de carga do Azure normais e podem ser geridas como recursos nativos do Kubernetes.

Um balanceador de carga do Azure pode distribuir o tráfego de cliente para aplicações no cluster do AKS, mas o que ele compreende sobre o que o tráfego está limitada. Um recurso do Balanceador de carga funciona na camada 4 e distribui o tráfego com base no protocolo ou portas. A maioria dos aplicativos da web que utilizam HTTP ou HTTPS devem usar recursos de entrada Kuberenetes e controladores, o que funcionam na camada 7. Entrada pode distribuir o tráfego com base no URL da terminação de TLS/SSL do aplicativo e o identificador. Esta capacidade também reduz o número de endereços IP expor e mapear. Com um balanceador de carga, cada aplicativo precisa, normalmente, um endereço IP público atribuído e mapeado para o serviço do cluster do AKS. Com um recurso de entrada, um endereço IP único pode distribuir o tráfego para vários aplicativos.

![Diagrama que mostra o fluxo de tráfego de entrada num cluster do AKS](media/operator-best-practices-network/aks-ingress.png)

 Existem dois componentes para entrada:

 * Uma entrada *recursos*, e
 * Uma entrada *controlador*

O recurso de entrada é um manifesto YAML de `kind: Ingress` que define o anfitrião, certificados e regras para encaminhar o tráfego para serviços que são executados no cluster do AKS. O manifesto YAML de exemplo seguinte seria a distribuir o tráfego para *myapp.com* a um dos dois serviços, *blogservice* ou *storeservice*. O cliente é direcionado para um serviço ou a outra com base no URL que acedem.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Um controlador de entrada é um daemon que é executada num nó AKS e monitoriza os pedidos recebidos. Em seguida, o tráfego é distribuído com base em regras definidas no recurso da entrada a pedido. O controlador de entrada mais comum se baseia [NGINX]. AKS não o restringe a um controlador específico, pelo que pode utilizar outros controladores, como [Contour][contour], [HAProxy][haproxy], ou [ Traefik][traefik].

Existem muitos cenários de entrada, incluindo os seguintes guias de procedimentos:

* [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
* [Criar um controlador de entrada que utiliza uma rede interna, privada e o endereço IP][aks-ingress-internal]
* [Criar um controlador de entrada que utiliza os seus próprios certificados TLS][aks-ingress-own-tls]
* Criar um controlador de entrada que utiliza a encriptar vamos para gerar automaticamente certificados TLS [com um endereço IP público dinâmico] [ aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Proteger o tráfego com uma firewall de aplicações web (WAF)

**Melhores diretrizes de práticas** - para analisar o tráfego de entrada para ataques potenciais, utilize uma firewall de aplicações web (WAF) como [Barracuda WAF for Azure] [ barracuda-waf] ou Gateway de aplicação do Azure. Esses recursos mais avançados de rede também podem encaminhar o tráfego para além de apenas ligações HTTP e HTTPS ou básica terminação de SSL.

Um controlador de entrada que distribui o tráfego para serviços e aplicações, normalmente, é um recurso de Kubernetes no seu cluster do AKS. O controlador é executado como um daemon num nó AKS e consome alguns dos recursos do nó, tais como CPU, memória e largura de banda de rede. Em ambientes maiores, muitas vezes, pretende descarregar algumas deste encaminhamento de tráfego ou a terminação de TLS para um recurso de rede fora do cluster do AKS. Também queira analisar o tráfego de entrada para potenciais ataques.

![Uma firewall de aplicações web (WAF) como o Gateway de aplicação do Azure pode proteger e distribuir o tráfego para o seu cluster do AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Uma firewall de aplicações web (WAF) fornece uma camada adicional de segurança ao filtrar o tráfego de entrada. O Open Web Application Security Project (OWASP) fornece um conjunto de regras para deteção de ataques, como cross site scripting ou envenenamento de cookie. [O Gateway de aplicação do Azure] [ app-gateway] (atualmente em pré-visualização no AKS) é uma WAF que pode ser integrado com clusters do AKS para fornecer esses recursos de segurança, antes que o tráfego alcance o seu cluster do AKS e aplicações. Outras soluções de terceiros também executam estas funções, para que pode continuar a utilizar os investimentos existentes ou conhecimentos num determinado produto.

Recursos de entrada ou de Balanceador de carga continuam a executar no cluster do AKS para refinar ainda mais a distribuição de tráfego. Gateway de aplicação podem ser gerido centralmente como um controlador de entrada com uma definição do recurso. Para começar, [criar um controlador de entradas de Gateway de aplicação][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Fluxo de tráfego do controlo com as políticas de rede

**Melhores diretrizes de práticas** -utilizar políticas de rede para permitir ou negar o tráfego para os pods. Por predefinição, todo o tráfego é permitido entre pods dentro de um cluster. Para obter mais segurança, defina regras que limitam a comunicação de pod.

Política de rede (atualmente em pré-visualização no AKS) é uma funcionalidade de Kubernetes, que permite-lhe controlar o fluxo de tráfego entre os pods. Pode optar por permitir ou negar o tráfego com base em etiquetas de definições de atribuídas como, espaço de nomes ou porta de tráfego. O uso de diretivas de rede fornece uma forma de nativas da cloud para controlar o fluxo de tráfego. À medida pods são criados dinamicamente num cluster do AKS, as políticas de rede necessária podem ser aplicadas automaticamente. Não utilize grupos de segurança de rede do Azure para controlar o tráfego de pod de pod, utilize políticas de rede.

Para utilizar a política de rede, a funcionalidade tem de estar ativada ao criar um cluster do AKS. Não é possível ativar a política de rede num cluster do AKS existente. Planeie com antecedência para se certificar de que ativar política de rede em clusters e pode utilizá-las conforme necessário.

É criada uma política de rede como um recurso de Kubernetes com um manifesto YAML. As políticas são aplicadas para pods definidos e, em seguida, as regras de entrada ou saída definem a forma como o tráfego pode fluir. O exemplo seguinte aplica-se uma política de rede a pods com o *aplicação: back-end* etiqueta aplicada aos mesmos. A regra de entrada, em seguida, permite apenas tráfego de pods com o *aplicação: front-end* etiqueta:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Para começar a utilizar com políticas, consulte o artigo [proteger o tráfego entre pods através de políticas de rede no Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Ligar de forma segura para nós através de um anfitrião de bastião

**Melhores diretrizes de práticas** -não expõem a conectividade remota para os nós do AKS. Criar um anfitrião de bastião ou saltar caixa, numa rede virtual de gestão. Utilize o anfitrião de bastião para encaminhar o tráfego de forma segura no seu cluster do AKS para tarefas de gestão remota.

A maioria das operações no AKS podem ser concluídas usando as ferramentas de gestão do Azure ou através do servidor de API do Kubernetes. Nós do AKS não estão ligadas à Internet pública e só estão disponíveis numa rede privada. Para ligar a nós e executar a manutenção ou resolução de problemas, encaminhar as suas ligações através de um anfitrião de bastião ou saltar caixa. Este anfitrião deve ser numa rede virtual de gestão separados que está com segurança em modo de peering para a rede virtual de cluster do AKS.

![Ligar a nós do AKS com o anfitrião de bastião ou saltar caixa](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A rede de gestão para o anfitrião de bastião deve ser protegida demasiado. Utilize um [Azure ExpressRoute] [ expressroute] ou [gateway de VPN] [ vpn-gateway] para ligar a uma rede no local e controlar o acesso com segurança de rede grupos.

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se em segurança e conectividade de rede. Para obter mais informações sobre noções básicas de rede no Kubernetes, consulte [conceitos para aplicações no Azure Kubernetes Service (AKS) de rede][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
