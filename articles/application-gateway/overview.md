---
title: O que é o Gateway de Aplicação do Azure
description: Saiba como pode utilizar um gateway de aplicação do Azure para gerir o tráfego da Web para a sua aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: 1e80fa23519104c3c62f6a0bf5d65cbbe0848ae2
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443827"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicação do Azure?

O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. Os balanceadores de carga tradicionais funcionam na camada de transporte (camada OSI 4 - TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e porta, para uma porta e um endereço IP de destino.

![Conceito de Gateway de aplicação](media/overview/figure1-720.png)

Com o Application Gateway, pode tomar decisões de encaminhamento com base em atributos adicionais de um pedido HTTP, como o caminho URI ou os cabeçalhos do anfitrião. Por exemplo, pode encaminhar tráfego com base no URL de origem. Por isso, se `/images` estiver no URL de origem, pode encaminhar o tráfego para um conjunto específico de servidores (conhecido como agrupamento) configurado para imagens. Se `/video` estiver no URL, esse tráfego é encaminhado para outra piscina otimizada para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Este tipo de encaminhamento é conhecido como balanceamento de carga da camada de aplicação (camada OSI 7). O Gateway de Aplicação do Azure pode fazer encaminhamento com base no URL e muito mais.

>[!NOTE]
> O Azure oferece um conjunto de soluções de balanceamento de carga totalmente geridas para os seus cenários. Se precisa de alto desempenho, baixa latência, equilíbrio de carga camada 4, veja [o que é o Azure Load Balancer?](../load-balancer/load-balancer-overview.md) Se procura o equilíbrio global de carga slédi, veja o que é o Gestor de [Tráfego?](../traffic-manager/traffic-manager-overview.md) Os seus cenários de ponta a ponta podem beneficiar da combinação destas soluções.
>
> Para uma comparação de opções de equilíbrio de carga Azure, consulte [a visão geral das opções de equilíbrio de carga em Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

As seguintes funcionalidades estão incluídas com o Gateway de Aplicação do Azure:

## <a name="secure-sockets-layer-ssltls-termination"></a>Cessação da camada de tomadas seguras (SSL/TLS)

O gateway da aplicação suporta a rescisão sSL/TLS no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de backend. Esta funcionalidade permite que os servidores Web estejam livres de sobrecarga de encriptação e desencriptação dispendiosa. Mas, por vezes, a comunicação não encriptada aos servidores não é uma opção aceitável. Isto pode ser devido a requisitos de segurança, requisitos de conformidade, ou o pedido só pode aceitar uma ligação segura. Para estas aplicações, o gateway da aplicação suporta o fim da encriptação SSL/TLS.

## <a name="autoscaling"></a>Dimensionamento automático

Implementações de Gateway de aplicação ou WAF sob Standard_v2 ou WAF_v2 suporte SKU autoscalcificação e podem escalar para cima ou para baixo com base na alteração dos padrões de carga de tráfego. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. Para obter mais informações sobre as funcionalidades Standard_v2 e WAF_v2 do Gateway de Aplicação, consulte [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundância da zona

Um Gateway de aplicação ou implementações WAF sob Standard_v2 ou WAF_v2 SKU pode abranger várias Zonas de Disponibilidade, oferecendo uma melhor resiliência de falhas e removendo a necessidade de fornecer gateways de aplicação separados em cada zona.

## <a name="static-vip"></a>VIP estático

A porta de entrada de aplicação VIP na Standard_v2 ou WAF_v2 SKU suporta exclusivamente o tipo VIP estático. Isto garante que o VIP associado ao gateway de aplicação não muda mesmo ao longo da vida do Gateway de Aplicação.

## <a name="web-application-firewall"></a>Firewall de aplicação Web

Firewall de aplicação web (WAF) é um serviço que fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns. A WAF baseia-se em regras da regra central da [OWASP (Open Web Application Security Project) definidas](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (apenas WAF_v2), 3.0 e 2.2.9. 

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Para mais informações, consulte o que é o Firewall de [aplicação web do Azure?](../web-application-firewall/overview.md)

## <a name="ingress-controller-for-aks"></a>Controlador de Entrada para AKS
O Controlador de Ingresso de Gateway de Aplicação (AGIC) permite-lhe utilizar o Application Gateway como entrada para um cluster [do Serviço Azure Kubernetes (AKS).](https://azure.microsoft.com/services/kubernetes-service/) 

O controlador de ingresso funciona como uma cápsula dentro do cluster AKS e consome [kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) e converte-os numa configuração de Gateway de aplicação que permite o gateway para o tráfego de equilíbrio de carga para as cápsulas Kubernetes. O controlador de entrada suporta apenas o Gateway de aplicação Standard_v2 e WAF_v2 SKUs. 

Para mais informações, consulte [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Encaminhamento com base no URL

O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de servidores de back-end com base nos Caminhos de URL. Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdo para diferentes agrupamentos.

Por exemplo, os pedidos de `http://contoso.com/video/*` são encaminhados para VideoServerPool e os pedidos de `http://contoso.com/images/*` são encaminhados para ImageServerPool. É selecionado o DefaultServerPool se nenhum dos padrões de caminho corresponder.

Para mais informações, consulte [o encaminhamento baseado em URL com o Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Alojamento de vários sites

O alojamento de vários sites permite-lhe configurar mais do que um site na mesma instância do gateway de aplicação. Esta funcionalidade permite-lhe configurar uma topologia mais eficiente para as suas implementações, adicionando até 100 web sites a um Gateway de Aplicação, ou 40 para WAF (para um desempenho ótimo). Cada site pode ser direcionado para o seu próprio agrupamento. Por exemplo, o gateway de aplicação pode servir o tráfego para `contoso.com` e `fabrikam.com` a partir de dois agrupamentos de servidores denominados ContosoServerPool e FabrikamServerPool.

Os pedidos de `http://contoso.com` são encaminhados para ContosoServerPool e os pedidos de `http://fabrikam.com` são encaminhados para FabrikamServerPool.

Da mesma forma, dois subdomínios do mesmo domínio principal podem ser alojados na mesma implementação do gateway de aplicação. Exemplos de como utilizar subdomínios podem incluir `http://blog.contoso.com` e `http://app.contoso.com` alojados numa única implementação do gateway de aplicação.

Para mais informações, consulte [o alojamento em vários locais com o Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Redirecionamento

Um cenário comum para muitas aplicações Web consiste em suportar o redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicações entre uma aplicação e os seus utilizadores ocorre através de um caminho encriptado.

No passado, pode ter usado técnicas como a criação dedicada de piscinas cujo único objetivo é redirecionar os pedidos que recebe em HTTP para HTTPS. O gateway de aplicação suporta a capacidade de redirecionar o tráfego no Gateway de Aplicação. Isto simplifica a configuração da aplicação, otimiza a utilização de recursos e suporta novos cenários de redirecionamento, incluindo o redirecionamento global e com base no caminho. O suporte de redirecionamento do Gateway de aplicação não se limita apenas à reorientação HTTP para HTTPS. Trata-se de um mecanismo de redirecionamento genérico, para que possa redirecionar de e para qualquer porta que define através de regras. Também suporta o redirecionamento para um site externo.

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

- Redirecionamento global de uma porta para outra porta no Gateway. Isto permite o redirecionamento de HTTP para HTTPS num site.
- Redirecionamento com base no caminho. Este tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas numa área específica de um site, por exemplo, uma área de carrinho de compras indicada por `/cart/*`.
- Redirecionar para um site externo.

Para mais informações, consulte [redirecionar o tráfego](https://docs.microsoft.com/azure/application-gateway/redirect-overview) com o Application Gateway.

## <a name="session-affinity"></a>Afinidade de sessão

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

## <a name="websocket-and-http2-traffic"></a>Tráfego de Websocket e HTTP/2

O Gateway de Aplicação fornece suporte nativo para os protocolos WebSocket e HTTP/2. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket.

Os protocolos WebSocket e HTTP/2 ativam a comunicação duplex completa entre um servidor e um cliente através de uma ligação TCP de execução longa. Isto permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem necessidade de consulta, que é necessária em implementações com base em HTTP. Estes protocolos têm despesas gerais baixas, ao contrário do HTTP, e podem reutilizar a mesma ligação TCP para múltiplos pedidos/respostas, resultando numa utilização mais eficiente dos recursos. Estes protocolos foram concebidos para funcionar através das portas HTTP tradicionais 80 e 443.

Para mais informações, consulte o [suporte WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) e o [suporte HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda a conseguir uma remoção correta de membros do conjunto de back-end durante as atualizações de serviço planeadas. Esta definição é ativada através da definição de http de back-end e pode ser aplicada a todos os membros de um conjunto de back-end durante a criação da regra. Uma vez ativado, o Application Gateway garante que todas as instâncias de registo de um pool de backend não recebem qualquer novo pedido, permitindo que os pedidos existentes completem dentro de um prazo configurado. Isto aplica-se a ambos os casos de backend que são explicitamente removidos do pool de backend por uma alteração de configuração do utilizador, e instâncias de backend que são reportadas como insalubres como determinadas pelas sondas de saúde. A única exceção a esta questão são os pedidos destinados a desregistar casos, que foram explicitamente desregistados, devido à afinidade da sessão gerida por gateways e continuarão a ser sujeitos às instâncias de desregisto.

Para mais informações, consulte a secção de drenagem de ligação da visão geral da configuração do gateway da [aplicação](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Para mais informações, consulte [Erros Personalizados](custom-error.md).

## <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Os cabeçalhos HTTP permitem ao cliente e servidor passar informações adicionais com o pedido ou a resposta. Reescrever estes cabeçalhos HTTP ajuda-o a realizar vários cenários importantes, tais como:

- Adicionar campos de cabeçalho relacionados com a segurança como HSTS/ X-XSS-Protection.
- Remover campos de cabeçalho de resposta que podem revelar informações sensíveis.
- Despojando informações da porta dos cabeçalhos X-Forwarded-For.

O Application Gateway suporta a capacidade de adicionar, remover ou atualizar os cabeçalhos de pedido e resposta http, enquanto os pacotes de pedido e resposta se movem entre o cliente e os pools back-end. Também lhe fornece a capacidade de adicionar condições para garantir que os cabeçalhos especificados são reescritos apenas quando determinadas condições são satisfeitas.

Para mais informações, consulte [os cabeçalhos http.](rewrite-http-headers.md)

## <a name="sizing"></a>Dimensionamento

O Gateway de aplicação Standard_v2 e WAF_v2 SKU podem ser configurados para implementações de tamanho fixo ou autoscalcificação. Estas SKUs não oferecem tamanhos diferentes de instâncias. Para obter mais informações sobre desempenho e preços v2, consulte [Autoscaling v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

O Aplicativo Gateway Standard e WAF SKU é atualmente oferecido em três tamanhos: **Pequeno,** **Médio**e **Grande**. Os tamanhos de instâncias pequenas destinam-se a cenários de testes e desenvolvimento.

Para obter uma lista completa dos limites do gateway de aplicação, veja [limites do serviço Gateway de Aplicação](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

A tabela seguinte mostra um rendimento médio para cada instância de gateway v1 de aplicação com descarga SSL ativada:

| Tamanho médio de resposta de página de back-end | Pequeno | Médio | Grande |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Estes valores são valores aproximados para um débito de gateway de aplicação. O débito real depende de vários detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para servir uma página. Para números de desempenho exatos, deve executar o seus próprios testes. Estes valores são fornecidos apenas para a capacidade orientação de planeamento.

## <a name="next-steps"></a>Passos seguintes

Consoante os seus requisitos e o ambiente, pode criar um Gateway de Aplicação de teste com o portal do Azure, o Azure PowerShell ou a CLI do Azure:

- [Quickstart: Tráfego web direto com Portal de Aplicação Azure - portal Azure](quick-create-portal.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure](quick-create-cli.md)
