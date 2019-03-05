---
title: Reescreva os cabeçalhos HTTP no Gateway de aplicação do Azure | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da capacidade para reescrever cabeçalhos HTTP no Gateway de aplicação do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 176e6804e6c98a1b9e9ffe4af04f02748c80928b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310917"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Reescreva os cabeçalhos HTTP com o Gateway de aplicação (pré-visualização pública)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cabeçalhos HTTP permitem que o cliente e o servidor passar informações adicionais com o pedido ou a resposta. Reescrever essas ajuda de cabeçalhos HTTP é realizar vários cenários importantes, como adicionar cabeçalho relacionadas à segurança campos como HSTS / X-XSS-proteção ou remover campos de cabeçalho de resposta, que pode revelar informações confidenciais, como o nome do servidor de back-end.

Gateway de aplicação suporta agora a capacidade de reescrita de cabeçalhos de pedidos HTTP recebidos, bem como as respostas HTTP de saída. Será capaz de adicionar, remover ou atualizar cabeçalhos de solicitação e resposta HTTP, enquanto os pacotes de solicitação/resposta mover entre os conjuntos de cliente e o back-end. Pode reescrever ambos os campos de cabeçalho padrão, bem como não-padrão.

> [!NOTE] 
>
> O suporte de reescrita de cabeçalho HTTP só está disponível para o [novo SKU [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

Suporte de reescrita de cabeçalho de Gateway de aplicação oferece:

- **Cabeçalho global reescrita**: Pode reescrever cabeçalhos específicos para todos os pedidos e respostas relativas ao site.
- **Baseado no caminho do cabeçalho reescrita**: este tipo de reescrita permite reescrita de cabeçalho para apenas os pedidos e respostas que dizem respeito a apenas numa área de site específico, por exemplo uma área de carrinho de compras indicado pelos /cart/\*.

Com esta alteração, terá de:

1. Crie novos objetos necessários para reescreva os cabeçalhos de http: 
   - **RequestHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de solicitação que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.
   - **ResponseHeaderConfiguration**: este objecto é utilizado para especificar os campos de cabeçalho de resposta que pretende reescrever e o novo valor que têm de ser reescrito para os cabeçalhos originais.
   - **ActionSet**: este objeto contém as configurações dos cabeçalhos de solicitação e resposta especificados acima. 
   - **RewriteRule**: este objeto contém todos os *actionSets* especificado acima. 
   - **RewriteRuleSet**-este objeto contém todos os *rewriteRules* e tem de ser vinculado a uma regra de encaminhamento para a solicitação como-básica ou baseado no caminho.
2. Em seguida, terá de ligar o conjunto de regras de reescrita com uma regra de roteamento. Depois de criado, esta configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utilizar uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando é utilizada uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Por isso, ele só se aplica a área de caminho específico de um site.

Pode criar múltiplos conjuntos de regras de reescrita a cabeçalho de http e cada conjunto de regras de reescrita pode ser aplicado a vários serviços de escuta. No entanto, pode aplicar apenas uma regra de reescrita de http definida como um serviço de escuta específico.

Pode reescrever o valor nos cabeçalhos para:

- Valor de texto. 

  *Exemplo:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- Valor do cabeçalho de outro. 

  *Exemplo 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > Para especificar um cabeçalho de pedido, terá de utilizar a sintaxe: {http_req_headerName}

  *Exemplo 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > Para especificar um cabeçalho de resposta, precisa usar a sintaxe: {http_resp_headerName}

- Valor das variáveis de servidor com suporte.

  *Exemplo:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > Para especificar uma variável de servidor, precisa usar a sintaxe: {var_serverVariable}

- Uma combinação de acima.

## <a name="server-variables"></a>Variáveis de servidor

Variáveis de servidor armazenam informações úteis num servidor web. Estas variáveis fornecem informações sobre o servidor, a ligação com o cliente e a solicitação atual na ligação, como o endereço IP do cliente ou o tipo de navegador da web. Mudam de forma dinâmica, como quando uma nova página é carregada ou um formulário é reenviado.  Esses usuários de variáveis a utilizar pode definir cabeçalhos de solicitação, bem como cabeçalhos de resposta. 

Esse recurso oferece suporte a reconfiguração de cabeçalhos para as seguintes variáveis de servidor:

| Variáveis de servidor suportadas | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | Devolve a lista de cifras suportados pelo cliente do          |
| ciphers_used               | Devolve a cadeia de caracteres de cifras utilizadas para uma ligação SSL estabelecida |
| client_port                | porta de cliente                                                  |
| client_tcp_rtt             | informações sobre o cliente de conexão TCP; disponível em sistemas que suportam a opção de soquete TCP_INFO |
| client_user                | Quando utilizar a autenticação HTTP, o nome de utilizador fornecido para a autenticação |
| anfitrião                       | por esta ordem de precedência: nome de anfitrião a partir da linha de solicitação ou nome de anfitrião a partir do campo de cabeçalho de pedido de "Host" ou o nome de servidor correspondente a um pedido |
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

## <a name="limitations"></a>Limitações

- Esta capacidade reescrever cabeçalhos HTTP só está atualmente disponível através do Azure PowerShell, a API do Azure e SDK do Azure. Suporte através da CLI do Azure e portal estará disponível em breve.

- O suporte de reescrita de cabeçalho HTTP apenas é suportado no novo SKU [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). O recurso não será suportado no SKU antigo.

- Reescrever os cabeçalhos de ligar, atualização e anfitrião ainda não é suportado.

- Duas variáveis de servidor importantes, client_ip (o endereço IP do cliente que efetua o pedido) e cookie_*name* (o *nome* cookie), ainda não são suportados. A variável de servidor client_ip é particularmente útil em cenários em que os clientes a intenção de reescrever o cabeçalho x-reencaminhados-para definido pelo Gateway de aplicação, para que o cabeçalho contém apenas o endereço IP do cliente e não as informações de porta.

  Estas variáveis de servidor serão suportadas em breve.

- A capacidade de condicionalmente reescreva os cabeçalhos de http vai estar disponível em breve.

- Os nomes de cabeçalho podem conter quaisquer carateres alfanuméricos e símbolos específicos conforme definido na [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No entanto, atualmente não suportamos a "caráter de sublinhado" (\_) caráter especial no nome do cabeçalho. 

## <a name="need-help"></a>Precisa de ajuda?

Contacte-nos [ AGHeaderRewriteHelp@microsoft.com ](mailto:AGHeaderRewriteHelp@microsoft.com) no caso de precisa de ajuda com esta capacidade.

## <a name="next-steps"></a>Passos Seguintes

Após a aprendizagem sobre a capacidade de reescrita de cabeçalhos HTTP, aceda a [criar um dimensionamento automático e o gateway de aplicação com redundância de zona que reescreve cabeçalhos HTTP](tutorial-http-header-rewrite-powershell.md) ou [cabeçalhos de HTTP de reescrever no dimensionamento automático existente e gateway de aplicação com redundância de zona](add-http-header-rewrite-rule-powershell.md)
