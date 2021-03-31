---
title: Melhores práticas para recursos de rede
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do operador de cluster para recursos de rede virtuais e conectividade no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 2bd332dbf9412f5c42e77b14ada3aab67ec8b66a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508593"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Melhores práticas para conectividade e segurança da rede no Azure Kubernetes Service (AKS)

Ao criar e gerir clusters no Serviço Azure Kubernetes (AKS), fornece conectividade de rede para os seus nós e aplicações. Estes recursos de rede incluem intervalos de endereços IP, equilibradores de carga e controladores de entrada. Para manter uma elevada qualidade de serviço para as suas aplicações, é necessário planear e, em seguida, configurar estes recursos.

Este artigo de boas práticas centra-se na conectividade da rede e na segurança dos operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Compare os modos de rede de kubenet e Azure Container (CNI) em AKS
> * Plano para a morada ip necessária e conectividade
> * Distribuir tráfego utilizando equilibradores de carga, controladores de entrada ou uma firewall de aplicação web (WAF)
> * Ligar-se de forma segura aos nosdes de cluster

## <a name="choose-the-appropriate-network-model"></a>Escolha o modelo de rede apropriado

**Orientação de boas práticas** - Para integração com redes virtuais existentes ou redes no local, utilize a rede CNI Azure em AKS. Este modelo de rede também permite uma maior separação de recursos e controlos num ambiente empresarial.

As redes virtuais fornecem a conectividade básica para os nós AKS e os clientes acederem às suas aplicações. Existem duas maneiras diferentes de implantar clusters AKS em redes virtuais:

* **Rede Kubenet** - Azure gere os recursos de rede virtuais à medida que o cluster é implantado e utiliza o [plugin kubenetes][kubenet] Kubernetes.
* **Rede CNI Azure** - Implementa-se numa rede virtual e utiliza o plugin kubernetes [da Interface de Rede de Contentores Azure (CNI).][cni-networking] Os pods recebem IPs individuais que podem encaminhar-se para outros serviços de rede ou recursos no local.

Para as implementações de produção, tanto a Kubenet como a Azure CNI são opções válidas.

### <a name="cni-networking"></a>Rede CNI

A Interface de Rede de Contentores (CNI) é um protocolo neutro do fornecedor que permite ao contentor fazer pedidos a um fornecedor de rede. O Azure CNI atribui endereços IP a pods e nós e fornece funcionalidades de gestão de endereços IP (IPAM) à medida que se conecta às redes virtuais Azure existentes. Cada nó e recurso de pod recebe um endereço IP na rede virtual Azure, e não é necessário um encaminhamento extra para comunicar com outros recursos ou serviços.

