---
title: Componentes do gateway de aplicativo
description: Este artigo fornece informações sobre os vários componentes em um gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 54606b4fbbf7ae459298b3842f957de5256ba0df
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971150"
---
# <a name="application-gateway-components"></a>Componentes do gateway de aplicativo

 Um gateway de aplicativo serve como o único ponto de contato para clientes. Ele distribui o tráfego de aplicativo de entrada entre vários pools de back-end, que incluem VMs do Azure, conjuntos de dimensionamento de máquinas virtuais, serviço de Azure App e servidores locais/externos. Para distribuir o tráfego, um gateway de aplicativo usa vários componentes descritos neste artigo.

![Os componentes usados em um gateway de aplicativo](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Endereços IP de front-end

Um endereço IP de front-end é o endereço IP associado a um gateway de aplicativo. Você pode configurar um gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um gateway de aplicativo dá suporte a um endereço IP público ou privado. Sua rede virtual e o endereço IP público devem estar no mesmo local que o seu gateway de aplicativo. Depois de criado, um endereço IP de front-end é associado a um ouvinte.

### <a name="static-versus-dynamic-public-ip-address"></a>Endereço IP público estático versus dinâmico

O SKU do gateway v2 de Aplicativo Azure pode ser configurado para dar suporte tanto ao endereço IP interno estático quanto ao endereço IP público estático ou somente ao endereço IP público estático. Ele não pode ser configurado para dar suporte apenas ao endereço IP interno estático.

A SKU v1 pode ser configurada para dar suporte ao endereço IP interno estático ou dinâmico e ao endereço IP público dinâmico. O endereço IP dinâmico do gateway de aplicativo não é alterado em um gateway em execução. Ele pode ser alterado somente quando você parar ou iniciar o gateway. Ele não é alterado em falhas do sistema, atualizações, atualizações de host do Azure, etc. 

O nome DNS associado a um gateway de aplicativo não é alterado durante o ciclo de vida do gateway. Como resultado, você deve usar um alias CNAME e apontar para o endereço DNS do gateway de aplicativo.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica as solicitações de conexão de entrada. Um ouvinte aceitará uma solicitação se o protocolo, a porta, o nome do host e o endereço IP associados à solicitação corresponderem aos mesmos elementos associados à configuração do ouvinte.

Antes de usar um gateway de aplicativo, você deve adicionar pelo menos um ouvinte. Pode haver vários ouvintes anexados a um gateway de aplicativo e eles podem ser usados para o mesmo protocolo.

Depois que um ouvinte detecta solicitações de entrada de clientes, o gateway de aplicativo roteia essas solicitações para membros no pool de back-end configurado na regra.

Os ouvintes dão suporte às portas e aos protocolos a seguir.

### <a name="ports"></a>Portas

Uma porta é onde um ouvinte escuta a solicitação do cliente. Você pode configurar portas que variam de 1 a 65502 para a SKU v1 e de 1 a 65199 para a SKU v2.

### <a name="protocols"></a>Protocolos

O gateway de aplicativo dá suporte a quatro protocolos: HTTP, HTTPS, HTTP/2 e WebSocket:
>[!NOTE]
>O suporte ao protocolo HTTP/2 está disponível para clientes que se conectam somente a ouvintes do gateway de aplicativo. A comunicação com os pools de servidores back-end é sempre por HTTP/1.1. Por padrão, o suporte a HTTP/2 está desabilitado. Você pode optar por habilitá-lo.

- Especifique entre os protocolos HTTP e HTTPS na configuração do ouvinte.
- O suporte para os [protocolos WebSockets e http/2](overview.md#websocket-and-http2-traffic) é fornecido nativamente, e o [suporte ao WebSocket](application-gateway-websocket.md) está habilitado por padrão. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket. Use WebSockets com ouvintes HTTP e HTTPS.

Use um ouvinte HTTPS para terminação SSL. Um ouvinte HTTPS descarrega o trabalho de criptografia e descriptografia para o gateway de aplicativo, para que os servidores Web não sejam sobrecarregados pela sobrecarga.

### <a name="custom-error-pages"></a>Páginas de erros personalizadas

O gateway de aplicativo permite que você crie páginas de erro personalizadas em vez de exibir páginas de erro padrão. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada. O gateway de aplicativo exibe uma página de erro personalizada quando uma solicitação não pode alcançar o back-end.

Para obter mais informações, consulte [páginas de erro personalizadas para o gateway de aplicativo](custom-error.md).

### <a name="types-of-listeners"></a>Tipos de ouvintes

Há dois tipos de ouvintes:

- **Básico**. Esse tipo de ouvinte escuta um único site de domínio, em que ele tem um único mapeamento de DNS para o endereço IP do gateway de aplicativo. Essa configuração de ouvinte é necessária quando você hospeda um único site por trás de um gateway de aplicativo.

- **Multissite**. Essa configuração de ouvinte é necessária quando você configura mais de um aplicativo Web na mesma instância do gateway de aplicativo. Ele permite que você configure uma topologia mais eficiente para suas implantações adicionando até 100 sites a um gateway de aplicativo. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo, três subdomínios, abc.contoso.com, xyz.contoso.com e pqr.contoso.com, apontam para o endereço IP do gateway de aplicativo. Você criaria três ouvintes multissite e configuraria cada ouvinte para as respectivas configurações de porta e protocolo.

    Para obter mais informações, consulte [hospedagem em vários sites](application-gateway-web-app-overview.md).

Depois de criar um ouvinte, você o associa a uma regra de roteamento de solicitação. Essa regra determina como a solicitação recebida no ouvinte deve ser roteada para o back-end.

O gateway de aplicativo processa os ouvintes na [ordem mostrada](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Regras de roteamento de solicitação

Uma regra de roteamento de solicitação é um componente-chave de um gateway de aplicativo, pois determina como rotear o tráfego no ouvinte. A regra associa o ouvinte, o pool de servidores de back-end e as configurações de HTTP de backend.

Quando um ouvinte aceita uma solicitação, a regra de roteamento de solicitação encaminha a solicitação para o back-end ou a redireciona em outro lugar. Se a solicitação for encaminhada para o back-end, a regra de roteamento de solicitação define o pool de servidores back-end para o qual encaminhá-lo. A regra de roteamento de solicitação também determina se os cabeçalhos na solicitação devem ser reescritos. Um ouvinte pode ser anexado a uma regra.

Há dois tipos de regras de roteamento de solicitações:

- **Básico**. Todas as solicitações no ouvinte associado (por exemplo, blog.contoso.com/*) são encaminhadas para o pool de back-end associado usando a configuração de HTTP associada.

- **Baseado em caminho**. Essa regra de roteamento permite rotear as solicitações no ouvinte associado para um pool de back-end específico, com base na URL na solicitação. Se o caminho da URL em uma solicitação corresponder ao padrão de caminho em uma regra com base em caminho, a regra roteará essa solicitação. Ele aplica o padrão de caminho somente ao caminho da URL, não aos seus parâmetros de consulta. Se o caminho da URL em uma solicitação de ouvinte não corresponder a nenhuma das regras com base no caminho, ele roteia a solicitação para o pool de back-end padrão e as configurações de HTTP.

Para obter mais informações, consulte [roteamento baseado em URL](url-route-overview.md).

### <a name="redirection-support"></a>Suporte de redirecionamento

A regra de roteamento de solicitação também permite que você redirecione o tráfego no gateway de aplicativo. Esse é um mecanismo de redirecionamento genérico, para que você possa redirecionar para e de qualquer porta que você definir usando regras.

Você pode escolher o destino de redirecionamento para outro ouvinte (que pode ajudar a habilitar o redirecionamento automático de HTTP para HTTPS) ou um site externo. Você também pode optar por fazer com que o redirecionamento seja temporário ou permanente, ou acrescentar o caminho do URI e a cadeia de caracteres de consulta à URL redirecionada.

Para obter mais informações, consulte [redirecionar o tráfego no seu gateway de aplicativo](redirect-overview.md).

### <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Usando as regras de roteamento de solicitação, você pode adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP (S), pois os pacotes de solicitação e resposta são movidos entre o cliente e os pools de back-end por meio do gateway de aplicativo.

Os cabeçalhos podem ser definidos para valores estáticos ou para outros cabeçalhos e variáveis de servidor. Isso ajuda com casos de uso importantes, como extração de endereços IP do cliente, remoção de informações confidenciais sobre o back-end, adição de mais segurança e assim por diante.

Para obter mais informações, consulte [reescrever cabeçalhos HTTP no seu gateway de aplicativo](rewrite-http-headers.md).

## <a name="http-settings"></a>Definições de HTTP

Um gateway de aplicativo roteia o tráfego para os servidores de back-end (especificados na regra de roteamento de solicitação que inclui as configurações de HTTP) usando o número da porta, o protocolo e outras configurações detalhadas neste componente.

A porta e o protocolo usados nas configurações de HTTP determinam se o tráfego entre o gateway de aplicativo e os servidores de back-end é criptografado (fornecendo SSL de ponta a ponta) ou não criptografado.

Esse componente também é usado para:

- Determine se uma sessão de usuário deve ser mantida no mesmo servidor usando a afinidade de [sessão baseada em cookie](overview.md#session-affinity).

- Remova normalmente os membros do pool de back-end usando o [descarregamento de conexão](overview.md#connection-draining).

- Associar uma investigação personalizada para monitorar a integridade do back-end, definir o intervalo de tempo limite da solicitação, substituir o nome do host e o caminho na solicitação e fornecer uma facilidade de clique para especificar as configurações para o back-end do serviço de aplicativo.

## <a name="backend-pools"></a>Conjuntos de back end

Um pool de back-end roteia a solicitação para servidores de back-end, que atendem à solicitação. Os pools de back-end podem conter:

- NICs
- Conjuntos de dimensionamento de máquinas virtuais
- Endereços IP públicos
- Endereços IP internos
- FQDN
- Back-ends de vários locatários (como o serviço de aplicativo)

Os membros do pool de back-end do gateway de aplicativo não estão associados a um conjunto de disponibilidade Um gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que ela está. Como resultado, os membros dos pools de back-end podem estar entre clusters, em data centers ou fora do Azure, contanto que haja conectividade IP.

Se você usar IPs internos como membros do pool de back-end, deverá usar o [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). O emparelhamento de rede virtual tem suporte e é benéfico para o tráfego de balanceamento de carga em outras redes virtuais.

Um gateway de aplicativo também pode se comunicar com servidores locais quando eles estiverem conectados por túneis de VPN ou ExpressRoute do Azure se o tráfego for permitido.

Você pode criar pools de back-end diferentes para diferentes tipos de solicitações. Por exemplo, crie um pool de back-end para solicitações gerais e, em seguida, outro pool de back-end para solicitações para os microserviços para seu aplicativo.

## <a name="health-probes"></a>Sondas do estado de funcionamento

Por padrão, um gateway de aplicativo monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente aqueles não íntegros. Em seguida, ele monitora instâncias não íntegras e as adiciona novamente ao pool de back-end íntegro quando elas se tornam disponíveis e respondem a investigações de integridade.

Além de usar o monitoramento de investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender aos requisitos do seu aplicativo. As investigações personalizadas permitem um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e o caminho para teste e quantas respostas com falha serão aceitas antes que a instância do pool de back-end seja marcada como não íntegra. Recomendamos que você configure investigações personalizadas para monitorar a integridade de cada pool de back-end.

Para obter mais informações, consulte [monitorar a integridade do seu gateway de aplicativo](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Passos seguintes

Criar um gateway de aplicativo:

* [No portal do Azure](quick-create-portal.md)
* [Usando Azure PowerShell](quick-create-powershell.md)
* [Usando o CLI do Azure](quick-create-cli.md)
