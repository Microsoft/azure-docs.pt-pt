---
title: Reescrever cabeçalhos HTTP com Gateway de Aplicação Azure [ Microsoft Docs
description: Este artigo fornece uma visão geral de reescrever cabeçalhos HTTP no Gateway de Aplicação Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: ced807b25cd1e829988a1e6b7621a5f73e0edfc2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82202435"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Reescrever os cabeçalhos HTTP com o Gateway da Aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cabeçalhos HTTP permitem que um cliente e servidor passem informações adicionais com um pedido ou resposta. Ao reescrever estes cabeçalhos, pode realizar tarefas importantes, tais como adicionar campos de cabeçalho relacionados com a segurança, como hSTS/ X-XSS-Protection, remover campos de cabeçalho de resposta que possam revelar informações sensíveis e remover informações de porta dos cabeçalhos X-Forwarded-For.

O Gateway de Aplicação permite-lhe adicionar, remover ou atualizar cabeçalhos de pedido e de resposta HTTP enquanto os pacotes de pedido e de resposta se movem entre os conjuntos de cliente e back-end. Além disso, permite-lhe adicionar condições para garantir que os cabeçalhos especificados são reescritos apenas quando forem cumpridas determinadas condições.

O Application Gateway também suporta [várias variáveis](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) de servidor que o ajudam a armazenar informações adicionais sobre pedidos e respostas. Isto torna-te mais fácil criar regras poderosas de reescrita.

> [!NOTE]
>
> O suporte de reescrita do cabeçalho HTTP só está disponível para o [Standard_V2 e WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Reescrever cabeçalhos](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Cabeçalhos suportados

Pode reescrever todos os cabeçalhos em pedidos e respostas, com exceção dos cabeçalhos de anfitrião, conexão e upgrade. Também pode usar o portal de aplicação para criar cabeçalhos personalizados e adicioná-los aos pedidos e respostas que estão a ser encaminhados através dele.

## <a name="rewrite-conditions"></a>Condições de reescrita

Pode utilizar condições de reescrita para avaliar o conteúdo dos pedidos e respostas http(S) e realizar um cabeçalho reescrever apenas quando uma ou mais condições estiverem reunidas. O gateway de aplicação utiliza este tipo de variáveis para avaliar o conteúdo dos pedidos e respostas http(S):

- Cabeçalhos HTTP no pedido.
- Cabeçalhos HTTP na resposta.
- Variáveis do servidor Gateway de aplicação.

Pode usar uma condição para avaliar se uma variável especificada está presente, se uma variável especificada corresponde a um valor específico, ou se uma variável especificada corresponde a um padrão específico. Utilize a [biblioteca Perl Compatível Regular Expressions (PCRE)](https://www.pcre.org/) para configurar um padrão de expressão regular correspondente nas condições. Para aprender sobre a sintaxe de expressão regular, consulte a página principal de [expressões regulares perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Reescrever ações

Utilize ações de reescrita para especificar os cabeçalhos de pedido e resposta que pretende reescrever e o novo valor para os cabeçalhos. Pode criar um novo cabeçalho, modificar o valor de um cabeçalho existente ou eliminar um cabeçalho existente. O valor de um novo cabeçalho ou de um cabeçalho existente pode ser definido para este tipo de valores:

- Uma mensagem.
- Peça cabeçalho. Para especificar um cabeçalho de pedido, tem de utilizar a sintaxe {http_req_*cabeçalhoNome*}.
- O cabeçalho da resposta. Para especificar um cabeçalho de resposta, tem de utilizar a sintaxe {http_resp_*cabeçalhoNome*}.
- Variável do servidor. Para especificar uma variável do servidor, é necessário utilizar a sintaxe {var_*servidorVariável*}.
- Uma combinação de texto, um cabeçalho de pedido, um cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis do servidor

O Gateway de aplicações utiliza variáveis do servidor para armazenar informações úteis sobre o servidor, a ligação com o cliente e o pedido atual na ligação. Exemplos de informações armazenadas incluem o endereço IP do cliente e o tipo de navegador web. As variáveis do servidor mudam dinamicamente, por exemplo, quando uma nova página carrega ou quando um formulário é publicado. Pode utilizar estas variáveis para avaliar as condições de reescrita e reescrever cabeçalhos. Para utilizar o valor das variáveis do servidor para reescrever os cabeçalhos, terá de especificar estas variáveis na sintaxe {var_*servidorVariável*}

O gateway da aplicação suporta estas variáveis do servidor:

| Nome da variável | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | O campo de cabeçalho de pedido `client_ip` do cliente X-Forwarded-For com a variável (ver explicação mais tarde nesta tabela) anexado a ele no formato IP1, IP2, IP3, e assim por diante. Se o campo X-Forwarded-For não estiver no `add_x_forwarded_for_proxy` cabeçalho de `$client_ip` pedido do cliente, a variável é igual à variável. Esta variável é particularmente útil quando pretende reescrever o cabeçalho X-Forward-For definido pelo Gateway da Aplicação, de modo a que o cabeçalho contenha apenas o endereço IP sem a informação da porta. |
| ciphers_supported          | Uma lista das cifras apoiadas pelo cliente.          |
| ciphers_used               | A cadeia de cifras utilizada para uma ligação TLS estabelecida. |
| client_ip                  | O endereço IP do cliente a partir do qual o gateway da aplicação recebeu o pedido. Se houver um proxy inverso antes do gateway da aplicação e do cliente originário, *client_ip* devolverá o endereço IP do proxy inverso. |
| client_port                | O porto de clientes.                                                  |
| client_tcp_rtt             | Informações sobre a ligação TCP do cliente. Disponível em sistemas que suportam a opção TCP_INFO tomada. |
| client_user                | Quando a autenticação HTTP é utilizada, o nome de utilizador fornecido para autenticação. |
| anfitrião                       | Nesta ordem de precedência: o nome do anfitrião da linha de pedido, o nome do anfitrião do campo de cabeçalho do pedido do anfitrião ou o nome do servidor correspondente a um pedido. Exemplo: no *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*pedido, o valor do hospedeiro será *contoso.com* |
| cookie_*nome*              | O *biscoito de nome.*                                            |
| http_method                | O método utilizado para fazer o pedido de URL. Por exemplo, GET ou POST. |
| http_status                | O estado da sessão. Por exemplo, 200, 400 ou 403.                       |
| http_version               | O protocolo de pedido. Normalmente HTTP/1.0, HTTP/1.1, ou HTTP/2.0. |
| query_string               | A lista de pares variáveis/de valor que segue o "?" no URL solicitado. Exemplo: no *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*pedido, query_string valor será *id=123&título=fabrikam* |
| received_bytes             | A duração do pedido (incluindo a linha de pedido, cabeçalho e órgão de pedido). |
| request_query              | Os argumentos na linha de pedido.                                |
| request_scheme             | O regime de pedidos: http ou https.                            |
| request_uri                | O pedido original completo URI (com argumentos). Exemplo: no *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*pedido, request_uri valor será */article.aspx?id=123&title=fabrikam*   |
| sent_bytes                 | O número de bytes enviados a um cliente.                             |
| server_port                | A porta do servidor que aceitou um pedido.                 |
| ssl_connection_protocol    | O protocolo de uma conexão TLS estabelecida.        |
| ssl_enabled                | "Ligado" se a ligação funcionar no modo TLS. Caso contrário, uma corda vazia. |
| uri_path                   | Identifica o recurso específico no anfitrião a que o cliente web quer aceder. Esta é a parte do pedido URI sem os argumentos. Exemplo: no *http://contoso.com:8080/article.aspx?id=123&title=fabrikam*pedido, uri_path valor será */article.aspx*  |

## <a name="rewrite-configuration"></a>Reescrever a configuração

Para configurar a reescrita do cabeçalho HTTP, é necessário completar estes passos.

1. Criar os objetos necessários para reescrever o cabeçalho HTTP:

   - **Reescrever ação**: Utilizado para especificar os campos de cabeçalho de pedido e pedido que pretende reescrever e o novo valor para os cabeçalhos. Pode associar uma ou mais condições de reescrita com uma ação de reescrita.

   - **Condição de reescrita**: Uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas http(S). A ação de reescrita ocorrerá se o pedido ou resposta HTTP(S) corresponder à condição de reescrita.

     Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições são satisfeitas. Por outras palavras, a operação é uma operação lógica e operacional.

   - **Regra de reescrita**: Contém múltiplas combinações de condições de reescrita/ reescrita.

   - **Sequência de regras**: Ajuda a determinar a ordem em que as regras de reescrita executam. Esta configuração é útil quando se tem várias regras de reescrita num conjunto de reescrita. Uma regra de reescrita que tem um valor de sequência de regras inferior corre primeiro. Se atribuir a mesma sequência de regras a duas regras de reescrita, a ordem de execução não é determinista.

   - **Reescrever conjunto**: Contém múltiplas regras de reescrita que serão associadas a uma regra de encaminhamento de pedidos.

2. Fixe o conjunto de reescrita *(reescreverRuleSet*) a uma regra de encaminhamento. A configuração de reescrita é fixada ao ouvinte de origem através da regra de encaminhamento. Quando se utiliza uma regra básica de encaminhamento, a configuração de reescrita do cabeçalho está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada no caminho, a configuração de reescrita do cabeçalho é definida no mapa do caminho do URL. Nesse caso, aplica-se apenas à área específica do caminho de um local.
   > [!NOTE]
   > URL Reescrever alterar os cabeçalhos; não altera o URL para o caminho.

Pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar cada conjunto de reescrita a vários ouvintes. Mas só pode aplicar um conjunto de reescrita a um ouvinte específico.

## <a name="common-scenarios"></a>Cenários comuns

Aqui estão alguns cenários comuns para usar a reescrita do cabeçalho.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remova as informações da porta do cabeçalho X-Forward-For

O Application Gateway insere um cabeçalho X-Forward-For em todos os pedidos antes de encaminhar os pedidos para o backend. Este cabeçalho é uma lista separada de portas IP separadas. Pode haver cenários em que os servidores de back-end só precisam dos cabeçalhos para conter endereços IP. Pode utilizar a reescrita do cabeçalho para remover as informações da porta do cabeçalho X-Forwarded-For. Uma maneira de fazer isso é definir o cabeçalho para a variável do servidor add_x_forwarded_for_proxy:

![Remover a porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificar um URL de reorientação

Quando uma aplicação de back-end envia uma resposta de reorientação, é melhor redirecionar o cliente para um URL diferente do especificado pela aplicação de back-end. Por exemplo, você pode querer fazê-lo quando um serviço de aplicações está hospedado atrás de um gateway de aplicação e exige que o cliente faça uma reorientação para o seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o Serviço de Aplicações é um serviço multiarrendatário, utiliza o cabeçalho do anfitrião no pedido para encaminhar o pedido para o ponto final correto. Os serviços de aplicações têm um nome de domínio padrão de *.azurewebsites.net (digamos contoso.azurewebsites.net) que é diferente do nome de domínio do gateway da aplicação (digamos contoso.com). Uma vez que o pedido original do cliente tem o nome de domínio do gateway da aplicação (contoso.com) como nome de anfitrião, o gateway da aplicação muda o nome de anfitrião para contoso.azurewebsites.net. Faz esta alteração para que o serviço de aplicações possa encaminhar o pedido para o ponto final correto.

Quando o serviço de aplicações envia uma resposta de reorientação, utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta como o pedido que recebe do gateway da aplicação. Assim, o cliente fará o pedido diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway da aplicação (contoso.com/path2). Contornar a porta de aplicação não é desejável.

Pode resolver este problema definindo o nome de anfitrião no cabeçalho de localização para o nome de domínio do gateway da aplicação.

Aqui estão os passos para substituir o nome de anfitrião:

1. Crie uma regra de reescrita com uma condição que avalie se o cabeçalho de localização na resposta contém azurewebsites.net. Introduza `(https?):\/\/.*azurewebsites\.net(.*)$`o padrão .
1. Execute uma ação para reescrever o cabeçalho de localização de modo a que tenha o nome de anfitrião do gateway da aplicação. Faça-o entrando `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho.

![Modificar cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar cabeçalhos HTTP de segurança para prevenir vulnerabilidades

Pode corrigir várias vulnerabilidades de segurança implementando os cabeçalhos necessários na resposta da aplicação. Estes cabeçalhos de segurança incluem X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. Pode utilizar o Application Gateway para definir estes cabeçalhos para todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminar cabeçalhos indesejados

É melhor remover os cabeçalhos que revelam informações sensíveis de uma resposta HTTP. Por exemplo, é melhor remover informações como o nome do servidor de back-end, o sistema operativo ou os detalhes da biblioteca. Pode utilizar a porta de entrada de aplicação para remover estes cabeçalhos:

![Cabeçalho de apagar](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verifique a presença de um cabeçalho

Pode avaliar um cabeçalho de pedido ou resposta HTTP para a presença de um cabeçalho ou variável do servidor. Esta avaliação é útil quando pretende efetuar uma reescrita do cabeçalho apenas quando um determinado cabeçalho estiver presente.

![Verificando a presença de um cabeçalho](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitações

- Se uma resposta tiver mais do que um cabeçalho com o mesmo nome, então reescrever o valor de um desses cabeçalhos resultará em deixar cair os outros cabeçalhos na resposta. Isto normalmente pode acontecer com o cabeçalho Set-Cookie, uma vez que pode ter mais do que um cabeçalho Set-Cookie numa resposta. Um desses cenários é quando está a utilizar um serviço de aplicações com um portal de aplicações e tem configurado a afinidade de sessão baseada em cookies no gateway da aplicação. Neste caso, a resposta conterá dois cabeçalhos Set-Cookie: um `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` utilizado pelo serviço de aplicações, por exemplo: e outro para a afinidade de gateway de aplicações, por exemplo, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`. Reescrever um dos cabeçalhos set-cookie neste cenário pode resultar na remoção do outro cabeçalho Set-Cookie da resposta.

- A reescrita dos cabeçalhos de Ligação, Upgrade e Anfitrião não é suportada atualmente.

- Os nomes do cabeçalho podem conter quaisquer caracteres alfanuméricos e símbolos específicos, tal como definidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Atualmente não apoiamos o\_sublinhado ( ) personagem especial em nomes cabeçalho.

## <a name="next-steps"></a>Passos seguintes

Para aprender a reescrever cabeçalhos HTTP, consulte:

- [Reescreva os cabeçalhos HTTP usando o portal Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Reescreva os cabeçalhos HTTP usando o Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
