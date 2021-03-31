---
title: Funcionalidades do Gateway de Aplicações Azure
description: Saiba mais sobre as funcionalidades do Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: ba9f42bc932a37e1052f17db2ae00413e0769d59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355742"
---
# <a name="azure-application-gateway-features"></a>Funcionalidades do Gateway de Aplicações Azure

[O Azure Application Gateway](overview.md) é um equilibrador de carga de tráfego web que lhe permite gerir o tráfego para as suas aplicações web.

![Aplicação Gateway conceptual](media/overview/figure1-720.png)

O Gateway de Aplicações inclui as seguintes funcionalidades:

- [Terminação da camada de tomadas seguras (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Dimensionamento automático](#autoscaling)
- [Redundância entre zonas](#zone-redundancy)
- [VIP estático](#static-vip)
- [Firewall de Aplicação Web](#web-application-firewall)
- [Controlador de Entrada para AKS](#ingress-controller-for-aks)
- [Encaminhamento baseado em URL](#url-based-routing)
- [Alojamento de vários sites](#multiple-site-hosting)
- [Redirecionamento](#redirection)
- [Afinidade da sessão](#session-affinity)
- [Tráfego de Websocket e HTTP/2](#websocket-and-http2-traffic)
- [Drenagem de ligação](#connection-draining)
- [Páginas de erro personalizadas](#custom-error-pages)
- [Rescrever cabeçalhos HTTP e URL](#rewrite-http-headers-and-url)
- [Dimensionamento](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Terminação da camada de tomadas seguras (SSL/TLS)

O gateway de aplicação suporta a terminação SSL/TLS no gateway, após o qual o tráfego normalmente flui desencriptado para os servidores de backend. Esta funcionalidade permite que os servidores Web estejam livres de sobrecarga de encriptação e desencriptação dispendiosa. Mas às vezes a comunicação não encriptada para os servidores não é uma opção aceitável. Isto pode ser devido a requisitos de segurança, requisitos de conformidade, ou o pedido só pode aceitar uma ligação segura. Para estas aplicações, os suportes de gateway de aplicação terminam com a encriptação SSL/TLS.

Para mais informações, consulte [a visão geral da rescisão de SSL e o fim do SSL com o Gateway de Aplicações](ssl-overview.md)

## <a name="autoscaling"></a>Dimensionamento automático

O Standard_v2 de Aplicação Gateway suporta a autoscalagem e pode escalar para cima ou para baixo com base na alteração dos padrões de carga de tráfego. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. 

Para obter mais informações sobre as funcionalidades de Standard_v2 do Gateway de Aplicação, consulte [Autoscaling v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundância entre zonas

Um Gateway de aplicação Standard_v2 pode abranger várias Zonas de Disponibilidade, oferecendo uma melhor resiliência de falhas e removendo a necessidade de providenciar gateways de aplicação separados em cada zona.

## <a name="static-vip"></a>VIP estático

A porta de entrada de aplicação Standard_v2 SKU suporta exclusivamente o tipo VIP estático. Isto garante que o VIP associado ao gateway de aplicações não muda mesmo ao longo da vida útil do Gateway de aplicações.

## <a name="web-application-firewall"></a>Firewall de Aplicações Web

Web Application Firewall (WAF) é um serviço que fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. A WAF baseia-se nas regras do [OWASP (Open Web Application Security Project) que define](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (apenas WAF_v2), 3.0 e 2.2.9. 

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser convertidos para um portal de aplicação ativado pela Web Application Firewall facilmente.

Para mais informações, veja [o que é a Azure Web Application Firewall?](../web-application-firewall/overview.md)

## <a name="ingress-controller-for-aks"></a>Controlador de Entrada para AKS
O Controlador de Entradas de Gateway (AGIC) permite-lhe utilizar o Application Gateway como entrada para um cluster [Azure Kubernetes Service (AKS).](https://azure.microsoft.com/services/kubernetes-service/) 

O controlador de entrada funciona como uma cápsula dentro do cluster AKS e consome [recursos Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) e converte-os numa configuração de Gateway de Aplicação, que permite ao portal de equilibrar o tráfego para as cápsulas Kubernetes. O controlador de entrada suporta apenas Standard_v2 de Gateway de Aplicação e SKUs WAF_v2. 

Para obter mais informações, consulte [o Controlador de Entrada de Entrada de Aplicação (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Encaminhamento com base no URL

O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de servidores de back-end com base nos Caminhos de URL. Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdo para diferentes agrupamentos.

Por exemplo, os pedidos de `http://contoso.com/video/*` são encaminhados para VideoServerPool e os pedidos de `http://contoso.com/images/*` são encaminhados para ImageServerPool. É selecionado o DefaultServerPool se nenhum dos padrões de caminho corresponder.

Para obter mais informações, consulte [a visão geral do encaminhamento baseado em caminhos url](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Alojamento de vários sites

Com o Application Gateway, pode configurar o encaminhamento com base no nome do anfitrião ou no nome de domínio para mais de uma aplicação web no mesmo gateway de aplicações. Permite-lhe configurar uma topologia mais eficiente para as implementações ao adicionar até 100 sites a um gateway de aplicação. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo, três domínios, contoso.com, fabrikam.com e adatum.com, apontam para o endereço IP do gateway de aplicação. Teria de criar três serviços de escuta com vários sites e configurar cada serviço de escuta para a respetiva definição de porta e protocolo. 

Os pedidos `http://contoso.com` são encaminhados para ContosoServerPool, `http://fabrikam.com` são encaminhados para FabrikamServerPool, e assim por diante.

Da mesma forma, dois subdomínios do mesmo domínio principal podem ser alojados na mesma implementação do gateway de aplicação. Exemplos de como utilizar subdomínios podem incluir `http://blog.contoso.com` e `http://app.contoso.com` alojados numa única implementação do gateway de aplicação. Para obter mais informações, consulte [o site application gateway múltiplo.](multiple-site-overview.md)

Além disso, pode definir nomes de anfitrião de caráter universal num serviço de escuta com vários sites e até cinco nomes de anfitrião por serviço de escuta. Para saber mais, consulte [os nomes dos anfitriões wildcard no ouvinte (pré-visualização)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

## <a name="redirection"></a>Redirecionamento

Um cenário comum para muitas aplicações Web consiste em suportar o redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicações entre uma aplicação e os seus utilizadores ocorre através de um caminho encriptado.

No passado, pode ter usado técnicas como a criação de piscinas dedicadas cujo único propósito é redirecionar os pedidos que recebe em HTTPS. O gateway de aplicação suporta a capacidade de redirecionar o tráfego no Gateway de Aplicação. Isto simplifica a configuração da aplicação, otimiza a utilização de recursos e suporta novos cenários de redirecionamento, incluindo o redirecionamento global e com base no caminho. O suporte de redirecionamento de Gateway de Aplicação não se limita apenas a https para reorientação HTTPS. Trata-se de um mecanismo de redirecionamento genérico, para que possa redirecionar de e para qualquer porta que define através de regras. Também suporta o redirecionamento para um site externo.

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

- Redirecionamento global de uma porta para outra porta no Gateway. Isto permite o redirecionamento de HTTP para HTTPS num site.
- Redirecionamento com base no caminho. Este tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas numa área específica de um site, por exemplo, uma área de carrinho de compras indicada por `/cart/*`.
- Redirecionar para um site externo.

Para obter mais informações, consulte [a visão geral do gateway de aplicações](redirect-overview.md).

## <a name="session-affinity"></a>Afinidade de sessão

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

Para mais informações, consulte [como funciona uma porta de aplicação.](how-application-gateway-works.md#modifications-to-the-request)

## <a name="websocket-and-http2-traffic"></a>Tráfego de Websocket e HTTP/2

O Gateway de Aplicação fornece suporte nativo para os protocolos WebSocket e HTTP/2. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket.

Os protocolos WebSocket e HTTP/2 ativam a comunicação duplex completa entre um servidor e um cliente através de uma ligação TCP de execução longa. Isto permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem necessidade de consulta, que é necessária em implementações com base em HTTP. Estes protocolos têm sobrecargas baixas, ao contrário de HTTP, e podem reutilizar a mesma ligação TCP para múltiplos pedidos/respostas, resultando numa utilização mais eficiente dos recursos. Estes protocolos foram concebidos para funcionar através das portas HTTP tradicionais 80 e 443.

Para obter mais informações, consulte [o suporte da WebSocket](application-gateway-websocket.md) e [o suporte HTTP/2](configuration-listeners.md#http2-support).

## <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda a conseguir uma remoção correta de membros do conjunto de back-end durante as atualizações de serviço planeadas. Esta definição é ativada através da definição de http de back-end e pode ser aplicada a todos os membros de um conjunto de back-end durante a criação da regra. Uma vez ativado, o Application Gateway garante que todas as instâncias de desregista de um pool de backend não recebem qualquer novo pedido, permitindo que os pedidos existentes completem dentro de um prazo configurado. Isto aplica-se a ambos os casos de backend que são explicitamente removidos do pool de backend por uma alteração de configuração do utilizador, e casos de backend que são reportados como insalubres como determinados pelas sondas de saúde. A única exceção a esta questão são os pedidos destinados a desregistar casos, que foram explicitamente descontados, devido à afinidade da sessão gerida por gateways e que continuam a ser acompanhados das instâncias de desregiscimento.

Para obter mais informações, consulte [a visão geral da configuração do Gateway de aplicações](configuration-http-settings.md#connection-draining).

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Para mais informações, consulte [Erros Personalizados.](custom-error.md)

## <a name="rewrite-http-headers-and-url"></a>Rescrever cabeçalhos HTTP e URL

Os cabeçalhos HTTP permitem ao cliente e ao servidor passar informações adicionais com o pedido ou a resposta. Reescrever estes cabeçalhos HTTP ajuda-o a realizar vários cenários importantes, tais como:

- Adicionar campos de cabeçalho relacionados com segurança como HSTS/ X-XSS-Protection.
- Remover campos de cabeçalho de resposta que podem revelar informações sensíveis.
- Despojando informações portuárias de cabeçalhos X-Forwarded-For.

O Gateway de Aplicação e o SKU da WAF v2 suportam a capacidade de adicionar, remover ou atualizar cabeçalhos de pedido e de resposta HTTP, enquanto os pacotes de pedido e de resposta se movem entre os conjuntos de cliente e back-end. Também pode reescrever os URLs, os parâmetros de cadeia de consulta e o nome do anfitrião. Com a reescrita de URLs e o encaminhamento baseado em caminhos de URLs, pode escolher encaminhar pedidos para um dos conjuntos de back-end com base no caminho original ou no caminho reescrito ao utilizar a opção Reavaliar mapa do caminho. 

Além disso, permite-lhe adicionar condições para garantir que os cabeçalhos ou URLs especificados são reescritos apenas quando forem cumpridas determinadas condições. Estas condições baseiam-se nas informações do pedido e da resposta.

Para obter mais informações, consulte [os cabeçalhos HTTP e URL](rewrite-http-headers-url.md).

## <a name="sizing"></a>Dimensionamento

O Gateway de aplicação Standard_v2 pode ser configurado para implantações de autoscaling ou de tamanho fixo. O V2 SKU não oferece diferentes tamanhos de instância. Para obter mais informações sobre o desempenho e preços v2, consulte o [preço de Autoscaling V2](application-gateway-autoscaling-zone-redundant.md) e [Understanding](understanding-pricing.md).

A Aplicação Gateway Standard (v1) é oferecida em três tamanhos: **Pequeno,** **Médio** e **Grande**. Os tamanhos de instâncias pequenas destinam-se a cenários de testes e desenvolvimento.

Para obter uma lista completa dos limites do gateway de aplicação, veja [limites do serviço Gateway de Aplicação](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

A tabela a seguir mostra uma produção média para cada instância de gateway v1 de aplicação com descarregamento SSL ativado:

| Tamanho médio de resposta de página de back-end | Pequeno | Médio | Grande |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Estes valores são valores aproximados para um débito de gateway de aplicação. O débito real depende de vários detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para servir uma página. Para números de desempenho exatos, deve executar o seus próprios testes. Estes valores são fornecidos apenas para a capacidade orientação de planeamento.

## <a name="version-feature-comparison"></a>Comparação de recursos de versão

Para uma comparação de funcionalidades de gateway de aplicação v1-v2, consulte [Autoscaling e Zone-redundante Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Passos seguintes

- Saiba como funciona o Application Gateway - [Como funciona um gateway de aplicações](how-application-gateway-works.md)