---
title: Práticas recomendadas de operador-conectividade de rede nos serviços Kubernetess do Azure (AKS)
description: Conheça as práticas recomendadas do operador de cluster para recursos de rede virtual e conectividade no serviço kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mlearned
ms.openlocfilehash: 5b138849538cb9bbd6af6cbcf3e7a11b0cf66395
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549126"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para conectividade de rede e segurança no AKS (serviço kubernetes do Azure)

Ao criar e gerenciar clusters no AKS (serviço kubernetes do Azure), você fornece conectividade de rede para seus nós e aplicativos. Esses recursos de rede incluem intervalos de endereços IP, balanceadores de carga e controladores de entrada. Para manter uma alta qualidade de serviço para seus aplicativos, você precisa planejar e, em seguida, configurar esses recursos.

Este artigo de práticas recomendadas se concentra na conectividade e segurança de rede para operadores de cluster. Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Comparar os modos de rede kubenet e CNI do Azure no AKS
> * Planejar o endereçamento e a conectividade IP necessários
> * Distribuir o tráfego usando balanceadores de carga, controladores de entrada ou um WAF (firewall do aplicativo Web)
> * Conectar-se com segurança a nós de cluster

## <a name="choose-the-appropriate-network-model"></a>Escolha o modelo de rede apropriado

**Diretrizes de práticas recomendadas** -para integração com redes virtuais ou redes locais existentes, use a rede CNI do Azure no AKs. Esse modelo de rede também permite uma maior separação de recursos e controles em um ambiente corporativo.

As redes virtuais fornecem a conectividade básica para nós AKS e clientes acessarem seus aplicativos. Há duas maneiras diferentes de implantar clusters AKS em redes virtuais:

* **Rede Kubenet** – o Azure gerencia os recursos da rede virtual à medida que o cluster é implantado e usa o plug-in kubernetes do [Kubenet][kubenet] .
* **Rede CNI do Azure** – implanta em uma rede virtual existente e usa o plug-in KUBERNETES do [CNI (interface de rede de contêiner do Azure)][cni-networking] . Os pods recebem IPs individuais que podem rotear para outros serviços de rede ou recursos locais.

A CNI (interface de rede de contêineres) é um protocolo neutro de fornecedor que permite que o tempo de execução do contêiner faça solicitações para um provedor de rede. O Azure CNI atribui endereços IP a pods e nós e fornece recursos de IPAM (gerenciamento de endereço IP) à medida que você se conecta a redes virtuais do Azure existentes. Cada nó e recurso Pod recebe um endereço IP na rede virtual do Azure e nenhum roteamento adicional é necessário para se comunicar com outros recursos ou serviços.

