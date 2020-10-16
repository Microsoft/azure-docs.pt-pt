---
title: Como funciona um gateway de aplicações
description: Este artigo fornece informações sobre como um gateway de aplicação aceita pedidos de entrada e os encaminha para o backend.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d33ec829e490ae45d38d33f5784126a71ae2d0aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506558"
---
# <a name="how-an-application-gateway-works"></a>Como funciona um gateway de aplicações

Este artigo explica como um gateway de aplicação aceita pedidos de entrada e os encaminha para o backend.

![Como um gateway de aplicação aceita um pedido](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Como um gateway de aplicação aceita um pedido

1. Antes de um cliente enviar um pedido para um gateway de aplicações, resolve o nome de domínio do gateway de aplicações utilizando um servidor do Sistema de Nome de Domínio (DNS). O Azure controla a entrada de DNS porque todos os gateways de aplicações estão no domínio azure.com.

2. O Azure DNS devolve o endereço IP ao cliente, que é o endereço IP frontal do gateway de aplicações.

3. O gateway de aplicações aceita o tráfego de entrada em um ou mais ouvintes. Um ouvinte é uma entidade lógica que verifica os pedidos de ligação. Está configurado com um endereço IP frontend, protocolo e número de porta para ligações de clientes ao gateway de aplicações.

4. Se estiver a ser utilizada uma firewall de aplicação web (WAF), o gateway de aplicação verifica os cabeçalhos de pedido e o corpo, se presente, contra as regras da WAF. Esta ação determina se o pedido é um pedido válido ou uma ameaça à segurança. Se o pedido for válido, é encaminhado para o backend. Se o pedido não for válido e a WAF estiver em modo prevenção, está bloqueada como uma ameaça à segurança. Se estiver em modo deteção, o pedido é avaliado e registado, mas ainda reencaminhado para o servidor backend.

O Azure Application Gateway pode ser usado como um equilibrador de carga de aplicação interna ou como um equilibrador de carga de aplicação virado para a Internet. Um portal de aplicações virado para a Internet utiliza endereços IP públicos. O nome DNS de um gateway de aplicações virado para a Internet é publicamente resolúvel para o seu endereço IP público. Como resultado, as portas de aplicação viradas para a Internet podem encaminhar os pedidos dos clientes para a internet.

Os gateways de aplicações internas utilizam apenas endereços IP privados. Se estiver a utilizar uma [zona DE DNS](https://docs.microsoft.com/azure/dns/private-dns-overview)personalizada ou privada, o nome de domínio deve ser internamente resolúvel para o endereço IP privado do Gateway de aplicações. Por isso, os equilibradores internos de carga só podem encaminhar pedidos de clientes com acesso a uma rede virtual para o gateway de aplicações.

## <a name="how-an-application-gateway-routes-a-request"></a>Como um gateway de aplicação encaminha um pedido

Se um pedido for válido e não bloqueado pela WAF, o gateway de aplicação avalia a regra de encaminhamento de pedidos que está associada ao ouvinte. Esta ação determina qual o pool de backend para encaminhar o pedido para.

Com base na regra de encaminhamento de pedidos, o gateway de aplicação determina se deve encaminhar todos os pedidos no ouvinte para uma piscina de backend específica, pedidos de rota para diferentes piscinas de backend com base na rota URL, ou redirecionar pedidos para outra porta ou site externo.
>[!NOTE]
>As regras são processadas na ordem em que estão listadas no portal para v1 SKU. 

Quando o gateway de aplicação seleciona o pool backend, envia o pedido para um dos servidores de backend saudáveis na piscina (y.y.y.y). A saúde do servidor é determinada por uma sonda de saúde. Se o pool de backend contiver vários servidores, o gateway de aplicações utiliza um algoritmo de robin redondo para encaminhar os pedidos entre servidores saudáveis. Esta carga equilibra os pedidos nos servidores.

Depois de o gateway de aplicações determinar o servidor backend, abre uma nova sessão de TCP com o servidor backend baseado nas definições HTTP. As definições HTTP especificam o protocolo, a porta e outras definições relacionadas com o encaminhamento que são necessárias para estabelecer uma nova sessão com o servidor de backend.

A porta e o protocolo utilizados nas definições HTTP determinam se o tráfego entre o gateway de aplicações e os servidores de backend está encriptado (realizando assim TLS de ponta a ponta) ou se não é encriptado.

Quando um gateway de aplicações envia o pedido original para o servidor backend, ele honra qualquer configuração personalizada feita nas definições HTTP relacionadas com a sobreposição do nome, caminho e protocolo do hospedeiro. Esta ação mantém a afinidade da sessão baseada em cookies, drenagem de ligação, seleção de nomes de anfitrião a partir do backend, e assim por diante.

 >[!NOTE]
>Se a piscina de backend:
> - **É um ponto final público,** o gateway de aplicações utiliza o seu FRONTend PUBLIC IP para chegar ao servidor. Se não houver um endereço IP público frontal, um é atribuído para a conectividade externa de saída.
> - **Contém um FQDN internamente resolúvel ou um endereço IP privado,** o gateway de aplicação encaminha o pedido para o servidor backend utilizando os seus endereços IP privados de exemplo.
> - **Contém um ponto final externo ou um FQDN resolúvel externamente,** o gateway de aplicação encaminha o pedido para o servidor backend utilizando o seu endereço IP público frontend. A resolução DNS baseia-se numa zona privada de DNS ou servidor DNS personalizado, se configurado, ou utiliza o DNS fornecido pelo Azure. Se não houver um endereço IP público frontal, um é atribuído para a conectividade externa de saída.

### <a name="modifications-to-the-request"></a>Alterações ao pedido

Um gateway de aplicação insere quatro cabeçalhos adicionais a todos os pedidos antes de encaminhar os pedidos para o backend. Estes cabeçalhos são x-forward-for, x-forward-proto, x-forward-forward-port, e x-original-host. O formato para cabeçalho x-forward-for é uma lista separada por vírgula de IP:porta.

Os valores válidos para x-forward-proto são HTTP ou HTTPS. A porta x-reencaminhada especifica a porta onde o pedido chegou ao gateway de aplicação. O cabeçalho do anfitrião x-original contém o cabeçalho original do anfitrião com o qual o pedido chegou. Este cabeçalho é útil na integração do site Azure, onde o cabeçalho do anfitrião de entrada é modificado antes que o tráfego seja encaminhado para o backend. Se a afinidade da sessão é ativada como uma opção, então adiciona um cookie de afinidade gerido por gateway.

Pode configurar o gateway de aplicações para modificar os cabeçalhos de pedido e resposta e URL utilizando [cabeçalhos e URL HTTP de reescrita](rewrite-http-headers-url.md) ou modificar o caminho URI utilizando uma definição de sobreposição de caminhos. No entanto, a menos que configurado para fazê-lo, todos os pedidos de entrada são proxiemente para o backend.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre os componentes do gateway de aplicações](application-gateway-components.md)
