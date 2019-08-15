---
title: Reescrever cabeçalhos HTTP com Aplicativo Azure gateway | Microsoft Docs
description: Este artigo fornece uma visão geral da reescrita de cabeçalhos HTTP no gateway Aplicativo Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: b6f26eca0592017306eaefd3f5fecb544dc6fb36
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932199"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>Reescrever cabeçalhos HTTP com o gateway de aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Os cabeçalhos HTTP permitem que um cliente e um servidor passem informações adicionais com uma solicitação ou resposta. Ao reescrever esses cabeçalhos, você pode realizar tarefas importantes, como adicionar campos de cabeçalho relacionados à segurança, como HSTS/X-XSS-Protection, remover campos de cabeçalho de resposta que podem revelar informações confidenciais e remover informações de porta de X-encaminhadas-para cabeçalhos.

O Gateway de Aplicação permite-lhe adicionar, remover ou atualizar cabeçalhos de pedido e de resposta HTTP enquanto os pacotes de pedido e de resposta se movem entre os conjuntos de cliente e back-end. Além disso, permite-lhe adicionar condições para garantir que os cabeçalhos especificados são reescritos apenas quando forem cumpridas determinadas condições.

O gateway de aplicativo também dá suporte a várias [variáveis de servidor](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) que ajudam a armazenar informações adicionais sobre solicitações e respostas. Isso facilita a criação de regras de reescrita avançadas.

> [!NOTE]
>
> O suporte à reescrita do cabeçalho HTTP só está disponível para o [SKU Standard_V2 e WAF_v2](application-gateway-autoscaling-zone-redundant.md).

