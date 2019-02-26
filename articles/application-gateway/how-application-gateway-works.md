---
title: Como o Azure funciona de Gateway de aplicação
description: Este artigo fornece informações sobre como funciona de Gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 06206ececcb1a51da402c4232f19801793c1cd4a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807342"
---
# <a name="how-application-gateway-works"></a>Como funciona o Gateway de aplicação

Gateway de aplicação é um controlador de entrega de aplicações de cloud com a camada 7 (HTTP) o balanceamento de carga permite-lhe gerir o tráfego da web em todos os servidores. Além disso, ele também fornece recursos de Firewall de aplicações Web (WAF) que fornecem proteção centralizada dos seus serviços web de vulnerabilidades e explorações web comuns.

Quando um cliente faz um pedido HTTP para o Gateway de aplicação, ele atua como proxy inverso e encaminha o tráfego para os servidores de back-end com base na sua configuração. Além disso, o Gateway de aplicação também monitoriza o estado de funcionamento dos seus servidores de back-end e garante que ele encaminha o tráfego apenas para o back-ends de bom estado de funcionamento.

![como-application-gateway-funciona](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Como o pedido foi aceite

Antes de um cliente envia um pedido para o Gateway de aplicação, ele resolve o nome de domínio de Gateway de aplicação a utilizar um servidor de sistema de nomes de domínio (DNS). A entrada DNS é controlada pelo Azure, porque os Gateways de aplicação estão no domínio azure.com. O DNS do Azure devolve o endereço IP para o cliente, o que é o *endereço IP de front-end* do Gateway de aplicação. O Gateway de aplicação aceita tráfego de entrada em um ou mais *serviços de escuta*. Um serviço de escuta é um processo que verifica a existência de pedidos de ligação. Este é configurado com um endereço IP apoiado, o protocolo e o número de porta para ligações de clientes para o Gateway de aplicação. Se estiver ativada a WAF, o Gateway de aplicação verifica os cabeçalhos de pedido e o corpo (se presente) em relação a *regras WAF* para determinar se o pedido é uma solicitação válida - caso em que vai ser encaminhado para o back-end - ou uma ameaça de segurança, no Nesse caso o pedido será bloqueado.  

## <a name="how-request-is-routed"></a>Como o pedido é encaminhado

Se o pedido é considerado válido (ou se não estiver ativada a WAF), o *solicitar regra de encaminhamento* associadas com o *serviço de escuta* é avaliada para determinar o *conjunto back-end* para qual o pedido é encaminhado. As regras são processadas pela ordem em que são apresentadas no portal. Com base na *solicitar regra de encaminhamento* configuração, o Gateway de aplicação decide se pretende encaminhar todos os pedidos no serviço de escuta a um conjunto de back-end específico ou roteá-los aos agrupamentos de back-end diferente dependendo no caminho do URL ou a *redirecionar pedidos* para outra porta ou um site externo

Uma vez um *back-end* *conjunto* tiver sido escolhido, o Gateway de aplicação envia o pedido para um dos servidores back-end configurados no conjunto que está em bom estado (y.y.y.y). O estado de funcionamento do servidor é determinado por um *sonda de estado de funcionamento*. Se o conjunto de back-end contém mais de um servidor, o Gateway de aplicação utiliza o algoritmo de round robin para encaminhar os pedidos entre os servidores em bom estado, assim, o balanceamento de carga as solicitações nos servidores.

Depois de ter sido determinado um servidor de back-end, o Gateway de aplicação é aberta uma nova sessão TCP com o servidor de back-end com base na configuração no *definições de HTTP*. O *definições de HTTP* é um componente que especifica o protocolo, porta e outros encaminhamento relacionados com a definição que é necessário para estabelecer uma nova sessão com o servidor de back-end. A porta e protocolo utilizado nas definições de HTTP determinam se o tráfego entre os servidores de back-end e o Gateway de aplicação é encriptado, assim a realização de SSL ponto a ponto ou não encriptada. Ao enviar a solicitação original para o servidor de back-end, o Gateway de aplicação respeita qualquer configuração personalizada efetuada nas definições de HTTP relacionadas ao substituir o nome de anfitrião, o caminho, o protocolo; afinidade de sessão baseada em cookies de manutenção, ligação a ser drenado, escolher o nome de anfitrião do back-end, etc.

O gateway de aplicação também insere esses cabeçalhos HTTP * predefinida de três: x-reencaminhados-para proto x reencaminhados e x-reencaminhados-porta para o pedido reencaminhado para o back-end.

Assim que o servidor de back-end processa a solicitação e envia uma resposta HTTP com o conteúdo da página para o Gateway de aplicação, o Gateway de aplicação encaminha a resposta ao cliente em que a página é apresentada no browser.

## <a name="application-load-balancing-type"></a>Tipo de balanceamento de carga da aplicação

Pode utilizar o Gateway de aplicação como um balanceador de carga de aplicação interna ou um balanceador de carga da aplicação de acesso à Internet. Um Gateway de aplicação de acesso à Internet tem endereços IP públicos. O nome DNS de um Gateway de aplicação de acesso à Internet é resolvível publicamente para o respetivo endereço IP público. Por conseguinte, os Gateways de aplicação de acesso à Internet podem encaminhar pedidos de clientes através da Internet.

Um Gateway de aplicação interna tem o endereço IP privado. O nome DNS de um Gateway de aplicação interna é internamente puder ser resolvido para o respetivo endereço IP privado. Por conseguinte, balanceadores de carga interno só podem encaminhar pedidos de clientes com acesso à VNET para o Gateway de aplicação.

Tenha em atenção que os Gateways de aplicação de acesso à Internet e interno encaminhar pedidos para os servidores de back-end com endereços IP privados. Se o seu recurso de conjunto de back-end que contém um endereço IP privado, configuração de NIC de VM ou um endereço resolvido internamente, e se o seu conjunto de back-end é um ponto final público, o Gateway de aplicação utiliza o IP público de front-end para alcançar o servidor. Se ainda não aprovisionou um endereço IP público de front-end, um é atribuído para a saída conectividade externa.

## <a name="next-steps"></a>Passos Seguintes

Depois de saber sobre o funcionamento do Gateway de aplicação, aceda a [componentes do Gateway de aplicação](application-gateway-components.md) para compreender os seus componentes em mais detalhes.
