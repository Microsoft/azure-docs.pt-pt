---
title: Como funciona um gateway de aplicação
description: Este artigo fornece informações sobre o funcionamento de um gateway de aplicação
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a16421182f533f5aa2ad4bcc2e58e910cc7e8ca6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702417"
---
# <a name="how-an-application-gateway-works"></a>Como funciona um gateway de aplicação

Este artigo explica como um gateway de aplicação aceita os pedidos recebidos e encaminha-os para o back-end.

![Como um gateway de aplicação aceita um pedido](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Como um gateway de aplicação aceita um pedido

1. Antes de um cliente envia um pedido para um gateway de aplicação, resolver o nome de domínio de gateway de aplicação utilizando um servidor de sistema de nomes de domínio (DNS). Azure controla a entrada DNS, uma vez que todos os gateways de aplicação estão no domínio azure.com.

2. O DNS do Azure devolve o endereço IP para o cliente, o que é o endereço IP de front-end do gateway de aplicação.

3. O gateway de aplicação aceita tráfego de entrada num ou mais serviços de escuta. Um serviço de escuta é uma entidade lógica que verifica a existência de pedidos de ligação. Este é configurado com um endereço IP de front-end, o protocolo e o número de porta para ligações de clientes para o gateway de aplicação.

4. Se uma firewall de aplicações web (WAF) está a ser utilizado, o gateway de aplicação verifica os cabeçalhos de pedido e o corpo, se estiver presente, com regras de WAF. Esta ação determina se o pedido de solicitação válida ou uma ameaça de segurança. Se o pedido for válido, é encaminhado para o back-end. Se o pedido não é válido, ele está bloqueado como uma ameaça de segurança.

O Gateway de aplicação do Azure pode ser utilizado como um balanceador de carga interno do aplicativo ou como um balanceador de carga da aplicação de acesso à internet. Um gateway de aplicação de acesso à internet utiliza endereços IP públicos. O nome DNS de um gateway de aplicação de acesso à internet é resolvível publicamente para o respetivo endereço IP público. Como resultado, os gateways de aplicação de acesso à internet podem encaminhar pedidos de cliente à internet.

Gateways de aplicação interna utilizam apenas endereços IP privados. O nome DNS de um gateway de aplicação interna é resolvível publicamente para o respetivo endereço IP privado. Por conseguinte, balanceadores de carga internos só podem encaminhar pedidos de clientes com o acesso a uma rede virtual para o gateway de aplicação.

Gateways de acesso à internet e aplicativos internos encaminham pedidos para servidores de back-end utilizar endereços IP privados. Servidores de back-end não tem endereços IP públicos para receber pedidos de um interno ou de um gateway de aplicação de acesso à internet.

## <a name="how-an-application-gateway-routes-a-request"></a>Como um gateway de aplicação encaminha um pedido

Se um pedido é válido ou uma WAF não está em utilização, o gateway de aplicação avalia a regra de encaminhamento de pedido que está associada com o serviço de escuta. Esta ação determina qual conjunto de back-end para encaminhar o pedido para.

As regras são processadas na ordem que estes estão listados no portal. Com base na regra de encaminhamento de pedido, o gateway de aplicação determina se deve encaminhar todos os pedidos de serviço de escuta a um conjunto de back-end específico, de encaminhar os pedidos de conjuntos de back-end diferente com base no caminho do URL ou redirecionar pedidos para outra porta ou externos site.

Quando o gateway de aplicação seleciona o conjunto de back-end, envia o pedido para um dos servidores de back-end em bom estado no conjunto (y.y.y.y). O estado de funcionamento do servidor é determinado por uma sonda de estado de funcionamento. Se o conjunto de back-end contém vários servidores, o gateway de aplicação utiliza um algoritmo de round robin para encaminhar os pedidos entre servidores de bom estado de funcionamento. Esta carga equilibra as solicitações nos servidores.

Depois do gateway de aplicação determina o servidor de back-end, é aberta uma nova sessão TCP com o servidor de back-end com base nas definições de HTTP. Definições de HTTP de especificar o protocolo, porta e outras definições de relacionados com o encaminhamento de mensagens em fila que são necessárias para estabelecer uma nova sessão com o servidor de back-end.

A porta e protocolo utilizado nas definições de HTTP determinam se o tráfego entre os servidores de gateway e o back-end de aplicação é encriptado (assim a realização de SSL ponto a ponto) ou não está encriptado.

Quando um gateway de aplicação envia a solicitação original para o servidor de back-end, ele respeita qualquer efetuada nas definições de HTTP relacionadas ao substituir o nome de anfitrião, o caminho e o protocolo de configuração personalizada. Esta ação mantém a afinidade de sessão baseada em cookies, seleção de drenagem, nome de anfitrião de ligação do back-end e assim por diante.

Um gateway de aplicação interno utiliza endereços IP privados. O nome DNS de um gateway de aplicação interna pode ser resolvido para o respetivo endereço IP privado. Como resultado, balanceadores de carga internos só podem encaminhar pedidos de clientes com acesso à rede virtual para o gateway de aplicação.

 >[!NOTE]
 >Ambos os gateways de aplicação com acesso à internet e interno encaminham pedidos para servidores de back-end através da utilização de endereços IP privados. Esta ação acontece quando o recurso de conjunto de back-end que contém um endereço IP privado, configuração de NIC de VM ou um endereço resolvido internamente. Se o conjunto de back-end:
> - **É um ponto final público**, o gateway de aplicação utiliza o seu IP público de front-end para alcançar o servidor. Se não existir um endereço IP público de front-end, um é atribuído para a saída conectividade externa.
> - **Contém um FQDN resolvível internamente ou um endereço IP privado**, o gateway de aplicação encaminha o pedido para o servidor de back-end através de seus endereços IP privados de instância.
> - **Contém um ponto final externo ou um FQDN resolvível externamente**, o gateway de aplicação encaminha o pedido para o servidor de back-end com o respetivo endereço IP público de front-end. A resolução DNS se baseia numa zona DNS privada ou o servidor DNS personalizado, se configurado ou utiliza a predefinição DNS fornecida pelo Azure. Se não existir um endereço IP público de front-end, um é atribuído para a saída conectividade externa.

### <a name="modifications-to-the-request"></a>Modificações ao pedido

Um gateway de aplicação insere quatro cabeçalhos adicionais a todos os pedidos antes de ela encaminha os pedidos para o back-end. Esses cabeçalhos são x-reencaminhados-para, proto x reencaminhados, porta x reencaminhados e x-original-anfitrião. O formato para o cabeçalho x-reencaminhados-para-se uma lista separada por vírgulas de IP: porta.

Os valores válidos para proto x reencaminhados são HTTP ou HTTPS. Porta X reencaminhados Especifica a porta em que o pedido foi atingido o gateway de aplicação. Cabeçalho de X-original-anfitrião contém o cabeçalho de anfitrião original com a qual a solicitação chegou. Este cabeçalho é útil em integração de Web site do Azure, onde o cabeçalho de anfitrião recebido é modificado antes do tráfego é encaminhado para o back-end. Se a afinidade de sessão está ativada como uma opção, em seguida, adiciona um cookie de afinidade gerida do gateway.

Pode configurar o gateway de aplicação para modificar os cabeçalhos usando [cabeçalhos HTTP reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou para modificar o caminho do URI utilizando uma definição de substituição do caminho. No entanto, a menos que configurado para tal, todos os pedidos recebidos são transmitidas por proxy para o back-end.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre os componentes de gateway de aplicação](application-gateway-components.md)
