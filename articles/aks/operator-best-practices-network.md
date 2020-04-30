---
title: Boas práticas para recursos de rede
titleSuffix: Azure Kubernetes Service
description: Conheça as melhores práticas do operador de cluster para recursos de rede virtuais e conectividade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 560a832821f5e5ff2fbbc2d66252945951d69511
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208062"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Melhores práticas para conectividade e segurança da rede no Azure Kubernetes Service (AKS)

À medida que cria e gere clusters no Serviço Azure Kubernetes (AKS), fornece conectividade de rede para os seus nós e aplicações. Estes recursos de rede incluem gamas de endereços IP, equilibradores de carga e controladores de ingresso. Para manter uma elevada qualidade de serviço para as suas aplicações, precisa de planear e, em seguida, configurar esses recursos.

Este artigo de boas práticas centra-se na conectividade da rede e segurança para os operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Compare os modos de rede Kubenet e Azure CNI no AKS
> * Plano para endereçamento ip e conectividade necessários
> * Distribuir tráfego utilizando equilibradores de carga, controladores de ingressos ou uma firewall de aplicação web (WAF)
> * Ligue-se de forma segura aos nós do cluster

## <a name="choose-the-appropriate-network-model"></a>Escolha o modelo de rede apropriado

**Orientação de boas práticas** - Para integração com redes virtuais existentes ou redes no local, utilize a rede Azure CNI em AKS. Este modelo de rede também permite uma maior separação de recursos e controlos num ambiente empresarial.

As redes virtuais proporcionam a conectividade básica para que os nossos nomes aks e clientes acedam às suas aplicações. Existem duas formas diferentes de implantar clusters AKS em redes virtuais:

* **Rede Kubenet** - Azure gere os recursos de rede virtuais à medida que o cluster é implantado e utiliza o plugin [kubenet][kubenet] Kubernetes.
* **Rede Azure CNI** - Implanta-se numa rede virtual existente e utiliza o plugin Kubernetes da Interface de Rede de [Contentores Azure (CNI).][cni-networking] Os pods recebem IPs individuais que podem encaminhar para outros serviços de rede ou recursos no local.

A Interface de Rede de Contentores (CNI) é um protocolo neutro do fornecedor que permite ao tempo de execução do contentor fazer pedidos a um fornecedor de rede. O Azure CNI atribui endereços IP a pods e nós, e fornece funcionalidades de gestão de endereços IP (IPAM) à medida que se conecta às redes virtuais azure existentes. Cada recurso de nó e pod recebe um endereço IP na rede virtual Azure, e não é necessário encaminhamento adicional para comunicar com outros recursos ou serviços.

