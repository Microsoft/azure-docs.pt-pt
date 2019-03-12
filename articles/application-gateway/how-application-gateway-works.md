---
title: Como o Azure funciona de Gateway de aplicação
description: Este artigo fornece informações sobre como funciona de Gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 41f69d2017d9fc04acda47d09c718d3585f6335c
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726282"
---
# <a name="how-application-gateway-works"></a>Como funciona o Gateway de aplicação

Este artigo explica como o gateway de aplicação aceita a entrada de pedidos e encaminha-os para o back-end.

![como-application-gateway-funciona](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-a-request-is-accepted"></a>Como um pedido foi aceite

Antes de um cliente envia um pedido para o gateway de aplicação, ele resolve o nome de domínio de gateway de aplicação a utilizar um servidor de sistema de nomes de domínio (DNS). A entrada DNS é controlada pelo Azure, porque os gateways de aplicação estão no domínio azure.com. O DNS do Azure devolve o endereço IP para o cliente, o que é o *endereço IP de front-end* do Gateway de aplicação. O gateway de aplicação aceita tráfego de entrada em um ou mais *serviços de escuta*. Um serviço de escuta é uma entidade lógica que verifica a existência de pedidos de ligação. Este é configurado com um endereço IP apoiado, o protocolo e o número de porta para ligações de clientes para o gateway de aplicação. Se a Firewall de aplicações Web (WAF) estiver ativada, o Gateway de aplicação verifica os cabeçalhos de pedido e o corpo (se presente) em relação a *regras WAF* para determinar se o pedido é uma solicitação válida - caso em que vai ser encaminhado para o back-end - ou uma ameaça de segurança, nesse caso o pedido será bloqueado.  

Gateway de aplicação pode ser utilizado como um balanceador de carga de aplicação interna ou um balanceador de carga da aplicação de acesso à Internet. Um gateway de aplicação de acesso à Internet tem endereços IP públicos. O nome DNS de um gateway de aplicação de acesso à Internet é resolvível publicamente para o respetivo endereço IP público. Por conseguinte, os gateways de aplicação de acesso à Internet podem encaminhar pedidos de clientes através da Internet. Gateways de aplicação interna tem o endereço IP privado. O nome DNS de um gateway de aplicação interna é resolvível publicamente para o respetivo endereço IP privado. Por conseguinte, balanceadores de carga interno só podem encaminhar pedidos de clientes com acesso à VNET para o gateway de aplicação. Gateways de aplicação de acesso à Internet e interno encaminhar pedidos para os servidores de back-end com endereços IP privados. Por conseguinte, os servidores de back-end não tem endereços IP públicos para receber pedidos de um interno ou de um Gateway de aplicação de acesso à Internet.

## <a name="how-a-request-is-routed"></a>Como um pedido é encaminhado

Se o pedido é considerado válido (ou se não estiver ativada a WAF), o *solicitar regra de encaminhamento* associadas com o *serviço de escuta* é avaliada para determinar o *conjunto back-end* para qual o pedido é encaminhado. As regras são processadas pela ordem em que são apresentadas no portal. Com base na *solicitar regra de encaminhamento* configuração, o gateway de aplicação decide se pretende encaminhar todos os pedidos no serviço de escuta a um conjunto de back-end específico ou roteá-los aos agrupamentos de back-end diferente dependendo no caminho do URL ou a *redirecionar pedidos* para outra porta ou um site externo

Uma vez um *back-end* *conjunto* tiver sido escolhido, o gateway de aplicação envia o pedido para um dos servidores back-end configurados no conjunto que está em bom estado (y.y.y.y). O estado de funcionamento do servidor é determinado por um *sonda de estado de funcionamento*. Se o conjunto de back-end contém mais de um servidor, o gateway de aplicação utiliza o algoritmo de round robin para encaminhar os pedidos entre os servidores em bom estado, assim, o balanceamento de carga as solicitações nos servidores.

Depois de ter sido determinado um servidor de back-end, o gateway de aplicação é aberta uma nova sessão TCP com o servidor de back-end com base na configuração no *definições de HTTP*. O *definições de HTTP* é um componente que especifica o protocolo, porta e outros encaminhamento relacionados com a definição que é necessário para estabelecer uma nova sessão com o servidor de back-end. A porta e protocolo utilizado nas definições de HTTP determinam se o tráfego entre os servidores de gateway e o back-end de aplicação é encriptado, assim a realização de SSL ponto a ponto ou não encriptada. Ao enviar a solicitação original para o servidor de back-end, o gateway de aplicação respeita qualquer configuração personalizada efetuada nas definições de HTTP relacionadas ao substituir o nome de anfitrião, o caminho, o protocolo; afinidade de sessão baseada em cookies de manutenção, ligação a ser drenado, escolher o nome de anfitrião do back-end, etc.

Um Gateway de aplicação interna tem o endereço IP privado. O nome DNS de um Gateway de aplicação interna é internamente puder ser resolvido para o respetivo endereço IP privado. Por conseguinte, balanceadores de carga interno só podem encaminhar pedidos de clientes com acesso à VNET para o Gateway de aplicação.

Tenha em atenção que os Gateways de aplicação de acesso à Internet e interno encaminhar pedidos para os servidores de back-end com endereços IP privados. Se o seu recurso de conjunto de back-end que contém um endereço IP privado, configuração de NIC de VM ou um endereço resolvido internamente, e se o seu conjunto de back-end é um ponto final público, o Gateway de aplicação utiliza o IP público de front-end para alcançar o servidor. Se ainda não aprovisionou um endereço IP público de front-end, um é atribuído para a saída conectividade externa.

### <a name="modifications-to-the-request"></a>Modificações ao pedido

Gateway de aplicação insere 4 cabeçalhos adicionais a todos os pedidos antes de ela encaminha os pedidos para o back-end. Esses cabeçalhos são X-reencaminhados-para, proto X reencaminhados, porta X reencaminhados e X-original-anfitrião. O formato para o cabeçalho x-reencaminhados-para-se uma lista separada por vírgulas de IP: porta. Os valores válidos para proto x reencaminhados são HTTP ou HTTPS. Porta X reencaminhados Especifica a porta em que o pedido foi atingido no gateway de aplicação. Cabeçalho de X-original-anfitrião contém o cabeçalho de anfitrião original com a qual a solicitação chegou. Este cabeçalho é útil em cenários como a integração do Web site do Azure, onde o cabeçalho de anfitrião recebido é modificado antes do tráfego é encaminhado para o back-end. Opcionalmente, se estiver ativada a afinidade de sessão, em seguida, um cookie de afinidade gerido de gateway é inserido. 

Além disso pode configurar o gateway de aplicação para modificar os cabeçalhos usando [cabeçalhos HTTP reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou modificar o caminho URI usando a definição de substituição de caminho. Mas, a menos que configurado para tal, todos os pedidos recebidos são transmitidas por proxy como é o back-end.


## <a name="next-steps"></a>Passos Seguintes

Depois de saber sobre o funcionamento do gateway de aplicação, consulte [componentes do gateway de aplicação](application-gateway-components.md) para compreender os seus componentes em mais detalhes.