![Diagrama mostrando dois nós com pontes conectando cada um a um único Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

Um benefício notável da rede CNI Azure para produção é que o modelo de rede permite a separação do controlo e gestão dos recursos. Do ponto de vista da segurança, muitas vezes queremos que diferentes equipas gerem e garantam esses recursos. A rede CNI Azure permite-lhe ligar-se aos recursos Azure existentes, recursos no local ou outros serviços diretamente através de endereços IP atribuídos a cada cápsula.

Quando utiliza a rede CNI Azure, o recurso de rede virtual encontra-se num grupo de recursos separado do cluster AKS. Delegar permissões para que a identidade do cluster AKS aceda e gere estes recursos. A identidade do cluster utilizada pelo cluster AKS deve ter pelo menos permissões [de Contribuinte de Rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede dentro da sua rede virtual. Se desejar definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de utilizar a função de contribuinte de rede incorporada, são necessárias as seguintes permissões:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Por padrão, a AKS utiliza uma identidade gerida para a sua identidade de cluster, mas em vez disso tem a opção de usar um principal de serviço. Para obter mais informações sobre a delegação principal do serviço AKS, consulte [o acesso do delegado a outros recursos da Azure.][sp-delegation] Para obter mais informações sobre identidades geridas, consulte [utilização de identidades geridas](use-managed-identity.md).

À medida que cada nó e pod recebem o seu próprio endereço IP, planeie os intervalos de endereços para as sub-redes AKS. A sub-rede deve ser suficientemente grande para fornecer endereços IP para cada nó, cápsulas e recursos de rede que implementar. Cada cluster AKS deve ser colocado na sua própria sub-rede. Para permitir a conectividade às redes no local ou nas redes espreitadas em Azure, não utilize intervalos de endereços IP que se sobreponham aos recursos de rede existentes. Existem limites padrão para o número de cápsulas que cada nó funciona com a rede kubenet e Azure CNI. Para lidar com eventos de escala ou atualizações de clusters, também precisa de endereços IP extra disponíveis para utilização na sub-rede atribuída. Este espaço de endereço extra é especialmente importante se utilizar recipientes do Windows Server, uma vez que esses conjuntos de nós requerem uma atualização para aplicar as mais recentes correções de segurança. Para obter mais informações sobre os nós do Windows Server, consulte [a atualização de um conjunto de nós em AKS][nodepool-upgrade].

Para calcular o endereço IP necessário, consulte [a rede CNI Configure CNI em AKS][advanced-networking].

Quando cria um cluster com rede CNI Azure, especifica outras gamas de endereços para utilização pelo cluster, como o endereço da ponte Docker, o IP de serviço DNS e a gama de endereços de serviço. Em geral, estes intervalos de endereços não devem sobrepor-se uns aos outros e não devem sobrepor-se a quaisquer redes associadas ao cluster, incluindo quaisquer redes virtuais, sub-redes, no local e redes esprevadas. Para obter os detalhes específicos em torno dos limites e dimensionamento para estas gamas de endereços, consulte [a rede Configure Azure CNI em AKS][advanced-networking].

### <a name="kubenet-networking"></a>Rede Kubenet

Embora o Kubenet não exija que crie as redes virtuais antes do cluster ser implantado, existem desvantagens:

* Os nós e as cápsulas são colocados em diferentes sub-redes IP. O encaminhamento definido do utilizador (UDR) e o encaminhamento IP é utilizado para encaminhar o tráfego entre cápsulas e nós. Este encaminhamento extra pode reduzir o desempenho da rede.
* As ligações às redes existentes no local ou olhando para outras redes virtuais Azure podem ser complexas.

A Kubenet é adequada para pequenas cargas de trabalho de desenvolvimento ou teste, uma vez que não precisa de criar a rede virtual e sub-redes separadamente do cluster AKS. Sites simples com tráfego baixo, ou para levantar e transferir cargas de trabalho em contentores, também podem beneficiar da simplicidade dos clusters AKS implantados com rede kubenet. Para a maioria das implementações de produção, deve planear e utilizar a rede Azure CNI.

Também pode [configurar as suas próprias gamas de endereços IP e redes virtuais utilizando kubenet.][aks-configure-kubenet-networking] Semelhantes à rede Azure CNI, estes intervalos de endereços não devem sobrepor-se uns aos outros e não devem sobrepor-se a quaisquer redes associadas ao cluster, incluindo quaisquer redes virtuais, sub-redes, no local e redes de olhos. Para obter os detalhes específicos em torno de limites e dimensionamento para estas gamas de endereços, consulte [utilizar a rede kubenet com os seus próprios intervalos de endereço IP em AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuir tráfego de entrada

**Orientação de boas práticas** - Para distribuir tráfego HTTP ou HTTPS às suas aplicações, utilize recursos e controladores de entrada. Os controladores ingress fornecem funcionalidades extras sobre um balanceador de carga Azure regular, e podem ser geridos como recursos nativos de Kubernetes.

Um equilibrador de carga Azure pode distribuir o tráfego do cliente para aplicações no seu cluster AKS, mas é limitado no que entende sobre esse tráfego. Um recurso de balanceador de carga funciona na camada 4 e distribui tráfego com base em protocolos ou portas. A maioria das aplicações web que usam HTTP ou HTTPS devem usar recursos e controladores de entrada de Kubernetes, que funcionam na camada 7. A Ingress pode distribuir tráfego com base no URL da aplicação e lidar com a rescisão TLS/SSL. Esta capacidade também reduz o número de endereços IP que expõe e mapeia. Com um equilibrador de carga, cada aplicação normalmente necessita de um endereço IP público atribuído e mapeado para o serviço no cluster AKS. Com um recurso de entrada, um único endereço IP pode distribuir tráfego para várias aplicações.

![Diagrama mostrando fluxo de tráfego ingress num cluster AKS](media/operator-best-practices-network/aks-ingress.png)

 Existem dois componentes para a entrada:

 * Um *recurso* ingress, e
 * Um *controlador ingress*

O recurso ingress é um manifesto YAML `kind: Ingress` que define o anfitrião, certificados e regras para encaminhar o tráfego para serviços que funcionam no seu cluster AKS. O exemplo seguinte, o manifesto YAML distribuiria o tráfego de *myapp.com* a um dos dois serviços, *blogservice* ou *serviço de loja.* O cliente é direcionado para um serviço ou outro com base no URL a que acedem.

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

Um controlador de entrada é um daemon que funciona num nó AKS e observa os pedidos de entrada. O tráfego é então distribuído com base nas regras definidas no recurso ingresss. O controlador de entrada mais comum baseia-se no [NGINX]. AKS não o restringe a um controlador específico, pelo que pode utilizar outros controladores como [Contour,][contour] [HAProxy][haproxy]ou [Traefik.][traefik]

Os controladores de entrada devem ser programados num nó Linux. Os nós do Windows Server não devem executar o controlador de entrada. Utilize um seletor de nó no seu manifesto YAML ou na implementação do gráfico Helm para indicar que o recurso deve ser executado num nó baseado em Linux. Para obter mais informações, consulte [os seletores de nó para controlar onde as cápsulas estão programadas em AKS][concepts-node-selectors].

Existem muitos cenários para a entrada, incluindo os seguintes guias:

* [Criar um controlador básico de entrada com conectividade de rede externa][aks-ingress-basic]
* [Crie um controlador ingress que utilize uma rede interna, privada e endereço IP][aks-ingress-internal]
* [Crie um controlador de entrada que use os seus próprios certificados TLS][aks-ingress-own-tls]
* Crie um controlador ingress que utilize o Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou com um endereço IP público [estático][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Tráfego seguro com uma firewall de aplicação web (WAF)

**Orientação para as melhores práticas** - Para digitalizar o tráfego de entrada para potenciais ataques, utilize uma firewall de aplicação web (WAF) como [Barracuda WAF para Azure][barracuda-waf] ou Azure Application Gateway. Estes recursos de rede mais avançados também podem encaminhar o tráfego para além das ligações HTTP e HTTPS ou da rescisão básica do TLS.

Um controlador ingress que distribui tráfego a serviços e aplicações é tipicamente um recurso Kubernetes no seu cluster AKS. O controlador funciona como um daemon num nó AKS, e consome alguns dos recursos do nó, como CPU, memória e largura de banda de rede. Em ambientes maiores, você frequentemente pretende descarregar parte deste encaminhamento de tráfego ou terminação TLS para um recurso de rede fora do cluster AKS. Também quer pesquisar o tráfego de entrada para potenciais ataques.

![Uma firewall de aplicação web (WAF) como a Azure App Gateway pode proteger e distribuir tráfego para o seu cluster AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Uma firewall de aplicação web (WAF) fornece uma camada extra de segurança filtrando o tráfego de entrada. O Open Web Application Security Project (OWASP) fornece um conjunto de regras para observar ataques como scripts de sites cruzados ou envenenamento por cookies. [O Azure Application Gateway][app-gateway] (atualmente em pré-visualização em AKS) é um WAF que pode integrar-se com clusters AKS para fornecer estas funcionalidades de segurança, antes que o tráfego chegue ao seu cluster AKS e aplicações. Outras soluções de terceiros também desempenham estas funções, para que possa continuar a utilizar investimentos ou conhecimentos existentes num determinado produto.

Os recursos de balançadores de carga ou de entrada continuam a funcionar no seu cluster AKS para aperfeiçoar ainda mais a distribuição de tráfego. App Gateway pode ser gerido centralmente como um controlador de entrada com uma definição de recurso. Para começar, [crie um controlador Application Gateway Ingress][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Controlar o fluxo de tráfego com políticas de rede

**Orientação de boas práticas** - Utilize políticas de rede para permitir ou negar tráfego a cápsulas. Por predefinição, todo o tráfego é permitido entre cápsulas dentro de um cluster. Para uma maior segurança, defina regras que limitem a comunicação do casulo.

A política de rede é uma funcionalidade kubernetes que permite controlar o fluxo de tráfego entre cápsulas. Pode optar por permitir ou negar tráfego com base em configurações como etiquetas atribuídas, espaço de identificação ou porta de tráfego. A utilização de políticas de rede dá uma forma nativa de controlar o fluxo de tráfego. Como os pods são criados dinamicamente num cluster AKS, as políticas de rede necessárias podem ser aplicadas automaticamente. Não utilize grupos de segurança da rede Azure para controlar o tráfego pod-to-pod, utilize as políticas de rede.

Para utilizar a política de rede, a funcionalidade deve ser ativada quando criar um cluster AKS. Não é possível ativar a política de rede num cluster AKS existente. Planeie com antecedência para garantir que permite a política de rede em clusters e pode usá-los conforme necessário. A política de rede só deve ser utilizada para nós e cápsulas baseados em Linux em AKS.

Uma política de rede é criada como um recurso Kubernetes usando um manifesto YAML. As políticas são aplicadas a cápsulas definidas, depois as regras de entrada ou saída definem como o tráfego pode fluir. O exemplo a seguir aplica uma política de rede às cápsulas com a app: etiqueta *de backend* aplicada a eles. A regra de entrada só permite então o tráfego de cápsulas com a app: etiqueta *frontal:*

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

Para começar com as políticas, consulte [tráfego seguro entre cápsulas utilizando políticas de rede no Serviço Azure Kubernetes (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Ligue-se seguramente aos nóles através de um hospedeiro de bastião

**Orientação para as melhores práticas** - Não exponha a conectividade remota aos seus nós AKS. Crie um hospedeiro de bastião, ou caixa de salto, numa rede virtual de gestão. Utilize o anfitrião de bastiões para encaminhar o tráfego de forma segura para o seu cluster AKS para tarefas de gestão remota.

A maioria das operações em AKS pode ser concluída usando as ferramentas de gestão Azure ou através do servidor API de Kubernetes. Os nós AKS não estão ligados à internet pública e só estão disponíveis numa rede privada. Para ligar aos nós e efetuar problemas de manutenção ou resolução de problemas, encaminhe as suas ligações através de um hospedeiro de bastião ou caixa de salto. Este anfitrião deve estar numa rede virtual de gestão separada que é seguramente espreitada para a rede virtual do cluster AKS.

![Ligue-se aos nosdes AKS usando um hospedeiro de bastião, ou caixa de salto](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A rede de gestão do hospedeiro de bastião também deve ser assegurada. Utilize uma porta de entrada [Azure ExpressRoute][expressroute] ou [VPN][vpn-gateway] para ligar a uma rede no local e controlar o acesso utilizando grupos de segurança de rede.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se na conectividade da rede e na segurança. Para obter mais informações sobre os fundamentos da rede em Kubernetes, consulte [conceitos de rede para aplicações no Serviço Azure Kubernetes (AKS)][aks-concepts-network]

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