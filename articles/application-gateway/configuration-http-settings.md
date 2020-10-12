---
title: Configuração de configurações de configurações de configurações de gateway de aplicação Azure HTTP
description: Este artigo descreve como configurar as definições do Gateway de Aplicação Azure HTTP.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89653022"
---
# <a name="application-gateway-http-settings-configuration"></a>Configuração de configurações de definições de Gateway HTTP de aplicação

O gateway de aplicação encaminha o tráfego para os servidores de back-end usando a configuração que especifica aqui. Depois de criar uma definição HTTP, deve associá-la a uma ou mais regras de encaminhamento de pedidos.

## <a name="cookie-based-affinity"></a>Afinidade com base no cookie

O Azure Application Gateway utiliza cookies geridos por gateways para manter as sessões de utilizador. Quando um utilizador envia o primeiro pedido para o Application Gateway, define um cookie de afinidade na resposta com um valor de haxixe que contém os detalhes da sessão, de modo que os pedidos subsequentes que transportam o cookie de afinidade serão encaminhados para o mesmo servidor de backend para manter a pegajosa. 

Esta funcionalidade é útil quando pretende manter uma sessão de utilizador no mesmo servidor e quando o estado de sessão é guardado localmente no servidor para uma sessão de utilizador. Se a aplicação não conseguir lidar com afinidade baseada em cookies, não poderá utilizar esta funcionalidade. Para usá-lo, certifique-se de que os clientes suportam cookies.

