---
title: Como funciona um gateway de aplicação
description: Este artigo fornece informações sobre como um gateway de aplicação aceita pedidos de entrada e encaminha-os para o backend.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132973"
---
# <a name="how-an-application-gateway-works"></a>Como funciona um gateway de aplicação

Este artigo explica como um gateway de aplicação aceita pedidos de entrada e encaminha-os para o backend.

![Como um gateway de aplicação aceita um pedido](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Como um gateway de aplicação aceita um pedido

1. Antes de um cliente enviar um pedido para um gateway de aplicação, resolve o nome de domínio do gateway da aplicação utilizando um servidor do Sistema de Nome de Domínio (DNS). O Azure controla a entrada do DNS porque todos os gateways de aplicação estão no domínio azure.com.

2. O Azure DNS devolve o endereço IP ao cliente, que é o endereço IP frontal do gateway da aplicação.

3. O gateway da aplicação aceita o tráfego de entrada em um ou mais ouvintes. Um ouvinte é uma entidade lógica que verifica pedidos de ligação. Está configurado com um endereço IP frontend, protocolo e número de porta para ligações de clientes ao gateway da aplicação.

4. Se estiver a ser utilizada uma firewall de aplicação web (WAF), o gateway da aplicação verifica os cabeçalhos de pedido e o organismo, se presente, contra as regras da WAF. Esta ação determina se o pedido é um pedido válido ou uma ameaça à segurança. Se o pedido for válido, é encaminhado para o backend. Se o pedido não for válido e a WAF estiver em modo de Prevenção, está bloqueada como uma ameaça à segurança. Se estiver no modo deteção, o pedido é avaliado e registado, mas ainda assim encaminhado para o servidor de backend.

O Portal de Aplicações Azure pode ser usado como um equilibrador de carga de aplicação interna ou como um equilibrador de carga de aplicação virado para a Internet. Um gateway de aplicação virado para a Internet utiliza endereços IP públicos. O nome DNS de um portal de aplicações virado para a Internet é publicamente resolúvel para o seu endereço IP público. Como resultado, os gateways de aplicações virados para a Internet podem encaminhar os pedidos dos clientes para a internet.

Os gateways de aplicação interna utilizam apenas endereços IP privados. Se estiver a utilizar uma [zona DNS](https://docs.microsoft.com/azure/dns/private-dns-overview)Personalizada ou Privada, o nome de domínio deve ser internalmente resolúvel para o endereço IP privado do Gateway de Aplicação. Por isso, os equilibradores de carga internos só podem encaminhar pedidos de clientes com acesso a uma rede virtual para o gateway da aplicação.

## <a name="how-an-application-gateway-routes-a-request"></a>Como um gateway de aplicação encaminha um pedido

Se um pedido for válido e não bloqueado pela WAF, o gateway de aplicação avalia a regra de encaminhamento de pedidos que está associada ao ouvinte. Esta ação determina qual a piscina de backend para encaminhar o pedido para.

Com base na regra de encaminhamento de pedidos, o gateway de aplicação determina se encaminha todos os pedidos no ouvinte para um pool específico de backend, pedidos de rota para diferentes piscinas de backend com base no caminho de URL, ou redirecionamento de pedidos para outro porto ou site externo.
>[!NOTE]
>As regras são processadas na ordem em que estão listadas no portal para v1 SKU. 

Quando o gateway da aplicação seleciona o pool backend, envia o pedido para um dos servidores saudáveis de backend na piscina (y.y.y.y). A saúde do servidor é determinada por uma sonda de saúde. Se o pool backend contiver vários servidores, o gateway da aplicação usa um algoritmo de robin redondo para encaminhar os pedidos entre servidores saudáveis. Esta carga equilibra os pedidos nos servidores.

Depois de o gateway da aplicação determinar o servidor backend, abre uma nova sessão de TCP com o servidor backend baseado nas definições http. As definições http especificam o protocolo, a porta e outras definições relacionadas com o encaminhamento que são necessárias para estabelecer uma nova sessão com o servidor backend.

A porta e o protocolo utilizados nas definições http determinam se o tráfego entre o gateway da aplicação e os servidores backend está encriptado (realizando assim TLS de ponta a ponta) ou se não está encriptado.

Quando um gateway de aplicação envia o pedido original para o servidor backend, ele honra qualquer configuração personalizada feita nas definições HTTP relacionadas com a sobreposição do nome de anfitrião, caminho e protocolo. Esta ação mantém a finidade de sessão baseada em cookies, drenagem de ligações, seleção de nome de anfitrião a partir do backend, e assim por diante.

 >[!NOTE]
>Se a piscina de backend:
> - **É um ponto final público,** o gateway da aplicação usa o seu IP público frontal para chegar ao servidor. Se não houver um endereço IP público frontal, um é atribuído para a conectividade externa de saída.
> - **Contém um FQDN internamente resolável ou um endereço IP privado,** o gateway da aplicação encaminha o pedido para o servidor backend utilizando os seus endereços IP privados de instância.
> - **Contém um ponto final externo ou um FQDN externamente resolável,** o gateway da aplicação encaminha o pedido para o servidor backend utilizando o seu endereço IP público frontend. A resolução DNS baseia-se numa zona Privada de DNS ou num servidor DNS personalizado, se configurado, ou utiliza o DNS fornecido por Predefinido. Se não houver um endereço IP público frontal, um é atribuído para a conectividade externa de saída.

### <a name="modifications-to-the-request"></a>Alterações ao pedido

Um gateway de aplicação insere quatro cabeçalhos adicionais para todos os pedidos antes de encaminhar os pedidos para o backend. Estes cabeçalhos são x-forward-for, x-forward-proto, x-forward-port e x-original-host. O formato para cabeçalho x-forward-for é uma lista separada de víramida de IP:porta.

Os valores válidos para x-forward-proto são HTTP ou HTTPS. A porta-x-forward especifica a porta onde o pedido chegou ao gateway de aplicação. O cabeçalho x-original-anfitrião contém o cabeçalho original do hospedeiro com o qual o pedido chegou. Este cabeçalho é útil na integração do site Azure, onde o cabeçalho do anfitrião é modificado antes que o tráfego seja encaminhado para o backend. Se a afinidade da sessão for ativada como uma opção, então adiciona um cookie de afinidade gerido por gateway.

Pode configurar o gateway da aplicação para modificar os cabeçalhos utilizando [os cabeçalhos HTTP rewrite](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou para modificar o caminho URI utilizando uma definição de sobreposição de caminhos. No entanto, a menos que esteja configurado para o fazer, todos os pedidos de entrada são enviados para o backend.

## <a name="next-steps"></a>Passos seguintes

[Conheça os componentes do gateway da aplicação](application-gateway-components.md)
