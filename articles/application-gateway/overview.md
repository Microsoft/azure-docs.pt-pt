---
title: O que é o Gateway de Aplicação do Azure
description: Saiba como pode utilizar um gateway de aplicação do Azure para gerir o tráfego da Web para a sua aplicação.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: a51276cd54a88c5e566afef0d06673f14b5862cf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572039"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicação do Azure?

O Gateway de Aplicação do Azure é um balanceador de carga do tráfego da Web que lhe permite gerir o tráfego para as suas aplicações Web. Os balanceadores de carga tradicionais funcionam na camada de transporte (camada OSI 4 - TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e porta, para uma porta e um endereço IP de destino.

![Conceitual do gateway de aplicativo](media/overview/figure1-720.png)

Com o gateway de aplicativo, você pode tomar decisões de roteamento com base em atributos adicionais de uma solicitação HTTP, como caminho de URI ou cabeçalhos de host. Por exemplo, pode encaminhar tráfego com base no URL de origem. Por isso, se `/images` estiver no URL de origem, pode encaminhar o tráfego para um conjunto específico de servidores (conhecido como agrupamento) configurado para imagens. Se `/video` estiver na URL, esse tráfego será roteado para outro pool otimizado para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Este tipo de encaminhamento é conhecido como balanceamento de carga da camada de aplicação (camada OSI 7). O Gateway de Aplicação do Azure pode fazer encaminhamento com base no URL e muito mais.

As seguintes funcionalidades estão incluídas com o Gateway de Aplicação do Azure:

## <a name="secure-sockets-layer-ssltls-termination"></a>Encerramento de protocolo SSL (SSL/TLS)

O gateway de aplicativo dá suporte à terminação SSL/TLS no gateway, após o qual o tráfego normalmente flui sem criptografia para os servidores de back-end. Esta funcionalidade permite que os servidores Web estejam livres de sobrecarga de encriptação e desencriptação dispendiosa. Mas, às vezes, a comunicação não criptografada com os servidores não é uma opção aceitável. Isso pode ser devido a requisitos de segurança, requisitos de conformidade ou o aplicativo só pode aceitar uma conexão segura. Para esses aplicativos, o gateway de aplicativo dá suporte à criptografia SSL/TLS de ponta a ponta.

## <a name="autoscaling"></a>Dimensionamento automático

As implantações de gateway de aplicativo ou de WAF em Standard_v2 ou WAF_v2 SKU dão suporte ao dimensionamento automático e podem ser escaladas verticalmente com base na alteração dos padrões de carga de tráfego. O dimensionamento automático também elimina o requisito de escolher um tamanho de implementação ou uma contagem de instâncias ou durante o aprovisionamento. Para obter mais informações sobre os recursos Standard_v2 e WAF_v2 do gateway de aplicativo, consulte [SKU v2 de dimensionamento](application-gateway-autoscaling-zone-redundant.md)automático.

## <a name="zone-redundancy"></a>Redundância de zona

Um gateway de aplicativo ou implantações WAF sob Standard_v2 ou WAF_v2 SKU pode abranger vários Zonas de Disponibilidade, oferecendo melhor resiliência de falha e removendo a necessidade de provisionar gateways de aplicativo separados em cada zona.

## <a name="static-vip"></a>VIP estático

O VIP do gateway de aplicativo no SKU Standard_v2 ou WAF_v2 dá suporte exclusivamente ao tipo de VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado mesmo durante o tempo de vida do gateway de aplicativo.

## <a name="web-application-firewall"></a>Firewall de aplicação Web

O WAF (firewall do aplicativo Web) é um serviço que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O WAF é baseado em regras dos [conjuntos de regras principais do OWASP (projeto de segurança de aplicativo Web aberto)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (somente WAF_v2), 3,0 e 2.2.9. 

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Destas vulnerabilidades, são frequentes os ataques de injeção de SQL, scripting entre sites, entre muitas outras. Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em muitas camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Para obter mais informações, consulte [o que é o Firewall do aplicativo Web do Azure?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Controlador de entrada para AKS
O AGIC (controlador de entrada do gateway de aplicativo) permite que você use o gateway de aplicativo como entrada para um cluster do [AKs (serviço de kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/) . 

O controlador de entrada é executado como um pod dentro do cluster AKS e consome [recursos de entrada do kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) e os converte em uma configuração de gateway de aplicativo que permite ao gateway balancear a carga do tráfego para o pods kubernetes. O controlador de entrada só dá suporte ao SKU do gateway de aplicativo v2. 

Para obter mais informações, consulte [controlador de entrada do gateway de aplicativo (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Encaminhamento com base no URL

O Encaminhamento Baseado no Caminho do URL permite-lhe encaminhar o tráfego para agrupamentos de servidores de back-end com base nos Caminhos de URL. Um dos cenários consiste em encaminhar pedidos de diferentes tipos de conteúdo para diferentes agrupamentos.

Por exemplo, os pedidos de `http://contoso.com/video/*` são encaminhados para VideoServerPool e os pedidos de `http://contoso.com/images/*` são encaminhados para ImageServerPool. É selecionado o DefaultServerPool se nenhum dos padrões de caminho corresponder.

Para obter mais informações, consulte [roteamento baseado em URL com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Alojamento de vários sites

O alojamento de vários sites permite-lhe configurar mais do que um site na mesma instância do gateway de aplicação. Esse recurso permite que você configure uma topologia mais eficiente para suas implantações adicionando até 100 sites a um gateway de aplicativo ou 40 para WAF (para obter um desempenho ideal). Cada site pode ser direcionado para o seu próprio agrupamento. Por exemplo, o gateway de aplicação pode servir o tráfego para `contoso.com` e `fabrikam.com` a partir de dois agrupamentos de servidores denominados ContosoServerPool e FabrikamServerPool.

Os pedidos de `http://contoso.com` são encaminhados para ContosoServerPool e os pedidos de `http://fabrikam.com` são encaminhados para FabrikamServerPool.

Da mesma forma, dois subdomínios do mesmo domínio principal podem ser alojados na mesma implementação do gateway de aplicação. Exemplos de como utilizar subdomínios podem incluir `http://blog.contoso.com` e `http://app.contoso.com` alojados numa única implementação do gateway de aplicação.

Para obter mais informações, consulte [Hospedagem de vários sites com o gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Redirecionamento

Um cenário comum para muitas aplicações Web consiste em suportar o redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicações entre uma aplicação e os seus utilizadores ocorre através de um caminho encriptado.

No passado, você pode ter usado técnicas como a criação de pools dedicados cujo único propósito é redirecionar as solicitações recebidas por HTTP para HTTPS. O gateway de aplicação suporta a capacidade de redirecionar o tráfego no Gateway de Aplicação. Isto simplifica a configuração da aplicação, otimiza a utilização de recursos e suporta novos cenários de redirecionamento, incluindo o redirecionamento global e com base no caminho. O suporte ao redirecionamento de gateway de aplicativo não é limitado apenas ao redirecionamento de HTTP para HTTPS. Trata-se de um mecanismo de redirecionamento genérico, para que possa redirecionar de e para qualquer porta que define através de regras. Também suporta o redirecionamento para um site externo.

O suporte de redirecionamento do Gateway de Aplicação oferece as seguintes capacidades:

- Redirecionamento global de uma porta para outra porta no Gateway. Isto permite o redirecionamento de HTTP para HTTPS num site.
- Redirecionamento com base no caminho. Este tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas numa área específica de um site, por exemplo, uma área de carrinho de compras indicada por `/cart/*`.
- Redirecionar para um site externo.

Para obter mais informações, consulte [redirecionando o tráfego](https://docs.microsoft.com/azure/application-gateway/redirect-overview) com o gateway de aplicativo.

## <a name="session-affinity"></a>Afinidade de sessão

A funcionalidade de afinidade de sessão com base em cookies é útil quando pretende manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

## <a name="websocket-and-http2-traffic"></a>Tráfego de Websocket e HTTP/2

O Gateway de Aplicação fornece suporte nativo para os protocolos WebSocket e HTTP/2. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket.

Os protocolos WebSocket e HTTP/2 ativam a comunicação duplex completa entre um servidor e um cliente através de uma ligação TCP de execução longa. Isto permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional sem necessidade de consulta, que é necessária em implementações com base em HTTP. Esses protocolos têm baixa sobrecarga, diferentemente de HTTP, e podem reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização de recursos mais eficiente. Estes protocolos foram concebidos para funcionar através das portas HTTP tradicionais 80 e 443.

Para obter mais informações, consulte [suporte a WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) e [suporte a http/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda a conseguir uma remoção correta de membros do conjunto de back-end durante as atualizações de serviço planeadas. Esta definição é ativada através da definição de http de back-end e pode ser aplicada a todos os membros de um conjunto de back-end durante a criação da regra. Uma vez habilitado, o gateway de aplicativo garante que todas as instâncias de cancelamento de registro de um pool de back-end não recebam nenhuma nova solicitação, permitindo que as solicitações existentes sejam concluídas dentro de um limite de tempo configurado. Isso se aplica a ambas as instâncias de back-end que são explicitamente removidas do pool de back-end por uma chamada à API e instâncias de back-end que são relatadas como não íntegras, conforme determinado pelas investigações de integridade.

Para obter mais informações, consulte a seção descarga de conexão da [visão geral de configuração do gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#connection-draining).

## <a name="custom-error-pages"></a>Páginas de erros personalizadas

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada.

Para obter mais informações, consulte [Custom Errors](custom-error.md).

## <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Os cabeçalhos HTTP permitem que o cliente e o servidor passem informações adicionais com a solicitação ou a resposta. Reescrever esses cabeçalhos HTTP ajuda você a realizar vários cenários importantes, como:

- Adicionar campos de cabeçalho relacionados à segurança como HSTS/X-XSS-Protection.
- Removendo campos de cabeçalho de resposta que podem revelar informações confidenciais.
- Removendo informações de porta de cabeçalhos X-Forwarded-for.

O gateway de aplicativo dá suporte à capacidade de adicionar, remover ou atualizar cabeçalhos de solicitação e resposta HTTP, enquanto os pacotes de solicitação e resposta são movidos entre os pools de cliente e de back-end. Ele também fornece a capacidade de adicionar condições para garantir que os cabeçalhos especificados sejam regravados somente quando determinadas condições forem atendidas.

Para obter mais informações, consulte [reescrever cabeçalhos HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Dimensionamento

O SKU do Application Gateway Standard_v2 e WAF_v2 pode ser configurado para implantações de tamanho automático ou fixo. Essas SKUs não oferecem tamanhos de instância diferentes. Para obter mais informações sobre o desempenho e os preços de v2, consulte [autoescala v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#pricing).

O SKU do gateway de aplicativo Standard e WAF é atualmente oferecido em três tamanhos: **pequeno**, **médio**e **grande**. Os tamanhos de instâncias pequenas destinam-se a cenários de testes e desenvolvimento.

Para obter uma lista completa dos limites do gateway de aplicação, veja [limites do serviço Gateway de Aplicação](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

A tabela a seguir mostra uma taxa de transferência de desempenho médio para cada instância do Application Gateway v1 com descarregamento de SSL habilitado:

| Tamanho médio da resposta da página de back-end | Pequeno | Médio | Grande |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Estes valores são valores aproximados para um débito de gateway de aplicação. O débito real depende de vários detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para servir uma página. Para números de desempenho exatos, deve executar o seus próprios testes. Estes valores são fornecidos apenas para a capacidade orientação de planeamento.

## <a name="next-steps"></a>Passos seguintes

Consoante os seus requisitos e o ambiente, pode criar um Gateway de Aplicação de teste com o portal do Azure, o Azure PowerShell ou a CLI do Azure:

- [Início rápido: tráfego direto da Web com Aplicativo Azure gateway-portal do Azure](quick-create-portal.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - CLI do Azure](quick-create-cli.md)