![Diagrama mostrando dois nós com pontes que ligam cada um a um único Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

Para a maioria das implementações de produção, deve utilizar a rede Azure CNI. Este modelo de rede permite a separação do controlo e gestão dos recursos. Do ponto de vista da segurança, muitas vezes queremos que diferentes equipas gerem e protejam esses recursos. A rede Azure CNI permite ligar-se aos recursos azure existentes, recursos no local ou outros serviços diretamente através de endereços IP atribuídos a cada cápsula.

Quando utiliza a rede Azure CNI, o recurso de rede virtual encontra-se num grupo de recursos separado para o cluster AKS. Delegar permissões para o principal de serviço aks para aceder e gerir estes recursos. O principal de serviço utilizado pelo cluster AKS deve ter pelo menos permissões de Colaborador de [Rede](../role-based-access-control/built-in-roles.md#network-contributor) na subrede dentro da sua rede virtual. Se desejar definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de utilizar a função de Colaborador de Rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Para obter mais informações sobre a delegação principal do serviço AKS, consulte o [acesso do Delegado a outros recursos Do Iae.][sp-delegation] Em vez de um diretor de serviço, também pode utilizar o sistema de identidade gerida atribuída para permissões. Para mais informações, consulte [Use identidades geridas](use-managed-identity.md).

À medida que cada nó e casulo recebem o seu próprio endereço IP, planeie as gamas de endereços para as subredes AKS. A subnet deve ser suficientemente grande para fornecer endereços IP para cada nó, cápsulas e recursos de rede que você implementa. Cada cluster AKS deve ser colocado na sua própria sub-rede. Para permitir a conectividade com as redes no local ou com o peered em Azure, não utilize gamas de endereços IP que se sobreponham aos recursos de rede existentes. Existem limites padrão para o número de cápsulas que cada nó funciona com a rede Kubenet e Azure CNI. Para lidar com eventos de escala ou upgrades de cluster, também precisa de endereços IP adicionais disponíveis para utilização na sub-rede atribuída. Este espaço adicional de endereço suplicou especialmente se utilizar os recipientes do Windows Server, uma vez que essas piscinas de nós requerem uma atualização para aplicar as mais recentes correções de segurança. Para obter mais informações sobre os nós do Windows Server, consulte [a atualização de um conjunto][nodepool-upgrade]de nós em AKS .

Para calcular o endereço IP necessário, consulte a [rede Configure Azure CNI em AKS][advanced-networking].

### <a name="kubenet-networking"></a>Rede kubenet

Embora kubenet não exija que você instale as redes virtuais antes de o cluster ser implantado, existem desvantagens:

* Os nódosos e as cápsulas são colocados em diferentes subredes IP. O encaminhamento definido pelo utilizador (UDR) e o encaminhamento IP são utilizados para encaminhar o tráfego entre cápsulas e nós. Este encaminhamento adicional pode reduzir o desempenho da rede.
* As ligações às redes existentes no local ou ao espreitar outras redes virtuais do Azure podem ser complexas.

Kubenet é adequado para pequenas cargas de trabalho de desenvolvimento ou teste, uma vez que você não precisa criar a rede virtual e subnets separadamente do cluster AKS. Sites simples com tráfego baixo, ou para levantar e transferir cargas de trabalho para contentores, também podem beneficiar da simplicidade dos clusters AKS implantados com rede kubenet. Para a maioria das implementações de produção, deve planear e utilizar a rede Azure CNI. Também pode [configurar as suas próprias gamas de endereços IP e redes virtuais utilizando kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuir tráfego de ingressos

**Orientação das melhores práticas** - Para distribuir o tráfego HTTP ou HTTPS às suas aplicações, utilize recursos de ingresso e controladores. Os controladores de ingressos fornecem funcionalidades adicionais sobre um equilibrador de carga Azure regular, e podem ser geridos como recursos nativos da Kubernetes.

Um equilibrador de carga Azure pode distribuir o tráfego do cliente a aplicações no seu cluster AKS, mas é limitado no que entende sobre esse tráfego. Um recurso de equilíbrio de carga funciona na camada 4 e distribui tráfego com base em protocoloou portas. A maioria das aplicações web que usam HTTP ou HTTPS devem usar recursos e controladores de ingresso Kuberenetes, que funcionam na camada 7. A Entrada pode distribuir o tráfego com base no URL da aplicação e manusear a rescisão TLS/SSL. Esta capacidade também reduz o número de endereços IP que expõe e mapeia. Com um equilibrador de carga, cada aplicação normalmente precisa de um endereço IP público atribuído e mapeado para o serviço no cluster AKS. Com um recurso de ingresso, um único endereço IP pode distribuir tráfego a várias aplicações.

![Diagrama mostrando fluxo de tráfego de Ingress em um cluster AKS](media/operator-best-practices-network/aks-ingress.png)

 Existem dois componentes para ingresso:

 * Um *recurso*de ingresso, e
 * Um controlador de *ingresso*

O recurso ingress é um `kind: Ingress` manifesto YAML que define o hospedeiro, certificados e regras para encaminhar o tráfego para serviços que funcionam no seu cluster AKS. O seguinte exemplo manifesto YAML distribuiria tráfego por *myapp.com* a um de dois serviços, *blogservice* ou *serviço de loja.* O cliente é direcionado para um serviço ou outro com base no URL a que acede.

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

Um controlador de ingresso é um daemon que corre num nó AKS e observa para pedidos de entrada. O tráfego é então distribuído com base nas regras definidas no recurso de ingresso. O controlador de ingresso mais comum é baseado no [NGINX]. O AKS não o restringe a um controlador específico, por isso pode utilizar outros controladores como [Contour,][contour] [HAProxy][haproxy]ou [Traefik][traefik].

Os controladores de ingresso devem ser programados num nó Linux. Os nós do Windows Server não devem executar o controlador de entrada. Utilize um seletor de nó no seu manifesto YAML ou na implantação do gráfico Helm para indicar que o recurso deve ser executado num nó baseado em Linux. Para mais informações, consulte [Utilize os seletores de nó para controlar onde as cápsulas estão programadas no AKS][concepts-node-selectors].

Existem muitos cenários para ingresso, incluindo os seguintes guias como-a::

* [Criar um controlador básico de ingresso com conectividade de rede externa][aks-ingress-basic]
* [Criar um controlador de ingresso que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
* [Crie um controlador de ingresso que utilize os seus próprios certificados TLS][aks-ingress-own-tls]
* Crie um controlador de ingresso que utilize Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Tráfego seguro com uma firewall de aplicação web (WAF)

**Orientação de boas práticas** - Para digitalizar o tráfego de entrada para potenciais ataques, use uma firewall de aplicação web (WAF) como [Barracuda WAF para Azure][barracuda-waf] ou Azure Application Gateway. Estes recursos de rede mais avançados também podem encaminhar o tráfego para além das ligações HTTP e HTTPS ou a rescisão básica de TLS.

Um controlador de ingresso que distribui tráfego para serviços e aplicações é tipicamente um recurso Kubernetes no seu cluster AKS. O controlador funciona como um daemon num nó AKS, e consome alguns dos recursos do nó, tais como CPU, memória e largura de banda da rede. Em ambientes maiores, muitas vezes você quer descarregar parte deste encaminhamento de tráfego ou tLS de terminação para um recurso de rede fora do cluster AKS. Também quer digitalizar o tráfego para potenciais ataques.

![Uma firewall de aplicação web (WAF) como O Gateway da App Azure pode proteger e distribuir tráfego para o seu cluster AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Uma firewall de aplicação web (WAF) fornece uma camada adicional de segurança filtrando o tráfego de entrada. O Open Web Application Security Project (OWASP) fornece um conjunto de regras para assistir a ataques como scripts de sites cruzados ou envenenamento por cookies. O Portal de [Aplicações Azure][app-gateway] (atualmente em pré-visualização no AKS) é um WAF que pode integrar-se com clusters AKS para fornecer estas funcionalidades de segurança, antes que o tráfego atinja o seu cluster AKS e aplicações. Outras soluções de terceiros também desempenham estas funções, para que possa continuar a utilizar investimentos ou conhecimentos existentes num determinado produto.

Os recursos de equilíbrio de carga ou de ingresso continuam a funcionar no seu cluster AKS para aperfeiçoar ainda mais a distribuição de tráfego. O App Gateway pode ser gerido centralmente como um controlador de ingresso com uma definição de recursos. Para começar, [crie um controlador Application Gateway Ingress][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Controlar o fluxo de tráfego com políticas de rede

**Orientação de boas práticas** - Utilize políticas de rede para permitir ou negar o tráfego às cápsulas. Por padrão, todo o tráfego é permitido entre cápsulas dentro de um cluster. Para uma maior segurança, defina regras que limitem a comunicação do pod.

A política de rede é uma funcionalidade Kubernetes que permite controlar o fluxo de tráfego entre cápsulas. Pode optar por permitir ou negar o tráfego com base em configurações como etiquetas atribuídas, espaço de nome ou porta de tráfego. O uso de políticas de rede dá uma forma nativa de controlar o fluxo de tráfego. Como as cápsulas são criadas dinamicamente num cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente. Não utilize grupos de segurança da rede Azure para controlar o tráfego de pod-to-pod, use políticas de rede.

Para utilizar a política de rede, a funcionalidade deve ser ativada quando criar um cluster AKS. Não se pode ativar a política de rede num cluster AKS existente. Planeie com antecedência para garantir que permite a política de rede em clusters e pode usá-los conforme necessário. A política de rede só deve ser utilizada para nódos e pods baseados em Linux no AKS.

Uma política de rede é criada como um recurso Kubernetes usando um manifesto YAML. As políticas são aplicadas a pods definidos, depois as regras de ingresso ou de saída definem como o tráfego pode fluir. O exemplo seguinte aplica uma política de rede aos pods com a *aplicação:* etiqueta de backend aplicada a eles. A regra de ingresso só permite o tráfego de casulos com a app: etiqueta *frontend:*

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

Para começar com as políticas, consulte o tráfego seguro entre cápsulas utilizando políticas de [rede no Serviço Azure Kubernetes (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Ligue-se seguramente aos nódosos através de um anfitrião de bastião

**Orientação de boas práticas** - Não exponha conectividade remota aos seus nódosos AKS. Crie um anfitrião de bastião, ou caixa de salto, numa rede virtual de gestão. Utilize o anfitrião do bastião para encaminhar de forma segura o tráfego para o seu cluster AKS para tarefas de gestão remota.

A maioria das operações no AKS podem ser concluídas utilizando as ferramentas de gestão Azure ou através do servidor Kubernetes API. Os nós aks não estão ligados à internet pública, e só estão disponíveis numa rede privada. Para ligar a nós e realizar problemas de manutenção ou resolução de problemas, encaminhe as suas ligações através de um hospedeiro de bastião ou de uma caixa de salto. Este hospedeiro deve estar numa rede virtual de gestão separada que seja seguramente espreitada para a rede virtual do cluster AKS.

![Ligue-se aos nós AKS usando um hospedeiro de bastião, ou caixa de salto](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A rede de gestão do anfitrião do bastião também deve ser assegurada. Utilize um portal [Azure ExpressRoute][expressroute] ou [VPN][vpn-gateway] para ligar a uma rede no local e controlar o acesso utilizando grupos de segurança de rede.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se na conectividade da rede e segurança. Para obter mais informações sobre os fundamentos da rede em Kubernetes, consulte conceitos de [rede para aplicações no Serviço Azure Kubernetes (AKS)][aks-concepts-network]

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
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool