---
title: Reescreva os cabeçalhos HTTP com o Gateway de aplicação do Azure | Documentos da Microsoft
description: Este artigo fornece uma visão geral de regravação de cabeçalhos HTTP no Gateway de aplicação do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/29/2019
ms.author: absha
ms.openlocfilehash: 9160d300270bf1ab5043bee632d27bcc4b7bf332
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476029"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Reescreva os cabeçalhos HTTP com o Gateway de aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Cabeçalhos HTTP permitem que um cliente e servidor passar informações adicionais com uma solicitação ou resposta. Ao converter esses cabeçalhos, pode realizar tarefas importantes, como a adição de campos de cabeçalho relacionadas à segurança, como HSTS / X-XSS-proteção, remover os campos de cabeçalho de resposta que podem revelar informações confidenciais e remover as informações de porta de X-reencaminhados-para cabeçalhos.

O Gateway de Aplicação permite-lhe adicionar, remover ou atualizar cabeçalhos de pedido e de resposta HTTP enquanto os pacotes de pedido e de resposta se movem entre os conjuntos de cliente e back-end. Além disso, permite-lhe adicionar condições para garantir que os cabeçalhos especificados são reescritos apenas quando forem cumpridas determinadas condições.

Gateway de aplicação também suporta vários [variáveis de servidor](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) que o ajudam a armazenar informações adicionais sobre pedidos e respostas. Isto torna mais fácil de criar regras de reescrita poderosas.

> [!NOTE]
>
> O suporte de reescrita de cabeçalho HTTP só está disponível para o [Standard_V2 e WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Cabeçalhos de reconfiguração](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Cabeçalhos suportados

Pode reescrever todos os cabeçalhos em solicitações e respostas, exceto os cabeçalhos de anfitrião, a ligação e a atualização. Também pode utilizar o gateway de aplicação para criar os cabeçalhos personalizados e adicioná-los para os pedidos e respostas que está a ser encaminhadas através do mesmo.

## <a name="rewrite-conditions"></a>Reescrever condições

Pode utilizar condições de reescrita para avaliar o conteúdo dos pedidos de HTTP (S) e as respostas e realizar uma reescrita de cabeçalho apenas quando um ou mais condições são cumpridas. O gateway de aplicação utiliza estes tipos de variáveis para avaliar o conteúdo dos pedidos de HTTP (S) e as respostas:

- Cabeçalhos HTTP no pedido.
- Cabeçalhos HTTP na resposta.
- Variáveis de servidor de Gateway de aplicação.

Pode utilizar uma condição para avaliar se o numa variável especificada está presente, quer numa variável especificada corresponde a um valor específico, ou se uma variável especificada corresponde a um padrão específico. Utilizar o [biblioteca de Perl compatível Regular expressões (PCRE)](https://www.pcre.org/) para configurar as condições de correspondência de padrão de expressão regular. Para saber mais sobre a sintaxe de expressão regular, consulte a [página de principal de expressões regulares do Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Ações de reconfiguração

Utilizar ações de reescrita para especificar os cabeçalhos de solicitação e resposta que queira reescrever e o novo valor para os cabeçalhos. Pode criar um novo cabeçalho, modifique o valor de um cabeçalho existente ou eliminar um cabeçalho existente. O valor de um novo cabeçalho ou um cabeçalho existente pode ser definido para esses tipos de valores:

- Texto.
- Cabeçalho do pedido. Para especificar um cabeçalho de pedido, tem de utilizar a sintaxe {http_req_*headerName*}.
- O cabeçalho da resposta. Para especificar um cabeçalho de resposta, precisa usar a sintaxe {http_resp_*headerName*}.
- Variável de servidor. Para especificar uma variável de servidor, tem de utilizar a sintaxe {var_*serverVariable*}.
- Uma combinação de texto, um cabeçalho de pedido, um cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

Gateway de aplicação utiliza variáveis de servidor para armazenar informações úteis sobre o servidor, a ligação com o cliente e a solicitação atual na ligação. Exemplos de informações armazenadas incluem o endereço IP do cliente e o tipo de navegador da web. Variáveis de servidor mudam de forma dinâmica, por exemplo, quando uma nova página for carregada, ou quando é publicado um formulário. Pode utilizar estas variáveis para avaliar condições de reescrita e volte a escrever cabeçalhos.

Gateway de aplicação suporta estas variáveis de servidor:

| Nome da variável | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | O campo de cabeçalho de pedido X-reencaminhados-para o cliente com o `client_ip` variável (consulte a explicação mais tarde nesta tabela) acrescentado no formato IP1, IP2, IP3 e assim por diante. Se o campo de X-reencaminhados-para que não está no cabeçalho de pedido do cliente, o `add_x_forwarded_for_proxy` é igual ao `$client_ip` variável. Esta variável é particularmente útil quando desejar reescrever o cabeçalho X-reencaminhados-para definido pelo Gateway de aplicação, para que o cabeçalho contém apenas o endereço IP sem as informações da porta. |
| ciphers_supported          | Uma lista de cifras suportados pelo cliente.          |
| ciphers_used               | A cadeia de cifras utilizadas para uma ligação SSL estabelecida. |
| client_ip                  | O endereço IP do cliente a partir do qual o gateway de aplicação recebeu o pedido. Se existir um proxy inverso antes do gateway de aplicação e o cliente de origem, *client_ip* retornará o endereço IP do proxy inverso. |
| client_port                | A porta de cliente.                                                  |
| client_tcp_rtt             | Informações sobre o cliente de conexão TCP. Disponível em sistemas que suportam a opção de soquete TCP_INFO. |
| client_user                | Quando é utilizada a autenticação HTTP, o nome de utilizador fornecido para a autenticação. |
| host                       | Por esta ordem de precedência: o nome de anfitrião a partir da linha de solicitação, o nome de anfitrião do campo de cabeçalho de pedido de anfitrião ou o nome de servidor correspondente a um pedido. |
| cookie_*nome*              | O *nome* cookie.                                            |
| http_method                | o método usado para fazer o pedido de URL. Por exemplo, obter ou PUBLIQUE. |
| http_status                | O estado de sessão. Por exemplo, 200, 400 ou 403.                       |
| http_version               | O protocolo de pedido. Normalmente, HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | A lista de pares de valor/variável que se segue o "?" no URL solicitado. |
| received_bytes             | O comprimento do pedido (incluindo a linha de solicitação, o cabeçalho e o corpo do pedido). |
| request_query              | Os argumentos na linha de solicitação.                                |
| request_scheme             | O esquema de pedido: http ou https.                            |
| request_uri                | O pedido original completo URI (com argumentos).                   |
| sent_bytes                 | O número de bytes enviados para um cliente.                             |
| server_port                | A porta do servidor que aceite um pedido.                 |
| ssl_connection_protocol    | O protocolo de uma ligação SSL estabelecida.        |
| ssl_enabled                | "On" se a ligação funciona em modo SSL. Caso contrário, uma cadeia vazia. |

## <a name="rewrite-configuration"></a>Reescrever a configuração

Para configurar a reescrita de cabeçalho HTTP, terá de concluir estes passos.

1. Crie os objetos que são necessários para a reescrita de cabeçalho HTTP:

   - **Ação de reescrever**: Utilizado para especificar o pedido e campos de cabeçalho de solicitação que queira reescrever e o novo valor para os cabeçalhos. Pode associar uma ou mais condições com uma ação de reescrita de regravação.

   - **Condição de reescrever**: Uma configuração opcional. Condições de reescrita avaliam o conteúdo dos pedidos de HTTP (S) e as respostas. A ação de reescrita ocorrerá se o pedido de HTTP (S) ou resposta corresponde a condição de regravação.

     Se associar mais do que uma condição uma ação, a ação ocorre apenas quando todas as condições são cumpridas. Em outras palavras, a operação é uma operação lógica AND.

   - **Regra de reescrever**: Contém vários de reescrita de ação / reescrever combinações de condição.

   - **Sequência de regra**: Ajuda a determinar a ordem na qual as regras de reescrita executar. Esta configuração é útil quando tem várias regras de reescrita de um conjunto de regravação. Uma regra de reescrita que tem um valor de seqüência de regra inferior é executado primeira. Se atribuir a mesma seqüência de regra para duas regras de reescrita, a ordem de execução é determinística.

   - **Conjunto de reescrever**: Contém várias regras de reescrita que serão associadas uma regra de encaminhamento do pedido.

2. Anexar o conjunto de reescrita (*rewriteRuleSet*) para uma regra de roteamento. A configuração de reescrita está ligada ao serviço de escuta de origem através da regra de encaminhamento. Quando utiliza uma regra básica de encaminhamento, a configuração de reescrita de cabeçalho está associada um serviço de escuta de origem e é uma reescrita de cabeçalho global. Quando utiliza uma regra de encaminhamento baseado no caminho, a configuração de reescrita de cabeçalho é definida no mapa do caminho do URL. Nesse caso, ele só se aplica a área de caminho específico de um site.

Pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar a cada reescrita definida como vários serviços de escuta. Mas pode aplicar-se apenas um reescrever definido como um serviço de escuta específico.

## <a name="common-scenarios"></a>Cenários comuns

Seguem-se alguns cenários comuns para a utilização de reescrita de cabeçalho.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações de porta a partir do cabeçalho X-reencaminhados-para

Gateway de aplicação insere um cabeçalho X-reencaminhados-para todos os pedidos antes de ela encaminha os pedidos para o back-end. Este cabeçalho é uma lista separada por vírgulas de portas IP. Pode haver situações em que os servidores de back-end precisam apenas os cabeçalhos para conter endereços IP. Pode utilizar a Reescrita do cabeçalho para remover as informações da porta do cabeçalho X-reencaminhados-para. Uma forma de fazer isso é definir o cabeçalho para a variável de servidor add_x_forwarded_for_proxy:

![Remover a porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificar um URL de redirecionamento

Quando uma aplicação de back-end envia uma resposta de redirecionamento, pode querer redirecionamento do cliente para um URL diferente daquele especificado pela aplicação de back-end. Por exemplo, pode querer fazer isso, quando um serviço de aplicações é alojado por trás de um gateway de aplicação e exige que o cliente fazer um redirecionamento para seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o serviço de aplicações é um serviço multi-inquilino, ele usa o cabeçalho de anfitrião no pedido para encaminhar a solicitação para o ponto final correto. Serviços de aplicações tem um nome de domínio predefinido *. azurewebsites.net (Digamos contoso.azurewebsites.net), que é diferente do nome de domínio de gateway de aplicação (por exemplo, contoso.com). Uma vez que o pedido original do cliente tem o nome de domínio (contoso.com) o gateway de aplicação como o nome de anfitrião, o gateway de aplicação muda o nome de anfitrião para contoso.azurewebsites.net. Faz esta alteração, para que o serviço de aplicações pode encaminhar a solicitação para o ponto final correto.

Quando o serviço de aplicações envia uma resposta de redirecionamento, ele usa o mesmo nome de anfitrião no cabeçalho location da resposta quanto do pedido que recebe do gateway de aplicação. Portanto, o cliente fará com que o pedido diretamente para contoso.azurewebsites.net/path2 em vez de utilizar o gateway de aplicação (contoso.com/path2). Ignorar o gateway de aplicação não é desejável.

Pode resolver este problema ao definir o nome de anfitrião no cabeçalho location para o nome de domínio de gateway de aplicação.

Eis os passos para substituir o nome de anfitrião:

1. Crie uma regra de reescrita com uma condição que avalia se o cabeçalho de localização na resposta contém azurewebsites.net. Introduza o padrão `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Execute uma ação de reescrever o cabeçalho de localização para que ele tem o nome de anfitrião de gateway de aplicação. Fazê-lo ao introduzir `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor de cabeçalho.

![Modificar o cabeçalho de localização](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar os cabeçalhos HTTP de segurança para evitar vulnerabilidades

Pode corrigir várias vulnerabilidades de segurança, implementando cabeçalhos na resposta da aplicação. Esses cabeçalhos de segurança incluem a proteção de XSS X, segurança de transporte Strict e política de segurança de conteúdo. Pode utilizar o Gateway de aplicação para definir esses cabeçalhos para todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminar cabeçalhos indesejados

Pode querer remover os cabeçalhos que revelam informações confidenciais de uma resposta HTTP. Por exemplo, pode querer remover informações como o nome do servidor de back-end, sistema operativo ou os detalhes da biblioteca. Pode utilizar o gateway de aplicação para remover estes cabeçalhos:

![A eliminar cabeçalho](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verificar a presença de um cabeçalho

Pode avaliar um cabeçalho de solicitação ou resposta HTTP para a presença de uma variável do cabeçalho ou no servidor. Essa avaliação é útil quando deseja realizar uma reescrita de cabeçalho apenas quando um determinado cabeçalho está presente.

![A verificar a presença de um cabeçalho](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitações

- Se uma resposta tem mais do que um cabeçalhos com o mesmo nome, em seguida, reescrever o valor de um dos cabeçalhos de irá resultar em remover os outros cabeçalhos na resposta. Normalmente, isto pode acontecer com cabeçalho Set-Cookie, uma vez que pode ter mais de um cabeçalho de Set-Cookie numa resposta. Um cenário desse tipo é quando estiver a utilizar um serviço de aplicações com um gateway de aplicação e tiver configurado a afinidade de sessão baseada em cookies no gateway de aplicação. Neste caso, a resposta conterá 2 cabeçalhos de Set-Cookie: usada pelo serviço de aplicações, ou seja, `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e outro para a afinidade do gateway de aplicação, ou seja, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`. A regravação de um dos cabeçalhos Set-Cookie neste cenário pode resultar em remover o cabeçalho de Set-Cookie da resposta.

- Reescrever os cabeçalhos de ligação, a atualização e o anfitrião não é atualmente suportada.

- Os nomes de cabeçalho podem conter quaisquer carateres alfanuméricos e símbolos específicos conforme definido na [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Atualmente não é suportado o caráter de sublinhado (\_) caráter especial no nomes de cabeçalho.

## <a name="next-steps"></a>Passos Seguintes

Para saber como reescrever cabeçalhos HTTP, consulte:

- [Reescreva os cabeçalhos HTTP com o portal do Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Reescreva os cabeçalhos HTTP com o Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