![Diagrama mostrando dois nós com pontes conectando cada um a uma única VNet do Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Para a maioria das implantações de produção, você deve usar a rede CNI do Azure. Esse modelo de rede permite a separação de controle e gerenciamento de recursos. Do ponto de vista da segurança, muitas vezes você deseja que equipes diferentes gerenciem e protejam esses recursos. O Azure CNI Networking permite que você se conecte a recursos existentes do Azure, recursos locais ou outros serviços diretamente por meio de endereços IP atribuídos a cada pod.

Quando você usa a rede CNI do Azure, o recurso de rede virtual está em um grupo de recursos separado para o cluster AKS. Delegue permissões para a entidade de serviço AKS acessar e gerenciar esses recursos. A entidade de serviço usada pelo cluster AKS deve ter pelo menos permissões de [colaborador de rede](../role-based-access-control/built-in-roles.md#network-contributor) na sub-rede em sua rede virtual. Se você quiser definir uma [função personalizada](../role-based-access-control/custom-roles.md) em vez de usar a função de colaborador de rede interna, as seguintes permissões serão necessárias:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Para obter mais informações sobre a delegação de entidade de serviço do AKS, consulte [delegar acesso a outros recursos do Azure][sp-delegation].

Como cada nó e Pod recebem seu próprio endereço IP, planeje os intervalos de endereços para as sub-redes AKS. A sub-rede deve ser grande o suficiente para fornecer endereços IP para cada nó, pods e recursos de rede que você implantar. Cada cluster AKS deve ser colocado em sua própria sub-rede. Para permitir a conectividade com redes locais ou emparelhadas no Azure, não use intervalos de endereços IP sobrepostos com os recursos de rede existentes. Há limites padrão para o número de pods que cada nó executa com o kubenet e a rede CNI do Azure. Para lidar com eventos de expansão ou atualizações de cluster, você também precisa de endereços IP adicionais disponíveis para uso na sub-rede atribuída. Esse espaço de endereço adicional é especialmente importante se você usar contêineres do Windows Server (atualmente em visualização no AKS), pois esses pools de nós exigem uma atualização para aplicar os patches de segurança mais recentes. Para obter mais informações sobre nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade].

Para calcular o endereço IP necessário, consulte [Configurar a rede CNI do Azure no AKs][advanced-networking].

### <a name="kubenet-networking"></a>Rede Kubenet

Embora o kubenet não exija que você configure as redes virtuais antes da implantação do cluster, há desvantagens:

* Os nós e os pods são colocados em sub-redes IP diferentes. O roteamento definido pelo usuário (UDR) e o encaminhamento de IP são usados para rotear o tráfego entre pods e nós. Esse roteamento adicional pode reduzir o desempenho da rede.
* As conexões com redes locais existentes ou emparelhamento com outras redes virtuais do Azure podem ser complexas.

O Kubenet é adequado para pequenas cargas de trabalho de desenvolvimento ou teste, pois você não precisa criar a rede virtual e sub-redes separadamente do cluster AKS. Sites simples com baixo tráfego, ou para migrar e deslocar cargas de trabalho em contêineres, também podem se beneficiar da simplicidade dos clusters AKS implantados com a rede kubenet. Para a maioria das implantações de produção, você deve planejar e usar a rede CNI do Azure. Você também pode [configurar seus próprios intervalos de endereços IP e redes virtuais usando o kubenet][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuir o tráfego de entrada

**Diretrizes de práticas recomendadas** -para distribuir o tráfego http ou HTTPS para seus aplicativos, use recursos e controladores de entrada. Controladores de entrada fornecem recursos adicionais em um balanceador de carga do Azure regular e podem ser gerenciados como recursos de kubernetes nativos.

Um balanceador de carga do Azure pode distribuir o tráfego do cliente para aplicativos em seu cluster AKS, mas ele é limitado no que entende esse tráfego. Um recurso de balanceador de carga funciona na camada 4 e distribui o tráfego com base no protocolo ou nas portas. A maioria dos aplicativos Web que usam HTTP ou HTTPS deve usar os recursos e controladores de entrada do Kuberenetes, que funcionam na camada 7. A entrada pode distribuir o tráfego com base na URL do aplicativo e tratar a terminação TLS/SSL. Essa capacidade também reduz o número de endereços IP que você expõe e mapeia. Com um balanceador de carga, cada aplicativo normalmente precisa de um endereço IP público atribuído e mapeado para o serviço no cluster AKS. Com um recurso de entrada, um único endereço IP pode distribuir o tráfego para vários aplicativos.

![Diagrama mostrando o fluxo de tráfego de entrada em um cluster AKS](media/operator-best-practices-network/aks-ingress.png)

 Há dois componentes para entrada:

 * Um *recurso*de entrada e
 * Um *controlador* de entrada

O recurso de entrada é um manifesto YAML de `kind: Ingress` que define o host, os certificados e as regras para rotear o tráfego para os serviços executados no cluster do AKS. O manifesto YAML de exemplo a seguir distribuirá o tráfego para *MyApp.com* para um dos dois serviços, *BlogService* ou *storeservice*. O cliente é direcionado para um serviço ou o outro com base na URL que eles acessam.

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

Um controlador de entrada é um daemon que é executado em um nó AKS e observa solicitações de entrada. O tráfego é distribuído com base nas regras definidas no recurso de entrada. O controlador de entrada mais comum é baseado em [Nginx]. O AKS não o restringe a um controlador específico, portanto, você pode usar outros controladores, como [contorno][contour], [HAProxy][haproxy]ou [Traefik][traefik].

Controladores de entrada devem ser agendados em um nó do Linux. Os nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Use um seletor de nó no manifesto do YAML ou na implantação do gráfico do Helm para indicar que o recurso deve ser executado em um nó baseado em Linux. Para obter mais informações, consulte [usar seletores de nó para controlar onde os pods estão agendados no AKs][concepts-node-selectors].

Há muitos cenários para entrada, incluindo os seguintes guias de instruções:

* [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
* [Criar um controlador de entrada que usa uma rede interna e privada e um endereço IP][aks-ingress-internal]
* [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
* Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Proteger o tráfego com um WAF (firewall do aplicativo Web)

**Diretrizes de práticas recomendadas** -para verificar o tráfego de entrada para ataques potenciais, use um WAF (firewall do aplicativo Web), como o [Barracuda WAF para o Azure][barracuda-waf] ou aplicativo Azure gateway. Esses recursos de rede mais avançados também podem rotear o tráfego além de conexões HTTP e HTTPS ou terminação SSL básica.

Um controlador de entrada que distribui o tráfego para serviços e aplicativos normalmente é um recurso kubernetes em seu cluster AKS. O controlador é executado como um daemon em um nó AKS e consome alguns dos recursos do nó, como CPU, memória e largura de banda da rede. Em ambientes maiores, muitas vezes você deseja descarregar parte desse roteamento de tráfego ou término de TLS para um recurso de rede fora do cluster AKS. Você também deseja verificar o tráfego de entrada para ataques potenciais.

![Um WAF (firewall do aplicativo Web), como Azure App gateway, pode proteger e distribuir o tráfego para o cluster AKS](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Um WAF (firewall do aplicativo Web) fornece uma camada adicional de segurança filtrando o tráfego de entrada. O Open Web Application Security Project (OWASP) fornece um conjunto de regras para inspecionar ataques como scripts entre sites ou envenenamento de cookie. [Aplicativo Azure gateway][app-gateway] (atualmente em visualização em AKs) é um WAF que pode ser integrado a clusters AKs para fornecer esses recursos de segurança, antes que o tráfego atinja o cluster e os aplicativos do AKS. Outras soluções de terceiros também executam essas funções, para que você possa continuar a usar os investimentos ou a experiência existente em um determinado produto.

O balanceador de carga ou os recursos de entrada continuam a ser executados no cluster AKS para refinar ainda mais a distribuição de tráfego. O gateway de aplicativo pode ser gerenciado centralmente como um controlador de entrada com uma definição de recurso. Para começar, [crie um controlador de entrada do gateway de aplicativo][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Controlar o fluxo de tráfego com as políticas de rede

**Diretrizes de práticas recomendadas** -use as políticas de rede para permitir ou negar o tráfego para pods. Por padrão, todo o tráfego é permitido entre pods em um cluster. Para aumentar a segurança, defina as regras que limitam a comunicação Pod.

A diretiva de rede é um recurso kubernetes que permite controlar o fluxo de tráfego entre pods. Você pode optar por permitir ou negar o tráfego com base em configurações, como rótulos, namespace ou porta de tráfego atribuídos. O uso de políticas de rede fornece uma maneira nativa de nuvem para controlar o fluxo de tráfego. Como os pods são criados dinamicamente em um cluster AKS, as diretivas de rede necessárias podem ser aplicadas automaticamente. Não use grupos de segurança de rede do Azure para controlar o tráfego Pod a Pod, use as políticas de rede.

Para usar a política de rede, o recurso deve ser habilitado quando você cria um cluster AKS. Não é possível habilitar a política de rede em um cluster AKS existente. Planeje com antecedência para garantir que você habilite a política de rede em clusters e possa usá-los conforme necessário. A política de rede deve ser usada somente para nós baseados em Linux e pods em AKS.

Uma política de rede é criada como um recurso kubernetes usando um manifesto YAML. As políticas são aplicadas ao pods definido, as regras de entrada ou saída definem como o tráfego pode fluir. O exemplo a seguir aplica uma política de rede a pods com o rótulo *aplicativo: back-end* aplicado a eles. A regra de entrada só permite o tráfego de pods com o rótulo *aplicativo: frontend* :

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

Para começar a usar as políticas, confira [proteger o tráfego entre os pods usando as políticas de rede no serviço de kubernetes do Azure (AKs)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Conectar-se com segurança a nós por meio de um host bastião

**Diretrizes de práticas recomendadas** -não expor conectividade remota aos nós do AKS. Crie um host de bastiões ou uma caixa de salto em uma rede virtual de gerenciamento. Use o host de bastiões para rotear com segurança o tráfego para o cluster AKS para tarefas de gerenciamento remoto.

A maioria das operações no AKS pode ser concluída usando as ferramentas de gerenciamento do Azure ou por meio do servidor de API do kubernetes. Os nós AKS não estão conectados à Internet pública e estão disponíveis apenas em uma rede privada. Para se conectar a nós e realizar a manutenção ou solucionar problemas, encaminhe suas conexões por meio de um host bastião ou Jump box. Esse host deve estar em uma rede virtual de gerenciamento separada que seja emparelhada com segurança para a rede virtual do cluster AKS.

![Conectar-se a nós do AKS usando um host de bastiões ou Jump Box](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A rede de gerenciamento para o host de bastiões também deve ser protegida. Use um [Gateway de VPN][vpn-gateway] ou [ExpressRoute do Azure][expressroute] para se conectar a uma rede local e controlar o acesso usando grupos de segurança de rede.

## <a name="next-steps"></a>Passos seguintes

Este artigo se concentrou na conectividade e na segurança da rede. Para obter mais informações sobre noções básicas de rede no kubernetes, consulte [conceitos de rede para aplicativos no serviço kubernetes do Azure (AKs)][aks-concepts-network]

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