![Regravando cabeçalhos](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Cabeçalhos com suporte

Você pode reescrever todos os cabeçalhos em solicitações e respostas, exceto para os cabeçalhos host, conexão e atualização. Você também pode usar o gateway de aplicativo para criar cabeçalhos personalizados e adicioná-los às solicitações e respostas que estão sendo roteadas por meio dele.

## <a name="rewrite-conditions"></a>Condições de regravação

Você pode usar condições de reescrita para avaliar o conteúdo das solicitações e respostas de HTTP (S) e executar uma regravação de cabeçalho somente quando uma ou mais condições forem atendidas. O gateway de aplicativo usa esses tipos de variáveis para avaliar o conteúdo das solicitações e respostas HTTP (S):

- Cabeçalhos HTTP na solicitação.
- Cabeçalhos HTTP na resposta.
- Variáveis de servidor do gateway de aplicativo.

Você pode usar uma condição para avaliar se uma variável especificada está presente, se uma variável especificada corresponde a um valor específico ou se uma variável especificada corresponde a um padrão específico. Use a [biblioteca PCRE (expressões regulares comuns) do Perl](https://www.pcre.org/) para configurar a correspondência de padrão de expressão regular nas condições. Para saber mais sobre a sintaxe de expressão regular, consulte a [página principal de expressões regulares do Perl](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Ações de regravação

Você usa ações de reescrita para especificar os cabeçalhos de solicitação e resposta que deseja reescrever e o novo valor para os cabeçalhos. Você pode criar um novo cabeçalho, modificar o valor de um cabeçalho existente ou excluir um cabeçalho existente. O valor de um novo cabeçalho ou de um cabeçalho existente pode ser definido para esses tipos de valores:

- Texto.
- Cabeçalho da solicitação. Para especificar um cabeçalho de solicitação, você precisa usar a sintaxe {http_req_*HeaderName*}.
- O cabeçalho da resposta. Para especificar um cabeçalho de resposta, você precisa usar a sintaxe {http_resp_*HeaderName*}.
- Variável de servidor. Para especificar uma variável de servidor, você precisa usar a sintaxe {var_*serverVariable*}.
- Uma combinação de texto, um cabeçalho de solicitação, um cabeçalho de resposta e uma variável de servidor.

## <a name="server-variables"></a>Variáveis de servidor

O gateway de aplicativo usa variáveis de servidor para armazenar informações úteis sobre o servidor, a conexão com o cliente e a solicitação atual na conexão. Exemplos de informações armazenadas incluem o endereço IP do cliente e o tipo de navegador da Web. As variáveis de servidor são alteradas dinamicamente, por exemplo, quando uma nova página é carregada ou quando um formulário é Postado. Você pode usar essas variáveis para avaliar as condições de regravação e reescrever os cabeçalhos.

O gateway de aplicativo dá suporte a essas variáveis de servidor:

| Nome da variável | Descrição                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | O campo de cabeçalho de solicitação de cliente X encaminhado-para `client_ip` com a variável (consulte a explicação mais adiante nesta tabela) acrescentado a ele no formato IP1, IP2, IP3 e assim por diante. Se o campo X-Forwarded-for não estiver no cabeçalho de solicitação do cliente `add_x_forwarded_for_proxy` , a variável será igual `$client_ip` à variável. Essa variável é particularmente útil quando você deseja reescrever o cabeçalho X-Forwardd-for definido pelo gateway de aplicativo para que o cabeçalho contenha apenas o endereço IP sem as informações de porta. |
| ciphers_supported          | Uma lista de codificações com suporte do cliente.          |
| ciphers_used               | A cadeia de caracteres de codificações usada para uma conexão SSL estabelecida. |
| client_ip                  | O endereço IP do cliente do qual o gateway de aplicativo recebeu a solicitação. Se houver um proxy reverso antes do gateway de aplicativo e do cliente de origem, *client_ip* retornará o endereço IP do proxy reverso. |
| client_port                | A porta do cliente.                                                  |
| client_tcp_rtt             | Informações sobre a conexão TCP do cliente. Disponível em sistemas que dão suporte à opção de soquete TCP_INFO. |
| client_user                | Quando a autenticação HTTP é usada, o nome de usuário fornecido para autenticação. |
| host                       | Nesta ordem de precedência: o nome do host da linha de solicitação, o nome do host do campo de cabeçalho de solicitação do host ou o nome do servidor que corresponde a uma solicitação. |
| *nome* do cookie_              | O *nome* do cookie.                                            |
| http_method                | O método usado para fazer a solicitação de URL. Por exemplo, GET ou POST. |
| http_status                | O status da sessão. Por exemplo, 200, 400 ou 403.                       |
| http_version               | O protocolo de solicitação. Geralmente, HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string               | A lista de pares de variáveis/valores que segue o "?" na URL solicitada. |
| received_bytes             | O comprimento da solicitação (incluindo a linha de solicitação, o cabeçalho e o corpo da solicitação). |
| request_query              | Os argumentos na linha de solicitação.                                |
| request_scheme             | O esquema de solicitação: http ou HTTPS.                            |
| request_uri                | O URI de solicitação original completo (com argumentos).                   |
| sent_bytes                 | O número de bytes enviados a um cliente.                             |
| server_port                | A porta do servidor que aceitou uma solicitação.                 |
| ssl_connection_protocol    | O protocolo de uma conexão SSL estabelecida.        |
| ssl_enabled                | "Ativado" se a conexão opera no modo SSL. Caso contrário, uma cadeia de caracteres vazia. |

## <a name="rewrite-configuration"></a>Reescrever configuração

Para configurar a reescrita do cabeçalho HTTP, você precisa concluir estas etapas.

1. Crie os objetos que são necessários para a reescrita do cabeçalho HTTP:

   - **Ação**de regravação: Usado para especificar os campos de cabeçalho de solicitação e de solicitação que você deseja reescrever e o novo valor para os cabeçalhos. Você pode associar uma ou mais condições de regravação a uma ação de regravação.

   - **Condição**de regravação: Uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP (S). A ação de regravação ocorrerá se a solicitação ou resposta HTTP (S) corresponder à condição de regravação.

     Se você associar mais de uma condição a uma ação, a ação ocorrerá somente quando todas as condições forem atendidas. Em outras palavras, a operação é uma operação AND lógica.

   - **Regra**de regravação: Contém várias combinações de condição de ação de regravação/regravação.

   - **Sequência de regras**: Ajuda a determinar a ordem na qual as regras de regravação são executadas. Essa configuração é útil quando você tem várias regras de regravação em um conjunto de regravação. Uma regra de regravação que tem um valor de sequência de regra mais baixo é executada primeiro. Se você atribuir a mesma sequência de regras a duas regras de reescrita, a ordem de execução será não determinística.

   - **Conjunto**de regravação: Contém várias regras de regravação que serão associadas a uma regra de roteamento de solicitação.

2. Anexe o conjunto de regravação (*rewriteRuleSet*) a uma regra de roteamento. A configuração de regravação é anexada ao ouvinte de origem por meio da regra de roteamento. Quando você usa uma regra de roteamento básica, a configuração de reescrita de cabeçalho é associada a um ouvinte de origem e é uma reescrita de cabeçalho global. Quando você usa uma regra de roteamento com base em caminho, a configuração de reescrita de cabeçalho é definida no mapa de caminho de URL. Nesse caso, ele se aplica somente à área de caminho específica de um site.
   > [!NOTE]
   > Reescrever URL alterar os cabeçalhos; Ele não altera a URL para o caminho.

Você pode criar vários conjuntos de regravação de cabeçalho HTTP e aplicar cada regravação definida a vários ouvintes. Mas você pode aplicar apenas uma regravação definida a um ouvinte específico.

## <a name="common-scenarios"></a>Cenários comuns

Aqui estão alguns cenários comuns para usar a reescrita de cabeçalho.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remover informações de porta do cabeçalho X-Forwarded-for

O gateway de aplicativo insere um cabeçalho X/encaminhado para todas as solicitações antes de encaminhar as solicitações para o back-end. Esse cabeçalho é uma lista separada por vírgulas de portas IP. Pode haver cenários nos quais os servidores back-end só precisam dos cabeçalhos para conter endereços IP. Você pode usar a reescrita de cabeçalho para remover as informações de porta do cabeçalho X-Forwardd-for. Uma maneira de fazer isso é definir o cabeçalho para a variável de servidor add_x_forwarded_for_proxy:

![Remover porta](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Modificar uma URL de redirecionamento

Quando um aplicativo de back-end envia uma resposta de redirecionamento, talvez você queira redirecionar o cliente para uma URL diferente daquela especificada pelo aplicativo de back-end. Por exemplo, talvez você queira fazer isso quando um serviço de aplicativo é hospedado atrás de um gateway de aplicativo e requer que o cliente faça um redirecionamento para seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o serviço de aplicativo é um serviço multilocatário, ele usa o cabeçalho de host na solicitação para rotear a solicitação para o ponto de extremidade correto. Os serviços de aplicativos têm um nome de domínio padrão *. azurewebsites.net (digamos, contoso.azurewebsites.net) diferente do nome de domínio do gateway de aplicativo (digamos contoso.com). Como a solicitação original do cliente tem o nome de domínio do gateway de aplicativo (contoso.com) como o hostname, o gateway de aplicativo altera o nome do host para contoso.azurewebsites.net. Ele faz essa alteração para que o serviço de aplicativo possa encaminhar a solicitação para o ponto de extremidade correto.

Quando o serviço de aplicativo envia uma resposta de redirecionamento, ele usa o mesmo nome de host no cabeçalho de local de sua resposta como aquele na solicitação que recebe do gateway de aplicativo. Portanto, o cliente fará a solicitação diretamente para contoso.azurewebsites.net/path2 em vez de passar pelo gateway de aplicativo (contoso.com/path2). Ignorar o gateway de aplicativo não é desejável.

Você pode resolver esse problema definindo o nome do host no cabeçalho Location como o nome de domínio do gateway de aplicativo.

Aqui estão as etapas para substituir o nome do host:

1. Crie uma regra de reescrita com uma condição que seja avaliada se o cabeçalho de local na resposta contiver azurewebsites.net. Insira o padrão `(https?):\/\/.*azurewebsites\.net(.*)$`.
1. Execute uma ação para reescrever o cabeçalho de local para que ele tenha o nome de host do gateway de aplicativo. Faça isso inserindo `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho.

![Modificar cabeçalho de local](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar cabeçalhos HTTP de segurança para evitar vulnerabilidades

Você pode corrigir várias vulnerabilidades de segurança implementando os cabeçalhos necessários na resposta do aplicativo. Esses cabeçalhos de segurança incluem o X-XSS-Protection, o Strict-Transport-Security e a política de segurança de conteúdo. Você pode usar o gateway de aplicativo para definir esses cabeçalhos para todas as respostas.

![Cabeçalho de segurança](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Excluir cabeçalhos indesejados

Talvez você queira remover os cabeçalhos que revelam informações confidenciais de uma resposta HTTP. Por exemplo, talvez você queira remover informações como o nome do servidor back-end, o sistema operacional ou os detalhes da biblioteca. Você pode usar o gateway de aplicativo para remover estes cabeçalhos:

![Excluindo cabeçalho](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Verificar a presença de um cabeçalho

Você pode avaliar uma solicitação HTTP ou um cabeçalho de resposta para a presença de um cabeçalho ou uma variável de servidor. Essa avaliação é útil quando você deseja executar uma regravação de cabeçalho somente quando um determinado cabeçalho está presente.

![Verificando a presença de um cabeçalho](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Limitações

- Se uma resposta tiver mais de um cabeçalho com o mesmo nome, a regravação do valor de um desses cabeçalhos fará com que os outros cabeçalhos sejam descartados na resposta. Isso geralmente pode acontecer com o cabeçalho Set-cookie, pois você pode ter mais de um cabeçalho Set-cookie em uma resposta. Um cenário desse tipo é quando você está usando um serviço de aplicativo com um gateway de aplicativo e configurou a afinidade de sessão baseada em cookie no gateway de aplicativo. Nesse caso, a resposta conterá dois cabeçalhos Set-Cookie: um usado pelo serviço de aplicativo, por exemplo: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e outro para afinidade de gateway de aplicativo, por exemplo `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`,. Reescrever um dos cabeçalhos Set-cookie nesse cenário pode resultar na remoção do outro cabeçalho Set-cookie da resposta.

- Atualmente, não há suporte para a regravação da conexão, da atualização e dos cabeçalhos de host.

- Os nomes de cabeçalho podem conter caracteres alfanuméricos e símbolos específicos, conforme definido no [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). No momento, não há suporte para o\_caractere especial sublinhado () em nomes de cabeçalho.

## <a name="next-steps"></a>Passos seguintes

Para saber como reescrever cabeçalhos HTTP, consulte:

- [Reescrever cabeçalhos HTTP usando portal do Azure](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [Reescrever cabeçalhos HTTP usando Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
