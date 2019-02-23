---
title: Componentes de Gateway de aplicação do Azure
description: Este artigo fornece informações sobre os vários componentes no Gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 14f400a1b85e213496ac98996d6c2378cf559026
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675802"
---
# <a name="application-gateway-components"></a>Componentes de Gateway de aplicação

 Um Gateway de aplicação serve como o único ponto de contacto para clientes. Distribui o tráfego das aplicações recebido em vários conjuntos de back-end, como as VMs do Azure, VMSS, serviços de aplicações ou servidores no local/externo. Para fazer isso, ele utiliza vários componentes descritos abaixo:

![componentes de gateway de aplicação](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip"></a>IP de front-end

Apoiado IP é o endereço IP (Internet Protocol) associado ao Gateway de aplicação. Pode configurar o Gateway de aplicação para ter um IP público ou um IP privado ou ambos. Apenas um endereço IP público é suportado num gateway de aplicação. A rede virtual e o endereço IP público tem de ser na mesma localização que o Gateway de aplicação.

### <a name="static-vs-dynamic-public-ip"></a>Estático vs dinâmico de IP público

O SKU de v1 suporta IPs estáticos de interno, mas não suporta a IPs públicos estáticos. O VIP pode alterar se o gateway de aplicação é parado e iniciado. O nome DNS associado com o gateway de aplicação não se altera ao longo do ciclo de vida do gateway. Por esse motivo, é recomendado utilizar um alias CNAME e apontá-lo para o endereço DNS do gateway de aplicação.

O SKU do Gateway de aplicação v2 suporta IPs públicos estáticos e bem como IPs interno. Apenas um endereço IP público é suportado num gateway de aplicação.

Depois de criar um IP de front-end, está associado à *serviços de escuta* que verifique a existência de pedidos recebidos no IP de front-end.

## <a name="listeners"></a>Ouvintes

Antes de começar a utilizar o Gateway de aplicação, tem de adicionar um ou mais serviços de escuta. Um serviço de escuta é um processo com o qual o Gateway de aplicação verifica a existência de pedidos recebidos de conexão, usando o protocolo e porta que configurou. Depois do serviço de escuta detecta solicitações de entrada dos clientes, o Gateway de aplicação encaminha estes pedidos para os servidores de back-end no conjunto de back-end, com as regras de encaminhamento de pedido por si para o serviço de escuta que recebeu o pedido de entrada.

Serviços de escuta suportam as seguintes portas e protocolos:

### <a name="ports"></a>Portas

Esta é a porta na qual o serviço de escuta faz a escuta para o pedido do cliente. O intervalo seguinte é permitido para a configuração da porta: 1-65535

### <a name="protocols"></a>Protocolos

Gateway de aplicação suporta os seguintes protocolos de 4: HTTP, HTTPS, HTTP/2, Websocket

Ao configurar o serviço de escuta, terá de escolher entre o protocolo HTTP e HTTPS. Gateway de aplicação fornece suporte nativo para protocolos WebSockets e HTTP/2. Pode utilizar WebSockets com serviços de escuta HTTP e HTTPS. 

Suporte de protocolo HTTP/2 está disponível para clientes que se conectam para ouvintes de gateway de aplicação apenas. A comunicação para agrupamentos de servidores de back-end é através de HTTP/1.1. Por predefinição, o suporte de HTTP/2 está desativado. O exemplo de fragmento de código do Azure PowerShell seguinte mostra como pode ativá-la:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Suporte de Websocket está ativado por predefinição. Não existe qualquer definição configurável pelo utilizador para ativar ou desativar seletivamente o suporte de WebSocket.

Pode utilizar um serviço de escuta HTTPS para a terminação de SSL que será descarregar o trabalho de criptografia e descriptografia para o Gateway de aplicação para que seus servidores web poderiam ser livres de sobrecarga de encriptação e desencriptação dispendiosa e seus aplicativos podem se concentrar em suas lógica de negócios. Tem de implementar pelo menos um certificado de servidor SSL para um serviço de escuta HTTPS. Para obter mais informações, consulte [como configurar a terminação de SSL](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de Aplicação permite-lhe criar páginas de erro personalizadas, em vez de apresentar as páginas de erro predefinidas. Pode utilizar a sua própria imagem e esquema corporativos através de uma página de erro personalizada. Gateway de aplicação pode apresentar uma página de erro personalizada quando um pedido não é possível alcançar o back-end. Para obter mais informações, consulte [páginas de erro personalizada para o Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/custom-error)

### <a name="types-of-listeners"></a>Tipos de serviços de escuta

Existem dois tipos de serviços de escuta:

- **Básico**: Este tipo de serviço de escuta de escuta a um site único domínio em que tenha um único mapeamento de DNS para endereço IP do Gateway de aplicação. Esta configuração de serviço de escuta é necessária quando está a alojar um site único por trás de um gateway de aplicação
- **Multilocal**: Esta configuração de serviço de escuta é necessária quando estiver a configurar mais do que uma aplicação web na mesma instância de gateway de aplicação. Isto permite-lhe configurar uma topologia mais eficiente para as implementações ao adicionar até 100 Web sites para um gateway de aplicação. Cada site pode ser direcionado para o seu próprio agrupamento de back-end. Por exemplo:  Para três subdomínios — abc.alpha.com, xyz.alpha.com e pqr.alpha.com apontando para o endereço IP do Gateway de aplicação. Crie 3 serviços de escuta do tipo "multilocal" e configure cada serviço de escuta para a respetiva porta e a definição do protocolo. 

Depois de criar um serviço de escuta, associá-la com uma regra de encaminhamento de pedido que determina como o pedido recebido do serviço de escuta de deve ser encaminhado para o back-end.

Serviços de escuta são processados na ordem em que forem sendo apresentados. Por esse motivo se uma solicitação de entrada corresponder a um serviço de escuta básico processa-lo primeiro. Serviços de escuta de múltiplos sites devem ser configurados antes de um serviço de escuta básico para garantir que o tráfego é encaminhado para o back-end correto.

## <a name="request-routing-rule"></a>Solicitar regra de encaminhamento

Este é o componente mais importante do Gateway de aplicação e determina como o tráfego no serviço de escuta associado a esta regra deve ser encaminhado. A regra vincula o serviço de escuta, o conjunto de servidores de back-end e o definições de HTTP de back-end e define para que conjunto de servidores de back-end o tráfego deve ser direcionado ao chegar a um determinado serviço de escuta. Um serviço de escuta pode ser anexado a apenas uma regra.

Pode haver dois tipos de regras de encaminhamento do pedido:

- **Básico:** Todos os pedidos no serviço de escuta associado (por exemplo: blog.contoso.com/*) são reencaminhados para o conjunto de back-end associado através da definição de HTTP associada.
- **Path-based:** Este tipo de regra fornece a capacidade para encaminhar os pedidos no serviço de escuta associado a um conjunto de back-end específicas com base no URL no pedido. Se o caminho do URL num pedido corresponde ao padrão caminho numa regra baseado no caminho, o pedido é encaminhado com essa regra. O padrão de caminho é aplicado apenas para o caminho do URL, não para seus parâmetros de consulta. 

Se o caminho do URL de um pedido de um serviço de escuta de não coincide com qualquer uma das regras com base no caminho, então o pedido é encaminhado para o *predefinido* conjunto back-end e o *predefinido* as definições de HTTP.

As regras são processadas na ordem que estão configurados. Recomenda-se que as regras de múltiplos sites estão configuradas antes do básico de regras para reduzir a possibilidade de que o tráfego é encaminhado para o back-end inadequado como a regra básica corresponderia tráfego com base na porta antes da regra de múltiplos site a ser avaliada.

### <a name="redirection-support"></a>Suporte de redirecionamento

A regra de encaminhamento de pedido também permite-lhe redirecionar o tráfego no Gateway de aplicação. Trata-se de um mecanismo de redirecionamento genérico, para que possa redirecionar de e para qualquer porta que define através de regras. Isto pode ajudar a ativar automática HTTP para redirecionamento a HTTPS garantir que todas as comunicações entre o aplicativo e seus usuários ocorrem através de um caminho encriptado. Pode especificar o serviço de escuta de destino ou um site externo ao qual o redirecionamento é desejado. O elemento de configuração também oferece suporte a opções para ativar a acrescentar a cadeia de caminho e a consulta do URI para o URL redirecionado. Também pode escolher se o redirecionamento é uma temporária (código de estado HTTP 302) ou um redirecionamento permanente (código de estado HTTP 301). Quando utilizar uma regra básica, a configuração de redirecionamento está associada um serviço de escuta de origem e é um redirecionamento global. Quando uma regra baseada em caminho é utilizada, a configuração de redirecionamento é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site. Para obter mais informações, consulte [redirecionar o tráfego no seu Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="http-headers"></a>Cabeçalhos HTTP

Gateway de aplicação insere cabeçalhos x-reencaminhados-para proto x reencaminhados e porta x reencaminhados para o pedido reencaminhado para o back-end. O formato para o cabeçalho x-reencaminhados-para-se uma lista separada por vírgulas de IP: porta. Os valores válidos para proto x reencaminhados são http ou https. Porta X reencaminhados Especifica a porta em que o pedido foi atingido no gateway de aplicação. Gateway de aplicação também insere o cabeçalho X-Original-Host, que contém o cabeçalho de anfitrião original com a qual a solicitação chegou. Este cabeçalho é útil em cenários como a integração do Web site do Azure, onde o cabeçalho de anfitrião recebido é modificado antes do tráfego é encaminhado para o back-end.

#### <a name="rewrite-http-headers"></a>Rescrever cabeçalhos HTTP

Utilizar as regras de encaminhamento de pedido que pode adicionar, remover ou atualizar cabeçalhos de solicitação e resposta de HTTP (S), enquanto os pacotes de solicitação e resposta mover entre o cliente e o back-end, conjuntos, através do Gateway de aplicação. Os cabeçalhos não só podem ser definidos para valores estáticos, mas também para outros cabeçalhos e as variáveis de servidor importantes. Isto irá ajudá-lo a realizar vários casos de uso importante, como extrair o endereço IP dos clientes, remoção das informações confidenciais sobre o back-end, adicionar medidas de segurança adicionais, etc. Para obter mais informações, consulte[cabeçalhos de HTTP de reescrever no seu Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)

## <a name="http-settings"></a>Definições de HTTP

O Gateway de aplicação encaminha o tráfego para os servidores de back-end com o número de porta, protocolo e outras definições especificadas neste recurso. 

Os conjuntos de back-end suportam as seguintes portas e protocolos:

### <a name="ports"></a>Portas

Esta é a porta em que os servidores de back-end estão à escuta para o tráfego proveniente do Gateway de aplicação. O intervalo seguinte é permitido para a configuração da porta: 1-65535

### <a name="protocols"></a>Protocolos

Gateway de aplicação suporta protocolos HTTP e HTTPS de encaminhamento de pedidos para os servidores de back-end.

Se for escolhido o protocolo HTTP, em seguida, o tráfego não criptografados para os servidores de back-end de fluxos. 

Nesses casos em que a comunicação sem encriptação para os servidores de back-end não é uma opção aceitável, deve escolher o protocolo HTTPS. Esta definição combinada com a escolher o protocolo HTTPS no serviço de escuta permite-lhe ativar [SSL ponto a ponto](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Que permitem-lhe transmitir dados confidenciais para o back-end encriptado de forma segura. Cada servidor de back-end no conjunto de back-end com SSL de ponta a ponta ativado deve ser configurado com um certificado, para permitir a comunicação segura.

Utilize a definição de HTTP pode tirar partido de várias capacidades:

### <a name="cookie-based-session-affinity"></a>Afinidade de sessão baseada em cookies

Esta funcionalidade é útil quando deseja manter uma sessão de utilizador no mesmo servidor. Ao utilizar cookies geridos por um gateway, o Gateway de Aplicação pode direcionar o tráfego subsequente de uma sessão de utilizador para o mesmo servidor para processamento. Isto é importante nos casos em que o estado da sessão é guardado localmente no servidor para uma sessão de utilizador.

Se a aplicação não é possível processar a afinidade com base no cookie, em seguida, não será capaz de utilizar esta capacidade

### <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda a conseguir uma remoção correta de membros do conjunto de back-end durante as atualizações de serviço planeadas. Esta definição pode ser aplicada a todos os membros de um conjunto de back-end durante a criação de regra. Depois de ativado, o Gateway de Aplicação assegura que nenhuma das instâncias de anulação do registo de um conjunto de back-end recebe qualquer pedido novo, ao mesmo tempo que permite que os pedidos existentes sejam concluídos num limite de tempo configurado. Isto aplica-se às instâncias de back-end que são explicitamente removidas do conjunto de back-end por uma chamada de API, bem como às instâncias de back-end que são reportadas como danificadas, conforme determinado pelas sondas de estado de funcionamento.

### <a name="override-backend-path"></a>Substituir caminho de back-end

Esta funcionalidade permite-lhe substituir o caminho no URL, para que os pedidos para um caminho específico podem ser encaminhados para outro caminho. Por exemplo, se pretende encaminhar pedidos para contoso.com/images predefinido, em seguida, introduza "/" nesta caixa de texto e, em seguida, anexar esta definição de HTTP para a regra associada contoso.com/images.

### <a name="pick-host-name-from-a-backend-address"></a>Escolha o nome de anfitrião de um endereço de back-end

Esta capacidade define a *anfitrião* cabeçalho no pedido para o nome de anfitrião do conjunto de back-end (IP ou FQDN). Isso é útil nos cenários em que o nome de domínio do back-end é diferente do nome DNS do aplicativo, como um cenário em que o serviço de aplicações do Azure é utilizado como back-end. Isto acontece porque uma vez que o serviço de aplicações do Azure é um ambiente multi-inquilino com um espaço compartilhado com um único endereço IP, um serviço de aplicações podem ser acedido apenas com os nomes de anfitriões configurado nas definições de domínio personalizado. Por predefinição, é "example.azurewebsites.net" e se desejar acessar seu serviço de aplicações com o Gateway de aplicação com um nome de anfitrião não registado no serviço de aplicações ou pelo FQDN de Gateway de aplicação, terá de substituir o nome de anfitrião no pedido original para a aplicação Nome de anfitrião do serviço.

### <a name="host-override"></a>**Substituição de anfitrião**

Esta funcionalidade substitui o *anfitrião* cabeçalho no pedido de entrada no Gateway de aplicação para o nome de anfitrião que especificar aqui. 

Depois de criar uma definição de HTTP, terá de associá-la com um ou mais regras de encaminhamento do pedido.

## <a name="backend-pool"></a>Conjunto back-end

O conjunto de back-end é utilizado para encaminhar pedidos para os servidores de back-end que irão ser que atendeu à solicitação. Conjuntos de back-end podem ser compostos de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, nomes de IPs interno, de domínio completamente qualificado (FQDN) e back-ends de multi-inquilino, como o serviço de aplicações do Azure. Membros do conjunto de back-end de Gateway de aplicação não estão associados a um conjunto de disponibilidade. Membros dos agrupamentos de back-end podem ser em clusters, centros de dados, ou fora do Azure, desde que eles têm conectividade IP. Pode criar conjuntos de back-end diferente para diferentes tipos de pedidos. Por exemplo, crie um conjunto de back-end para pedidos gerais e outro conjunto de back-end para os pedidos para os microsserviços para a sua aplicação.

Depois de criar um conjunto de back-end terá de associá-lo a uma ou mais regras de encaminhamento de pedido. Terá também de configurar sondas de estado de funcionamento para cada conjunto de back-end no Gateway de aplicação. Quando for cumprido um condição de regra de encaminhamento de pedido, o Gateway de aplicação encaminha o tráfego para os servidores de bom estado de funcionamento (conforme determinado pelas sondas de estado de funcionamento) no agrupamento de back-end correspondente.

## <a name="health-probes"></a>Sondas do estado de funcionamento

O Gateway de aplicação do Azure por predefinição monitoriza o estado de funcionamento de todos os recursos no seu conjunto de back-end e automaticamente remove qualquer recurso considerado em mau estado de funcionamento do conjunto. Gateway de aplicação continua a monitorizar as instâncias em mau estado de funcionamento e adiciona-os novamente para o conjunto de back-end em bom estado, uma vez que eles se tornarem disponíveis e respondem às sondas de estado de funcionamento. Gateway de aplicação envia que o estado de funcionamento sondas com a mesma porta que é definida nas definições de HTTP de back-end.

Além de utilizar a monitorização de sonda de estado de funcionamento do predefinido, também pode personalizar a sonda de estado de funcionamento para atender às necessidades da sua aplicação. As pesquisas personalizadas permitem que tenha um controle mais granular sobre a monitorização de estado de funcionamento. Ao utilizar sondas personalizadas, pode configurar o intervalo de pesquisa, o URL e caminho para testar e quantos respostas com falhas para aceitar antes de os marcar a instância de conjunto de back-end como mau estado de funcionamento. É altamente recomendável que configure as pesquisas personalizadas para monitorizar o estado de funcionamento de cada conjunto de back-end. Para obter mais informações, consulte [monitorizar o estado de funcionamento do seu gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)

## <a name="next-steps"></a>Passos Seguintes

Depois de saber sobre os componentes de Gateway de aplicação, [pode criar um Gateway de aplicação no portal do Azure](quick-create-portal.md) ou uma [criar um Gateway de aplicação com o PowerShell](quick-create-powershell.md) ou [criar um Gateway de aplicação com a CLI do Azure](quick-create-cli.md).