A [atualização v80](https://chromiumdash.appspot.com/schedule) [do navegador de Crómio](https://www.chromium.org/Home) trouxe um mandato em que os cookies HTTP sem atributo [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) têm de ser tratados como SameSite=Lax. No caso dos pedidos do CORS (Cross-Origin Resource Sharing), se o cookie tiver de ser enviado num contexto de terceiros, tem de utilizar *o SameSite=Nenhum; Atributos* seguros e deve ser enviado apenas https. Caso contrário, num cenário http apenas, o navegador não envia os cookies no contexto de terceiros. O objetivo desta atualização do Chrome é aumentar a segurança e evitar ataques de falsificação de pedidos de cross-site (CSRF). 

Para suportar esta mudança, a partir de 17 de fevereiro de 2020, o Application Gateway (todos os tipos SKU) irá injetar outro cookie chamado *ApplicationGatewayAffinityCORS,* além do cookie *applicationGatewayAffinity* existente. O cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele *("SameSite=Nenhum; Secure"*) para que a sessão pegajosa seja mantida mesmo para pedidos de origem cruzada.

Note que o nome de cookies de afinidade padrão é *ApplicationGatewayAffinity* e pode alterá-lo. Caso você esteja usando um nome de cookie de afinidade personalizado, um cookie adicional é adicionado com CORS como sufixo. Por exemplo, *CustomCookieNameCORS*.

> [!NOTE]
> Se o atributo *SameSite=Nenhum* estiver definido, é obrigatório que o cookie também contenha a bandeira *Secure,* e deve ser enviado em HTTPS.  Se for necessária afinidade da sessão sobre o CORS, deve migrar a sua carga de trabalho para HTTPS. Consulte a documentação TLS de descarregamento de TLS e TLS de ponta a ponta para o Gateway de Aplicações aqui – [Visão geral](ssl-overview.md), [Configure um gateway de aplicações com rescisão de TLS utilizando o portal Azure](create-ssl-portal.md), [Configurar TLS de ponta a ponta utilizando o Gateway de Aplicação com o portal](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Drenagem de ligação

A drenagem de ligação ajuda-o a remover graciosamente os membros da piscina de fundo durante as atualizações de serviço planeadas. Pode aplicar esta definição a todos os membros de uma piscina traseira, permitindo a drenagem da ligação na definição HTTP. Garante que todos os casos de desregista de um pool back-end continuem a manter as ligações existentes e a servir pedidos em curso para um tempo limite configurável e não recebam quaisquer novos pedidos ou ligações. A única exceção a esta questão são os pedidos destinados a desregistar casos devido à afinidade da sessão gerida por gateways e continuarão a ser reencaminhados para as instâncias de desregistação. A drenagem de ligação aplica-se a casos de back-end que são explicitamente removidos da piscina traseira.

## <a name="protocol"></a>Protocolo

O Application Gateway suporta http e HTTPS para pedidos de encaminhamento para os servidores de back-end. Se escolher HTTP, o tráfego para os servidores de back-end não é encriptado. Se a comunicação não encriptada não for aceitável, escolha HTTPS.

Esta definição combinada com HTTPS no ouvinte suporta [TLS de ponta a ponta](ssl-overview.md). Isto permite-lhe transmitir de forma segura dados sensíveis encriptados para a parte de trás. Cada servidor de back-end no pool back-end que tenha OLS de ponta a ponta habilitado deve ser configurado com um certificado para permitir uma comunicação segura.

## <a name="port"></a>Porta

Esta definição especifica a porta onde os servidores de back-end ouvem o tráfego a partir do gateway de aplicações. Pode configurar portas que variam entre 1 e 65535.

## <a name="request-timeout"></a>Prazo de pedido

Esta definição é o número de segundos que o gateway de aplicação espera para receber uma resposta do servidor back-end.

## <a name="override-back-end-path"></a>Anular caminho de trás

Esta definição permite-lhe configurar um caminho de encaminhamento personalizado opcional para usar quando o pedido for reencaminhado para a parte de trás. Qualquer parte do caminho de entrada que corresponda ao caminho personalizado no campo do **caminho de reposição de substituição** é copiada para o caminho reencaminhado. A tabela a seguir mostra como esta funcionalidade funciona:

- Quando a definição HTTP é anexada a uma regra básica de encaminhamento de pedidos:

  | Pedido original  | Anular caminho de trás | Pedido reencaminhado para o final |
  | ----------------- | --------------------- | ---------------------------- |
  | /casa/            | /sobreposição/            | /sobreposição/casa/              |
  | /home/secondhome/ | /sobreposição/            | /sobreposição/casa/segunda casa/   |

- Quando a definição HTTP é anexada a uma regra de encaminhamento de pedidos baseada no caminho:

  | Pedido original           | Regra do caminho       | Anular caminho de trás | Pedido reencaminhado para o final |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /regra da patula*      | /sobreposição/            | /sobreposição/casa/              |
  | /pathrule/home/secondhome/ | /regra da patula*      | /sobreposição/            | /sobreposição/casa/segunda casa/   |
  | /casa/                     | /regra da patula*      | /sobreposição/            | /sobreposição/casa/              |
  | /home/secondhome/          | /regra da patula*      | /sobreposição/            | /sobreposição/casa/segunda casa/   |
  | /pathrule/home/            | /pathrule/home* | /sobreposição/            | /sobreposição/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /sobreposição/            | /sobreposição/secondhome/        |
  | /pathrule/                 | /pathrule/      | /sobreposição/            | /sobreposição/                   |

## <a name="use-for-app-service"></a>Uso para serviço de aplicações

Este é um atalho único da UI que seleciona as duas definições necessárias para o back end do Azure App Service. Permite **escolher o nome do anfitrião a partir do endereço back-end**, e cria uma nova sonda personalizada se ainda não tiver um. (Para mais informações, consulte o [nome do anfitrião Pick a partir da](#pick-host-name-from-back-end-address)secção de definição de endereço de fundo deste artigo.) Uma nova sonda é criada, e o cabeçalho da sonda é escolhido a partir do endereço do membro de trás.

## <a name="use-custom-probe"></a>Use sonda personalizada

Esta definição associa uma [sonda personalizada](application-gateway-probe-overview.md#custom-health-probe) a uma definição HTTP. Pode associar apenas uma sonda personalizada a uma definição HTTP. Se não associar explicitamente uma sonda personalizada, a [sonda padrão](application-gateway-probe-overview.md#default-health-probe-settings) é usada para monitorizar a saúde da parte de trás. Recomendamos que crie uma sonda personalizada para um maior controlo sobre a monitorização da saúde das suas extremidades traseiras.

> [!NOTE]
> A sonda personalizada não monitoriza a saúde da piscina traseira a menos que a definição HTTP correspondente esteja explicitamente associada a um ouvinte.

## <a name="pick-host-name-from-back-end-address"></a>Escolha o nome do anfitrião a partir do endereço back-end

Esta capacidade define dinamicamente o cabeçalho do *anfitrião* no pedido para o nome de anfitrião da piscina de back-end. Utiliza um endereço IP ou FQDN.

Esta funcionalidade ajuda quando o nome de domínio da parte de trás é diferente do nome DNS do gateway de aplicação, e a parte traseira depende de um cabeçalho de anfitrião específico para resolver o ponto final correto.

Um caso exemplo são os serviços de vários inquilinos como o back-end. Um serviço de aplicações é um serviço multi-inquilino que usa um espaço compartilhado com um único endereço IP. Assim, um serviço de aplicações só pode ser acedido através dos hostnames que são configurados nas definições de domínio personalizado.

Por predefinição, o nome de domínio personalizado é *example.azurewebsites.net*. Para aceder ao serviço da sua aplicação utilizando um gateway de aplicações através de um nome de anfitrião que não está explicitamente registado no serviço de aplicações ou através do FQDN do gateway de aplicações, substitui o nome de anfitrião no pedido original para o nome de anfitrião do serviço de aplicações. Para isso, ative o nome do anfitrião da definição **do endereço de backend.**

Para um domínio personalizado cujo nome DNS personalizado existente está mapeado para o serviço de aplicações, não precisa de ativar esta definição.

> [!NOTE]
> Esta definição não é necessária para o Ambiente de Serviço de Aplicações, que é uma implementação dedicada.

## <a name="host-name-override"></a>Sobreposição de nome de anfitrião

Esta capacidade substitui o cabeçalho do *anfitrião* no pedido de entrada no gateway de aplicações pelo nome de anfitrião que especifica.

Por exemplo, se *www.contoso.com* for especificado na definição do **nome anfitrião,** o pedido original * `https://appgw.eastus.cloudapp.azure.com/path1` é alterado para * quando o pedido é `https://www.contoso.com/path1` reencaminhado para o servidor de back-end.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre a piscina de back-end](configuration-overview.md#back-end-pool)