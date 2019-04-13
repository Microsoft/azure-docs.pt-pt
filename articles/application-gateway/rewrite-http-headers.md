---
title: Reescreva os cabeçalhos HTTP no Gateway de aplicação do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da capacidade para reescrever cabeçalhos HTTP no Gateway de aplicação do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/11/2019
ms.author: absha
ms.openlocfilehash: efb7b46919066beb1382d70b676a2115ea0fb8ac
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544157"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Reescreva os cabeçalhos HTTP com o Gateway de aplicação (pré-visualização pública)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cabeçalhos HTTP permitem que o cliente e o servidor passar informações adicionais com o pedido ou a resposta. Reescrever desses cabeçalhos HTTP ajuda-o a realizar vários cenários importantes, como a adição de campos de cabeçalho relacionadas à segurança, como HSTS / X-XSS-proteção, remover o cabeçalho de resposta de campos que pode revelar informações confidenciais, informações de porta de remoção de X-reencaminhados-para cabeçalhos, etc. Gateway de aplicação suporta a capacidade de adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP enquanto o pedido e pacotes de resposta se mover entre os conjuntos de cliente e o back-end. Ele também fornece a capacidade de adicionar condições para se certificar de que os cabeçalhos especificados foram reescritos apenas quando forem cumpridas determinadas condições.
> [!NOTE]
>
> O suporte de reescrita de cabeçalho HTTP só está disponível para o [novo SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

## <a name="headers-supported-for-rewrite"></a>Cabeçalhos suportados para reescrever

A capacidade permite-lhe reescrever todos os cabeçalhos na solicitação e resposta, impedindo os cabeçalhos de anfitrião, a ligação e a atualização. Também pode utilizar o gateway de aplicação para criar os cabeçalhos personalizados e adicioná-los para o pedido e as respostas que está a ser encaminhadas através do mesmo. 

## <a name="rewrite-conditions"></a>Reescrever condições

Utilizar a reescrita condições pode avaliar o conteúdo dos pedidos de HTTP (S) e as respostas e realizar um cabeçalho de reescrever apenas quando uma ou mais condições são cumpridas. Os 3 tipos de variáveis seguintes são utilizados pelo gateway de aplicação para avaliar o conteúdo dos pedidos de HTTP (S) e as respostas:

- Cabeçalhos HTTP no pedido
- Cabeçalhos HTTP na resposta
- Variáveis de servidor de gateway de aplicação

Uma condição pode ser utilizada para avaliar se a variável especificada está presente, se a variável especificada corresponde exatamente a um valor específico, ou se a variável especificada corresponde exatamente a um padrão específico. [Biblioteca do Perl compatível Regular expressões (PCRE)](https://www.pcre.org/) é utilizado para implementar as condições de correspondência de padrão de expressão regular. Para saber mais sobre a sintaxe de expressão regular, consulte a [expressões regulares do Perl cara página](http://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Ações de reconfiguração

Reescrever ações são utilizadas para especificar os cabeçalhos de solicitação e resposta que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais. Pode criar um novo cabeçalho, modifique o valor de um cabeçalho existente ou eliminar um cabeçalho existente. O valor de um novo cabeçalho ou um cabeçalho existente pode ser definido para os seguintes tipos de valores:

- Texto 
- Cabeçalho do pedido: Para especificar um cabeçalho de pedido, terá de utilizar a sintaxe {http_req_*headerName*}
- Cabeçalho de resposta: Para especificar um cabeçalho de resposta, precisa usar a sintaxe {http_resp_*headerName*}
- Variável de servidor: Para especificar uma variável de servidor, precisa usar a sintaxe {var_*serverVariable*}
- Combinação de texto, o cabeçalho do pedido, o cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

Gateway de aplicação utiliza variáveis de servidor para armazenar informações úteis sobre o servidor, a ligação com o cliente e a solicitação atual na ligação, como o endereço IP do cliente ou o tipo de navegador da web. Estas variáveis de alterar dinamicamente, como quando uma nova página é carregada ou um formulário é reenviado. Pode utilizar estas variáveis de servidor para avaliar condições de reescrita e volte a escrever cabeçalhos. 

Gateway de aplicação suporta as seguintes variáveis de servidor:

| Variáveis de servidor suportadas | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Contém o "X-reencaminhados-For" cliente pedido campo de cabeçalho com o `client_ip` (explicado nesta tabela abaixo) variável acrescentado no formato (IP1, IP2, IP3,...). Se o campo "X-reencaminhados-For" não está presente no cabeçalho de pedido do cliente, o `add_x_forwarded_for_proxy` é igual ao `$client_ip` variável. Esta variável é particularmente útil em cenários em que os clientes a intenção de reescrever o cabeçalho X-reencaminhados-para definido pelo Gateway de aplicação, de forma a que o cabeçalho contém apenas o endereço IP sem as informações da porta. |
| ciphers_supported          | Devolve a lista de cifras suportados pelo cliente do          |
| ciphers_used               | Devolve a cadeia de caracteres de cifras utilizadas para uma ligação SSL estabelecida |
| client_ip                  | Endereço IP do cliente a partir do qual o gateway de aplicação recebeu o pedido. Se existir um proxy inverso antes do gateway de aplicação e o cliente de origem, em seguida, *client_ip* retornará o endereço IP do proxy inverso. |
| client_port                | porta de cliente                                                  |
| client_tcp_rtt             | informações sobre o cliente de conexão TCP; disponível em sistemas que suportam a opção de soquete TCP_INFO |
| client_user                | Quando utilizar a autenticação HTTP, o nome de utilizador fornecido para a autenticação |
| anfitrião                       | por esta ordem de precedência: nome de anfitrião a partir da linha de solicitação ou nome de anfitrião a partir do campo de cabeçalho de pedido de "Host" ou o nome de servidor correspondente a um pedido |
| cookie_*nome*              | o *nome* cookie                                            |
| http_method                | o método usado para fazer o pedido de URL. Por exemplo GET, POST etc. |
| http_status                | Estado da sessão, por exemplo: 200, 400, 403 etc.                       |
| http_version               | protocolo de pedido, normalmente, "HTTP 1.0", "HTTP/1.1" ou "HTTP/2.0" |
| query_string               | a lista de valor de variável de pares que se seguem o "?" no URL solicitado. |
| received_bytes             | comprimento do pedido (incluindo a linha de solicitação, o cabeçalho e o corpo do pedido) |
| request_query              | argumentos na linha de solicitação                                |
| request_scheme             | esquema de pedido, "http" ou "https"                            |
| request_uri                | original completo URI do pedido (com argumentos)                   |
| sent_bytes                 | número de bytes enviados para um cliente                             |
| server_port                | porta do servidor, que aceita um pedido                 |
| ssl_connection_protocol    | Devolve o protocolo de uma ligação estabelecida de SSL        |
| ssl_enabled                | "Se on" ligação opera no modo SSL, ou uma cadeia vazia; caso contrário |

## <a name="rewrite-configuration"></a>Reescrever a configuração

Para configurar a reescrita de cabeçalho HTTP, precisará para:

1. Crie novos objetos necessários para reescreva os cabeçalhos de http:

   - **Ação de reescrever**: utilizado para especificar o pedido e campos de cabeçalho de solicitação que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais. Pode optar por associar uma ou mais de reescrita de condição com uma ação de reescrita.

   - **Condição de reescrever**: É uma configuração opcional. Se for adicionada uma condição de reescrita, avaliará o conteúdo dos pedidos de HTTP (S) e as respostas. A decisão para executar a ação de reescrita associada com a condição de reescrita irão basear-se a solicitação de HTTP (S) ou resposta correspondidos com a condição de regravação. 

     Se mais do que um condições estão associados com uma ação, em seguida, a ação será executado apenas quando todas as condições são cumpridas, ou seja, uma operação lógica AND será efetuada.

   - **Regra de reescrever**: regra de reescrita contém vários de reescrita de ação - reescrever combinações de condição.

   - **Sequência de regra**: ajuda a determinar a ordem na qual as diferentes regras de reescrita são executadas. Isso é útil quando existem várias regras de reescrita de um conjunto de regravação. A regra de reescrita com o menor valor de seqüência de regra é executada pela primeira vez. Se fornecer a mesma seqüência de regra para duas regras de reescrita será determinística, em seguida, a ordem de execução.

   - **Conjunto de reescrever**: contém várias regras de reescrita que serão associadas a uma regra de encaminhamento do pedido.

2. Será solicitado para anexar o conjunto de reescrita (*rewriteRuleSet*) com uma regra de roteamento. Isto acontece porque a configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utilizar uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando é utilizada uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site.

Pode criar vários conjuntos de reescrita de cabeçalho de http e cada conjunto de reescrita pode ser aplicado a vários serviços de escuta. No entanto, pode aplicar apenas um reescrever definido como um serviço de escuta específico.

## <a name="common-scenarios"></a>Cenários comuns

Alguns dos cenários comuns que necessitam de reescrita de cabeçalho são mencionadas a seguir.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações de porta a partir do cabeçalho X-reencaminhados-para

Gateway de aplicação insere o cabeçalho X-reencaminhados-para a todos os pedidos antes de ela encaminha os pedidos para o back-end. O formato para este cabeçalho é uma lista separada por vírgulas de IP: porta. No entanto, poderão existir cenários em que os servidores de back-end requerem o cabeçalho para conterem apenas endereços IP. Para a realização de tais cenários, reescrita de cabeçalho pode ser utilizada para remover as informações da porta do cabeçalho X-reencaminhados-de. Uma forma de fazer isso é definir o cabeçalho à variável de servidor add_x_forwarded_for_proxy. 

![Remover a porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-the-redirection-url"></a>Modificar o URL de redirecionamento

Quando uma aplicação de back-end envia uma resposta de redirecionamento, pode querer redirecionamento do cliente para um URL diferente daquele especificado pela aplicação de back-end. Um cenário desse tipo é quando um serviço de aplicações é alojado por trás de um gateway de aplicação e exige que o cliente fazer um redirecionamento para seu caminho relativo (redirecionamento a partir do contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2). 

Uma vez que o serviço de aplicações é um serviço de multi-inquilino, utiliza o cabeçalho de anfitrião no pedido para encaminhar para o ponto final correto. Serviços de aplicações tem um nome de domínio predefinido *. azurewebsites.net (Digamos contoso.azurewebsites.net), que é diferente do nome de domínio de gateway de aplicação (por exemplo, contoso.com). Uma vez que o pedido original do cliente tem contoso.com de nome de domínio de gateway de aplicação como o nome do anfitrião, o gateway de aplicação muda o nome de anfitrião para contoso.azurewebsites.net, para que o serviço de aplicações pode encaminhar para o ponto final correto. Quando o serviço de aplicações envia uma resposta de redirecionamento, ele usa o mesmo nome de anfitrião no cabeçalho location da resposta quanto do pedido que recebe do gateway de aplicação. Por conseguinte, o cliente fará com que o pedido diretamente para contoso.azurewebsites.net/path2, em vez de utilizar o gateway de aplicação (contoso.com/path2). Ignorar o gateway de aplicação não é desejável. 

Este problema pode ser resolvido ao definir o nome de anfitrião no cabeçalho location para o nome de domínio de gateway de aplicação. Para tal, pode criar uma regra de reescrita com uma condição que avalia se o cabeçalho de localização na resposta contém azurewebsites.net introduzindo `(https?):\/\/.*azurewebsites\.net(.*)$` como o padrão e efetua uma ação para reescrever o cabeçalho de localização para que o gateway de aplicação nome de anfitrião ao introduzir `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor de cabeçalho.

![Modificar o cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar os cabeçalhos HTTP de segurança para evitar vulnerabilidades

Várias vulnerabilidades de segurança podem ser corrigidas através da implementação de cabeçalhos na resposta da aplicação. Alguns desses cabeçalhos de segurança são XSS-X-proteção, segurança de transporte Strict, conteúdo--política de segurança, etc. Pode utilizar o gateway de aplicação para definir esses cabeçalhos para todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

## <a name="limitations"></a>Limitações

- Reescrever os cabeçalhos de ligação, atualização e anfitrião ainda não é suportado.

- Os nomes de cabeçalho podem conter quaisquer carateres alfanuméricos e símbolos específicos conforme definido na [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No entanto, atualmente não suportamos a "caráter de sublinhado" (\_) caráter especial no nome do cabeçalho. 

## <a name="need-help"></a>Precisa de ajuda?

Contacte-nos [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) no caso de precisa de ajuda com esta capacidade.

## <a name="next-steps"></a>Passos Seguintes

Para saber como reescrever cabeçalhos HTTP, consulte:

-  [Reescreva os cabeçalhos HTTP com o portal do Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
-  [Reescreva os cabeçalhos HTTP com o Azure PowerShell](add-http-header-rewrite-rule-powershell.md)