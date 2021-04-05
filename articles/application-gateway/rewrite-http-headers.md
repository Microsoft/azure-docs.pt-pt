---
title: Reescreva os cabeçalhos HTTP com Azure Application Gateway | Microsoft Docs
description: Este artigo fornece uma visão geral da reescrita de cabeçalhos HTTP em Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 7c5b4f0d5d4b153684683963c56b7506e76d963e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575658"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Reescrever cabeçalhos HTTP com Gateway de Aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cabeçalhos HTTP permitem que um cliente e servidor passem informações adicionais com um pedido ou resposta. Ao reescrever estes cabeçalhos, pode realizar tarefas importantes, tais como adicionar campos de cabeçalho relacionados com segurança como HSTS/ X-XSS-Protection, remover campos de cabeçalho de resposta que possam revelar informações sensíveis e remover informações da porta de cabeçalhos X-Forwarded-For.

O Gateway de Aplicação permite-lhe adicionar, remover ou atualizar cabeçalhos de pedido e de resposta HTTP enquanto os pacotes de pedido e de resposta se movem entre os conjuntos de cliente e back-end. Além disso, permite-lhe adicionar condições para garantir que os cabeçalhos especificados são reescritos apenas quando forem cumpridas determinadas condições.

O Application Gateway também suporta [várias variáveis de servidor](#server-variables) que o ajudam a armazenar informações adicionais sobre pedidos e respostas. Isto facilita-lhe a criação de regras de reescrita poderosas.

> [!NOTE]
>
> O suporte de reescrita do cabeçalho HTTP só está disponível para o [Standard_V2 e WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Reescrita cabeçalhos](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Cabeçalhos apoiados

Pode reescrever todos os cabeçalhos em pedidos e respostas, com exceção dos cabeçalhos host, Connection e Upgrade. Também pode utilizar o gateway de aplicações para criar cabeçalhos personalizados e adicioná-los aos pedidos e respostas que estão a ser encaminhados através dele.

## <a name="rewrite-conditions"></a>Reescrever condições

Pode utilizar condições de reescrita para avaliar o conteúdo dos pedidos e respostas HTTP(S) e efetuar uma reescrita do cabeçalho apenas quando uma ou mais condições forem satisfeitas. O gateway de aplicações utiliza este tipo de variáveis para avaliar o conteúdo dos pedidos e respostas HTTP(S):

- Cabeçalhos HTTP no pedido.
- Cabeçalhos HTTP na resposta.
- Variáveis do servidor Gateway de aplicação.

Pode utilizar uma condição para avaliar se uma variável especificada está presente, se uma variável especificada corresponde a um valor específico, ou se uma variável especificada corresponde a um padrão específico. Utiliza a [biblioteca Perl Compatible Expresss Regular Expressions (PCRE)](https://www.pcre.org/) para configurar um padrão de expressão regular correspondente às condições. Para saber mais sobre a sintaxe de expressão regular, consulte a página principal das [expressões perl.](https://perldoc.perl.org/perlre.html)

## <a name="rewrite-actions"></a>Reescrever ações

Utiliza ações de reescrita para especificar os cabeçalhos de pedido e resposta que pretende reescrever e o novo valor para os cabeçalhos. Pode criar um novo cabeçalho, modificar o valor de um cabeçalho existente ou apagar um cabeçalho existente. O valor de um novo cabeçalho ou de um cabeçalho existente pode ser definido para este tipo de valores:

- Texto.
- Pedido de cabeçalho. Para especificar um cabeçalho de pedido, é necessário utilizar a sintaxe {http_req_ *nome do cabeçalho*}.
- O cabeçalho da resposta. Para especificar um cabeçalho de resposta, é necessário utilizar a sintaxe {http_resp_ *nome do cabeçalho*}.
- Variável do servidor. Para especificar uma variável de servidor, é necessário utilizar a sintaxe {var_ *servidorVariable*}.
- Uma combinação de texto, um cabeçalho de pedido, um cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

O Application Gateway utiliza variáveis de servidor para armazenar informações úteis sobre o servidor, a ligação com o cliente e o pedido atual na ligação. Exemplos de informação armazenada incluem o endereço IP do cliente e o tipo de navegador web. As variáveis do servidor mudam dinamicamente, por exemplo, quando uma nova página carrega ou quando um formulário é publicado. Pode utilizar estas variáveis para avaliar as condições de reescrita e reescrever cabeçalhos. Para utilizar o valor das variáveis do servidor para reescrever cabeçalhos, terá de especificar estas variáveis na sintaxe {var_ *servidorVariável*}

O gateway de aplicações suporta estas variáveis de servidor:

| Nome da variável | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | O campo de cabeçalho de pedido de cliente X-Forwarded-For com a `client_ip` variável (ver explicação mais tarde nesta tabela) anexado a ele no formato IP1, IP2, IP3, e assim por diante. Se o campo X-Forwarded-For não estiver no cabeçalho de pedido do cliente, a `add_x_forwarded_for_proxy` variável é igual à `$client_ip` variável. Esta variável é particularmente útil quando pretende reescrever o cabeçalho X-Forwarded-For definido pelo Application Gateway para que o cabeçalho contenha apenas o endereço IP sem a informação da porta. |
| ciphers_supported          | Uma lista das cifras apoiadas pelo cliente.          |
| ciphers_used               | A cadeia de cifras usada para uma ligação TLS estabelecida. |
| client_ip                  | O endereço IP do cliente a partir do qual o gateway de aplicação recebeu o pedido. Se houver um representante inverso antes do gateway de aplicação e do cliente originário, *client_ip* devolverão o endereço IP do representante inverso. |
| client_port                | O porto do cliente.                                                  |
| client_tcp_rtt             | Informação sobre a ligação com a TCP do cliente. Disponível em sistemas que suportam a opção de tomada TCP_INFO. |
| client_user                | Quando é utilizada a autenticação HTTP, o nome de utilizador fornecido para autenticação. |
| anfitrião                       | Nesta ordem de precedência: o nome de anfitrião da linha de pedido, o nome de anfitrião do campo de cabeçalho de pedido do anfitrião ou o nome do servidor correspondente a um pedido. Exemplo: no `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, o valor do anfitrião será *contoso.com* |
| cookie_ *nome*              | O biscoito *de nome.*                                            |
| http_method                | O método usado para fazer o pedido de URL. Por exemplo, GET ou POST. |
| http_status                | O estado da sessão. Por exemplo, 200, 400 ou 403.                       |
| http_version               | O protocolo de pedido. Normalmente HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | A lista de pares variáveis/valor que segue o "?" na URL solicitada. Exemplo: no pedido `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , query_string valor será *id=123&título=fabrikam* |
| received_bytes             | A duração do pedido (incluindo a linha de pedido, cabeçalho e corpo de pedido). |
| request_query              | Os argumentos na linha de pedido.                                |
| request_scheme             | O sistema de pedidos: http ou https.                            |
| request_uri                | O pedido original completo URI (com argumentos). Exemplo: no pedido `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` , request_uri valor será */article.aspx?id=123&título=fabrikam*   |
| sent_bytes                 | O número de bytes enviados a um cliente.                             |
| server_port                | A porta do servidor que aceitou um pedido.                 |
| ssl_connection_protocol    | O protocolo de uma ligação TLS estabelecida.        |
| ssl_enabled                | "Ligado" se a ligação funcionar no modo TLS. Caso contrário, uma corda vazia. |
| uri_path                   | Identifica o recurso específico no anfitrião a que o cliente web quer aceder. Esta é a parte do pedido URI sem os argumentos. Exemplo: no `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, uri_path valor será */artigo.aspx*  |

## <a name="rewrite-configuration"></a>Reescrever configuração

Para configurar a reescrita do cabeçalho HTTP, é necessário completar estes passos.

1. Crie os objetos necessários para reescrever o cabeçalho HTTP:

   - **Ação de reescrita**: Usado para especificar os campos de pedido e pedido que pretende reescrever e o novo valor para os cabeçalhos. Pode associar uma ou mais condições de reescrita com uma ação de reescrita.

   - **Condição de reescrita**: Uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas HTTP(S). A ação de reescrita ocorrerá se o pedido http(S) ou a resposta corresponder à condição de reescrita.

     Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições estão reunidas. Por outras palavras, a operação é uma operação lógica.

   - **Regra de reescrita**: Contém combinações de condição de reescrita múltiplas/ reescrita.

   - **Sequência de** regras : Ajuda a determinar a ordem pela qual as regras de reescrita executam. Esta configuração é útil quando tem várias regras de reescrita num conjunto de reescrita. Uma regra de reescrita que tem um valor de sequência de regras mais baixo corre primeiro. Se atribuir a mesma sequência de regras a duas regras de reescrita, a ordem de execução não é determinística.

   - **Conjunto de reescrita**: Contém várias regras de reescrita que serão associadas a uma regra de encaminhamento de pedidos.

2. Fixe o conjunto de *reescrita (reescreversetset)* a uma regra de encaminhamento. A configuração de reescrita é anexada ao ouvinte de origem através da regra de encaminhamento. Quando utiliza uma regra de encaminhamento básico, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada no caminho, a configuração de reescrita do cabeçalho é definida no mapa do caminho URL. Nesse caso, aplica-se apenas à área específica do percurso de um sítio.
   > [!NOTE]
   > URL Reescreve os cabeçalhos; não altera o URL para o caminho.

Pode criar vários conjuntos de reescrita do cabeçalho HTTP e aplicar cada conjunto de reescrita a vários ouvintes. Mas pode aplicar apenas um conjunto de reescrita a um ouvinte específico.

## <a name="common-scenarios"></a>Cenários comuns

Aqui estão alguns cenários comuns para a reescrita do cabeçalho.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remova as informações da porta do cabeçalho X-Forwarded-For

O Application Gateway insere um cabeçalho X-Forwarded-For em todos os pedidos antes de encaminhar os pedidos para o backend. Este cabeçalho é uma lista separada por vírgulas de portas IP. Pode haver cenários em que os servidores de back-end apenas precisam dos cabeçalhos para conter endereços IP. Pode utilizar a reescrita do cabeçalho para remover as informações da porta do cabeçalho X-Forwarded-For. Uma maneira de fazer isto é definir o cabeçalho para a variável do servidor add_x_forwarded_for_proxy:

![Remover porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificar um URL de redirecionamento

Quando uma aplicação de back-end envia uma resposta de redirecionamento, é melhor redirecionar o cliente para um URL diferente do especificado pela aplicação back-end. Por exemplo, é melhor fazê-lo quando um serviço de aplicações está hospedado atrás de um gateway de aplicações e requer que o cliente faça uma reorientação para o seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o Serviço de Aplicações é um serviço multitenant, utiliza o cabeçalho do anfitrião no pedido para encaminhar o pedido para o ponto final correto. Os serviços de aplicações têm um nome de domínio padrão de *.azurewebsites.net (digamos contoso.azurewebsites.net) que é diferente do nome de domínio do gateway de aplicação (digamos contoso.com). Como o pedido original do cliente tem o nome de domínio do gateway de aplicação (contoso.com) como nome de anfitrião, o gateway de aplicação altera o nome de anfitrião para contoso.azurewebsites.net. Faz esta alteração para que o serviço de aplicações possa encaminhar o pedido para o ponto final correto.

Quando o serviço de aplicações envia uma resposta de redirecionamento, utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta que o pedido que recebe do gateway de aplicações. Assim, o cliente fará o pedido diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicação (contoso.com/path2). Contornar o portal de aplicações não é desejável.

Pode resolver este problema definindo o nome de anfitrião no cabeçalho de localização para o nome de domínio do gateway de aplicação.

Aqui estão os passos para a substituição do nome de anfitrião:

1. Crie uma regra de reescrita com uma condição que avalie se o cabeçalho de localização na resposta contém azurewebsites.net. Introduza o `(https?):\/\/.*azurewebsites\.net(.*)$` padrão.
1. Realize uma ação para reescrever o cabeçalho de localização de modo a ter o nome de anfitrião do gateway de aplicação. `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`Faça-o introduzindo-o como o valor do cabeçalho.

![Modificar o cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar cabeçalhos HTTP de segurança para prevenir vulnerabilidades

Pode corrigir várias vulnerabilidades de segurança implementando cabeçalhos necessários na resposta à aplicação. Estes cabeçalhos de segurança incluem X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. Pode utilizar o Application Gateway para definir estes cabeçalhos para todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminar cabeçalhos indesejados

É melhor remover os cabeçalhos que revelem informações sensíveis a partir de uma resposta HTTP. Por exemplo, é melhor remover informações como o nome do servidor de fundo, o sistema operativo ou os detalhes da biblioteca. Pode utilizar o gateway de aplicação para remover estes cabeçalhos:

![Apagar cabeçalho](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verifique a presença de um cabeçalho

Pode avaliar um pedido HTTP ou cabeçalho de resposta para a presença de uma variável de cabeçalho ou servidor. Esta avaliação é útil quando pretender reescrever um cabeçalho apenas quando um determinado cabeçalho estiver presente.

![Verificação da presença de um cabeçalho](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitações

- Se uma resposta tiver mais do que um cabeçalho com o mesmo nome, então reescrever o valor de um desses cabeçalhos resultará em deixar cair os outros cabeçalhos na resposta. Isto pode geralmente acontecer com Set-Cookie cabeçalho, uma vez que pode ter mais de um Set-Cookie cabeçalho numa resposta. Um desses cenários é quando está a usar um serviço de aplicações com um gateway de aplicações e configurar afinidade de sessão baseada em cookies no gateway da aplicação. Neste caso, a resposta conterá dois cabeçalhos Set-Cookie: um utilizado pelo serviço de aplicações, por exemplo: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e outro para afinidade do gateway de aplicações, por exemplo, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Reescrever um dos cabeçalhos Set-Cookie neste cenário pode resultar na remoção do outro Set-Cookie cabeçalho da resposta.

- As reescritas não são suportadas quando o gateway de aplicações está configurado para redirecionar os pedidos ou para mostrar uma página de erro personalizada.

- A reescrita dos cabeçalhos De Ligação, Atualização e Anfitrião não é suportada atualmente.

- Os nomes dos cabeçalhos podem conter quaisquer caracteres alfanuméricos e símbolos específicos, tal como definidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Atualmente, não apoiamos o carácter especial sublinhado \_ em nomes de Cabeçalho.

## <a name="next-steps"></a>Passos seguintes

Para aprender a reescrever cabeçalhos HTTP, consulte:

- [Reescrever cabeçalhos de HTTP com o portal do Azure](./rewrite-http-headers-portal.md)
- [Reescrever cabeçalhos HTTP usando Azure PowerShell](add-http-header-rewrite-rule-powershell.md)