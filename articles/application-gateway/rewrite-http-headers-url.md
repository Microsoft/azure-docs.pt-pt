---
title: Reescreva os cabeçalhos HTTP e URL com a Azure Application Gateway | Microsoft Docs
description: Este artigo fornece uma visão geral da reescrita de cabeçalhos HTTP e URL em Azure Application Gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: 7662ef5c2c3f5ed20069f64781d222ae44e52168
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384844"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>Reescrever cabeçalhos HTTP e URL com Gateway de aplicação

 O Application Gateway permite-lhe reescrever conteúdo selecionado de pedidos e respostas. Com esta funcionalidade, pode traduzir URLs, parâmetros de cadeia de consulta, bem como modificar os cabeçalhos de pedido e resposta. Também permite adicionar condições para garantir que o URL ou os cabeçalhos especificados só são reescritos quando determinadas condições são satisfeitas. Estas condições baseiam-se nas informações do pedido e da resposta.

>[!NOTE]
>As funcionalidades de reescrita HTTP e URL estão disponíveis apenas para o [Gateway de Aplicação v2 SKU](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Reescrever tipos suportados

### <a name="request-and-response-headers"></a>Cabeçalhos de pedidos e respostas

Os cabeçalhos HTTP permitem que um cliente e servidor passem informações adicionais com um pedido ou resposta. Ao reescrever estes cabeçalhos, pode realizar tarefas importantes, tais como adicionar campos de cabeçalho relacionados com segurança como HSTS/ X-XSS-Protection, remover campos de cabeçalho de resposta que possam revelar informações sensíveis e remover informações da porta de cabeçalhos X-Forwarded-For.

O Gateway de Aplicação permite-lhe adicionar, remover ou atualizar cabeçalhos de pedido e de resposta HTTP enquanto os pacotes de pedido e de resposta se movem entre os conjuntos de cliente e back-end.

Para aprender a reescrever cabeçalhos de pedido e resposta com o Portal de Aplicação usando o portal Azure, consulte [aqui](rewrite-url-portal.md).

![img](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Cabeçalhos apoiados**

Pode reescrever todos os cabeçalhos em pedidos e respostas, com exceção dos cabeçalhos De Ligação e Atualização. Também pode utilizar o gateway de aplicações para criar cabeçalhos personalizados e adicioná-los aos pedidos e respostas que estão a ser encaminhados através dele.

### <a name="url-path-and-query-string"></a>Caminho de URL e cadeia de consulta

Com capacidade de reescrita de URL no Gateway de Aplicação, pode:

* Reescrever o nome do anfitrião, caminho e cadeia de consulta do URL de pedido 

* Opte por reescrever o URL de todos os pedidos num ouvinte ou apenas os pedidos que correspondam a uma ou mais das condições definidas. Estas condições baseiam-se nas propriedades de pedido e resposta (pedido, cabeçalho, cabeçalho de resposta e variáveis do servidor).

* Opte por encaminhar o pedido (selecione o pool backend) com base no URL original ou no URL reescrito

Para aprender a reescrever URL com o Portal Azure, consulte [aqui](rewrite-url-portal.md).

![Diagrama que descreve o processo de reescrita de um URL com Gateway de aplicação.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Reescrever ações

Utiliza ações de reescrita para especificar o URL, solicitar cabeçalhos ou cabeçalhos de resposta que pretende reescrever e o novo valor a que pretende reescrevê-las. O valor de um URL ou de um cabeçalho novo ou existente pode ser definido para este tipo de valores:

* Texto
* Pedido de cabeçalho. Para especificar um cabeçalho de pedido, é necessário utilizar a sintaxe {http_req_ *nome do cabeçalho*}
* O cabeçalho da resposta. Para especificar um cabeçalho de resposta, é necessário utilizar a sintaxe {http_resp_ *cabeçalhoName*}
* Variável do servidor. Para especificar uma variável de servidor, é necessário utilizar a sintaxe {var_ *servidorVariable*}. Consulte a lista de variáveis de servidor suportadas
* Uma combinação de texto, um cabeçalho de pedido, um cabeçalho de resposta e uma variável de servidor. 

## <a name="rewrite-conditions"></a>Condições de reescrita

Pode utilizar condições de reescrita, uma configuração opcional, para avaliar o conteúdo dos pedidos e respostas HTTP(S) e realizar uma reescrita apenas quando uma ou mais condições forem satisfeitas. O gateway de aplicações utiliza este tipo de variáveis para avaliar o conteúdo de pedidos e respostas:

* Cabeçalhos HTTP no pedido
* Cabeçalhos HTTP na resposta
* Variáveis do servidor Gateway de aplicação

Pode utilizar uma condição para avaliar se uma variável especificada está presente, se uma variável especificada corresponde a um valor específico, ou se uma variável especificada corresponde a um padrão específico. 


### <a name="pattern-matching"></a>Correspondência de padrões 

O Application Gateway utiliza expressões regulares para combinar padrões na condição. Pode utilizar a [biblioteca Perl Compatible Expresss Regular Expressions (PCRE)](https://www.pcre.org/) para configurar um padrão de expressão regular correspondente às condições. Para saber mais sobre a sintaxe de expressão regular, consulte a página principal das [expressões perl.](https://perldoc.perl.org/perlre.html)

### <a name="capturing"></a>Captura

Para capturar um sub-cordão para uso posterior, coloque parênteses em torno do subpadrão que o corresponde na definição de regex da condição. O primeiro par de parênteses armazena o seu substring em 1, o segundo par em 2, e assim por diante. Pode usar os parênteses que quiser; Perl continua a definir mais variáveis numeradas para que representes estas cordas capturadas. Alguns exemplos do [árbitro:](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm) 

*  /\d)(\d)/ # Combine dois dígitos, capturando-os nos grupos 1 e 2 

* /\d+)/ # Match one ou more digits, capturando-os todos no grupo 1 

* /\d)+/ # Match um dígito uma ou mais vezes, capturando o último no grupo 1

Uma vez capturados, pode fazê-los referenciar no conjunto de ação utilizando o seguinte formato:

* Para uma captura de cabeçalho de pedido, deve utilizar {http_req_headerName_groupNumber}. Por exemplo, {http_req_User-Agent_1} ou {http_req_User-Agent_2}
* Para uma captura de cabeçalho de resposta, deve utilizar {http_resp_headerName_groupNumber}. Por exemplo, {http_resp_Location_1} ou {http_resp_Location_2}
* Para uma variável de servidor, deve utilizar {var_serverVariableName_groupNumber}. Por exemplo, {var_uri_path_1} ou {var_uri_path_2}

Se quiser usar todo o valor, não deve mencionar o número. Basta utilizar o formato {http_req_headerName}, etc. sem o groupNumber.

## <a name="server-variables"></a>Variáveis de servidor

O Application Gateway utiliza variáveis de servidor para armazenar informações úteis sobre o servidor, a ligação com o cliente e o pedido atual na ligação. Exemplos de informação armazenada incluem o endereço IP do cliente e o tipo de navegador web. As variáveis do servidor mudam dinamicamente, por exemplo, quando uma nova página carrega ou quando um formulário é publicado. Pode utilizar estas variáveis para avaliar as condições de reescrita e reescrever cabeçalhos. Para utilizar o valor das variáveis do servidor para reescrever cabeçalhos, terá de especificar estas variáveis na sintaxe {var_ *servidorVariableName*}

O gateway de aplicações suporta as seguintes variáveis de servidor:

|   Nome da variável    |                   Descrição                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | O campo de cabeçalho de pedido de cliente X-Forwarded-For com a `client_ip` variável (ver explicação mais tarde nesta tabela) anexado a ele no formato IP1, IP2, IP3, e assim por diante. Se o campo X-Forwarded-For não estiver no cabeçalho de pedido do cliente, a `add_x_forwarded_for_proxy` variável é igual à `$client_ip` variável.   Esta variável é particularmente útil quando pretende reescrever o cabeçalho X-Forwarded-For definido pelo Application Gateway para que o cabeçalho contenha apenas o endereço IP sem a informação da porta. |
| ciphers_supported         | Uma lista das cifras apoiadas pelo cliente.               |
| ciphers_used              | A cadeia de cifras usada para uma ligação TLS estabelecida. |
| client_ip                 | O endereço IP do cliente a partir do qual o gateway de aplicação recebeu o pedido. Se houver um representante inverso antes do gateway de aplicação e do cliente originário, `client_ip` devolverá o endereço IP do representante inverso. |
| client_port               | O porto do cliente.                                             |
| client_tcp_rtt            | Informação sobre a ligação com a TCP do cliente. Disponível em sistemas que suportam a opção de tomada TCP_INFO. |
| client_user               | Quando é utilizada a autenticação HTTP, o nome de utilizador fornecido para autenticação. |
| anfitrião                      | Nesta ordem de precedência: o nome de anfitrião da linha de pedido, o nome de anfitrião do campo de cabeçalho de pedido do anfitrião ou o nome do servidor correspondente a um pedido. Exemplo: No `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, o valor do anfitrião será `contoso.com` |
| cookie_ *nome*             | O biscoito *de nome.*                                           |
| http_method               | O método usado para fazer o pedido de URL. Por exemplo, GET ou POST. |
| http_status               | O estado da sessão. Por exemplo, 200, 400 ou 403.           |
| http_version              | O protocolo de pedido. Normalmente HTTP/1.0, HTTP/1.1 ou HTTP/2.0. |
| query_string              | A lista de pares variáveis/valor que segue o "?" na URL solicitada. Exemplo: No `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, query_string valor será `id=123&title=fabrikam` |
| received_bytes            | A duração do pedido (incluindo a linha de pedido, cabeçalho e corpo de pedido). |
| request_query             | Os argumentos na linha de pedido.                           |
| request_scheme            | O sistema de pedidos: http ou https.                           |
| request_uri               | O pedido original completo URI (com argumentos). Exemplo: no pedido `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` , request_uri valor será `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | O número de bytes enviados a um cliente.                        |
| server_port               | A porta do servidor que aceitou um pedido.              |
| ssl_connection_protocol   | O protocolo de uma ligação TLS estabelecida.               |
| ssl_enabled               | "Ligado" se a ligação funcionar no modo TLS. Caso contrário, uma corda vazia. |
| uri_path                  | Identifica o recurso específico no anfitrião a que o cliente web quer aceder. Esta é a parte do pedido URI sem os argumentos. Exemplo: No `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` pedido, uri_path valor será `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Variáveis do servidor de autenticação mútua (Pré-visualização)

O Application Gateway suporta as seguintes variáveis de servidor para cenários de autenticação mútua. Utilize estas variáveis de servidor da mesma forma que acima com as outras variáveis do servidor. 

|   Nome da variável    |                   Descrição                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | O certificado de cliente em formato PEM para uma ligação SSL estabelecida. |
| client_certificate_end_date| A data final do certificado do cliente. |
| client_certificate_fingerprint| A impressão digital SHA1 do certificado de cliente para uma ligação SSL estabelecida. |
| client_certificate_issuer | A cadeia "emitente DN" do certificado de cliente para uma ligação SSL estabelecida. |
| client_certificate_serial | O número de série do certificado de cliente para uma ligação SSL estabelecida.  |
| client_certificate_start_date| A data de início do certificado do cliente. |
| client_certificate_subject| A cadeia "assunto DN" do certificado de cliente para uma ligação SSL estabelecida. |
| client_certificate_verification| O resultado da verificação do certificado de cliente: *SUCCESS,* *<reason> FAILED:*, ou *NENHUM* se não estiver presente um certificado. | 

## <a name="rewrite-configuration"></a>Reescrever configuração

Para configurar uma regra de reescrita, é necessário criar um conjunto de regras de reescrita e adicionar a configuração da regra de reescrita na região.

Um conjunto de regras de reescrita contém:

* **Solicitar associação de regras de encaminhamento:** A configuração de reescrita está associada ao ouvinte de origem através da regra de encaminhamento. Quando utiliza uma regra de encaminhamento básico, a configuração de reescrita está associada a um ouvinte de origem e é uma reescrita global do cabeçalho. Quando utiliza uma regra de encaminhamento baseada em caminhos, a configuração de reescrita é definida no mapa do caminho URL. Nesse caso, aplica-se apenas à área específica do percurso de um sítio. Pode criar vários conjuntos de reescrita e aplicar cada conjunto de reescrita a vários ouvintes. Mas pode aplicar apenas um conjunto de reescrita a um ouvinte específico.

* **Condição de reescrita**: É uma configuração opcional. As condições de reescrita avaliam o conteúdo dos pedidos e respostas HTTP(S). A ação de reescrita ocorrerá se o pedido http(S) ou a resposta corresponder à condição de reescrita. Se associar mais do que uma condição a uma ação, a ação ocorre apenas quando todas as condições estão reunidas. Por outras palavras, a operação é uma operação lógica.

* **Tipo de reescrita**: Existem 3 tipos de reescritas disponíveis:
   * Reescrita pedido de cabeçalhos 
   * Reescrevendo cabeçalhos de resposta.
   * URL de reescrita: Url de reescrita tem 3 componentes
      * **Caminho URL**: O valor para o qual o caminho deve ser reescrito. 
      * **Url Query String**: O valor a que a cadeia de consulta deve ser reescrita. 
      * **Reavalie o mapa do caminho**: Usado para determinar se o mapa do caminho URL deve ser reavaliado ou não. Se for mantido sem controlo, o caminho URL original será usado para corresponder ao padrão do caminho no mapa do caminho URL. Se for definido como verdadeiro, o mapa do caminho url será reavaliado para verificar a correspondência com o caminho reescrito. Ativar este interruptor ajuda a encaminhar o pedido para uma reescrita diferente do backend pool post.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Utilizando a reescrita de URL ou a reescrita do cabeçalho do anfitrião com firewall de aplicação web (WAF_v2 SKU)

Quando configurar a reescrita do URL ou da reescrita do cabeçalho do anfitrião, a avaliação da WAF ocorrerá após a modificação dos parâmetros do cabeçalho de pedido ou URL (pós-reescrita). E quando remover a configuração de reescrita do URL ou do cabeçalho do anfitrião no seu Gateway de Aplicação, a avaliação da WAF será feita antes da reescrita do cabeçalho (pré-reescrita). Esta ordem garante que as regras da WAF são aplicadas ao pedido final que seria recebido pelo seu pool de backend.

Por exemplo, digamos que tem a seguinte regra de reescrita do cabeçalho para o cabeçalho `"Accept" : "text/html"` - se o valor do cabeçalho for igual a , em `"Accept"` `"text/html"` seguida, reescreva o valor para `"image/png"` .

Aqui, com apenas a reescrita do cabeçalho configurada, a avaliação da WAF será feita em `"Accept" : "text/html"` . Mas quando configurar a reescrita do URL ou a reescrita do cabeçalho do anfitrião, então a avaliação da WAF será feita em `"Accept" : "image/png"` .

>[!NOTE]
> Espera-se que as operações de reescrita de URL causem um pequeno aumento na utilização do CPU do seu Gateway de Aplicação WAF. Recomenda-se que monitorize a métrica de utilização do [CPU](high-traffic-support.md) durante um breve período de tempo depois de permitir as regras de reescrita de URL no seu Gateway de aplicação WAF.

### <a name="common-scenarios-for-header-rewrite"></a>Cenários comuns para reescrever cabeçalho

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Remova as informações da porta do cabeçalho X-Forwarded-For

O Application Gateway insere um cabeçalho X-Forwarded-For em todos os pedidos antes de encaminhar os pedidos para o backend. Este cabeçalho é uma lista separada por vírgulas de portas IP. Pode haver cenários em que os servidores de back-end apenas precisam dos cabeçalhos para conter endereços IP. Pode utilizar a reescrita do cabeçalho para remover as informações da porta do cabeçalho X-Forwarded-For. Uma maneira de fazer isso é definir o cabeçalho para a variável do servidor add_x_forwarded_for_proxy. Em alternativa, também pode utilizar a variável client_ip:

![Remover porta](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Modificar um URL de redirecionamento

Quando uma aplicação de back-end envia uma resposta de redirecionamento, é melhor redirecionar o cliente para um URL diferente do especificado pela aplicação back-end. Por exemplo, é melhor fazê-lo quando um serviço de aplicações está hospedado atrás de um gateway de aplicações e requer que o cliente faça uma reorientação para o seu caminho relativo. (Por exemplo, um redirecionamento de contoso.azurewebsites.net/path1 para contoso.azurewebsites.net/path2.)

Como o Serviço de Aplicações é um serviço multitenant, utiliza o cabeçalho do anfitrião no pedido para encaminhar o pedido para o ponto final correto. Os serviços de aplicações têm um nome de domínio padrão de *.azurewebsites.net (digamos contoso.azurewebsites.net) que é diferente do nome de domínio do gateway de aplicação (digamos contoso.com). Como o pedido original do cliente tem o nome de domínio do gateway de aplicação (contoso.com) como nome de anfitrião, o gateway de aplicação altera o nome de anfitrião para contoso.azurewebsites.net. Faz esta alteração para que o serviço de aplicações possa encaminhar o pedido para o ponto final correto.

Quando o serviço de aplicações envia uma resposta de redirecionamento, utiliza o mesmo nome de anfitrião no cabeçalho de localização da sua resposta que o pedido que recebe do gateway de aplicações. Assim, o cliente fará o pedido diretamente para `contoso.azurewebsites.net/path2` em vez de passar pelo gateway de aplicação ( `contoso.com/path2` ). Contornar o portal de aplicações não é desejável.

Pode resolver este problema definindo o nome de anfitrião no cabeçalho de localização para o nome de domínio do gateway de aplicação.

Aqui estão os passos para a substituição do nome de anfitrião:

1. Crie uma regra de reescrita com uma condição que avalie se o cabeçalho de localização na resposta contém azurewebsites.net. Introduza o `(https?):\/\/.*azurewebsites\.net(.*)$` padrão.
2. Realize uma ação para reescrever o cabeçalho de localização de modo a ter o nome de anfitrião do gateway de aplicação. `{http_resp_Location_1}://contoso.com{http_resp_Location_2}`Faça-o introduzindo-o como o valor do cabeçalho. Em alternativa, também pode utilizar a variável do servidor `host` para definir o nome de anfitrião para corresponder ao pedido original.

![Modificar o cabeçalho de localização](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementar cabeçalhos HTTP de segurança para prevenir vulnerabilidades

Pode corrigir várias vulnerabilidades de segurança implementando cabeçalhos necessários na resposta à aplicação. Estes cabeçalhos de segurança incluem X-XSS-Protection, Strict-Transport-Security e Content-Security-Policy. Pode utilizar o Application Gateway para definir estes cabeçalhos para todas as respostas.

![Cabeçalho de segurança](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Eliminar cabeçalhos indesejados

É melhor remover os cabeçalhos que revelem informações sensíveis a partir de uma resposta HTTP. Por exemplo, é melhor remover informações como o nome do servidor de fundo, o sistema operativo ou os detalhes da biblioteca. Pode utilizar o gateway de aplicação para remover estes cabeçalhos:

![Apagar cabeçalho](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Verifique a presença de um cabeçalho

Pode avaliar um pedido HTTP ou cabeçalho de resposta para a presença de uma variável de cabeçalho ou servidor. Esta avaliação é útil quando pretender reescrever um cabeçalho apenas quando um determinado cabeçalho estiver presente.

![Verificação da presença de um cabeçalho](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Cenários comuns para reescrita de URL

#### <a name="parameter-based-path-selection"></a>Seleção de caminhos baseados em parâmetros

Para realizar cenários em que pretende escolher o pool de backend com base no valor de um cabeçalho, parte do URL ou cadeia de consulta no pedido, pode utilizar a combinação de capacidade de reescrita de URL e encaminhamento baseado em caminhos.  Por exemplo, se tiver um site de compras, e a categoria de produto for passada como cadeia de consulta no URL e pretender encaminhar o pedido para o backend com base na cadeia de consulta,então:

**Passo 1:**  Crie um mapa de caminhos como mostrado na imagem abaixo

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL reescrever cenário 1-1.":::

**Passo 2 (a):** Crie um conjunto de reescrita que tenha 3 regras de reescrita: 

* A primeira regra tem uma condição que verifica a *variável query_string* para *categoria=sapatos* e tem uma ação que reescreve o caminho URL para/listagem1 e tem **reavalie o mapa do caminho** ativado

* A segunda regra tem uma condição que verifica a *variável query_string* para *categoria=sacos* e tem uma ação que reescreve o caminho URL para/listagem2 e tem **reavalie o mapa do caminho** ativado

* A terceira regra tem uma condição que verifica a *variável query_string* para *categoria=acessórios* e tem uma ação que reescreve o caminho URL para/listagem3 e tem **reavalie o mapa do caminho** ativado

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL reescrever cenário 1-2.":::

 

**Passo 2 (b):** Associe este conjunto de reescrita com o caminho padrão da regra acima baseada no caminho

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL reescrever cenário 1-3.":::

Agora, se o utilizador solicitar *contoso.com/listing?category=any*, então será combinado com o caminho padrão, uma vez que nenhum dos padrões de caminho no mapa do caminho (/listing1, /listing2, /listing3) irá corresponder. Uma vez que associou o conjunto de reescrita acima com este caminho, este conjunto de reescrita será avaliado. Como a cadeia de consulta não corresponderá à condição em nenhuma das 3 regras de reescrita neste conjunto de reescrita, não será efetuada qualquer ação de reescrita e, portanto, o pedido será encaminhado inalterado para o backend associado à trajetória padrão (que é *GenericList).*

 Se o utilizador solicitar *contoso.com/listing?category=shoes, então, novamente,* o caminho predefinido será igualado. No entanto, neste caso, a condição na primeira regra irá coincidir e, portanto, será executada a ação associada à condição que reescreverá o caminho URL *para/listagens1*  e reavaliará o mapa do caminho. Quando o mapa do caminho for reavaliado, o pedido irá agora corresponder ao caminho associado ao padrão */listagens1* e o pedido será encaminhado para o backend associado a este padrão, que é ShoesListBackendPool

>[!NOTE]
>Este cenário pode ser estendido a qualquer valor de cabeçalho ou cookie, caminho DE URL, cadeia de consulta ou servidor com base na condição definida e essencialmente permite-lhe encaminhar pedidos com base nessas condições.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Reescrever parâmetros de cadeia de consulta com base no URL

Considere um cenário de um site de compras onde o link visível do utilizador deve ser simples e legível, mas o servidor backend precisa dos parâmetros de cadeia de consulta para mostrar o conteúdo certo.

Nesse caso, o Application Gateway pode capturar parâmetros a partir do URL e adicionar pares de valores-chave de ligação de consulta dos do URL. Por exemplo, digamos que o utilizador quer reescrever, `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` para, pode ser alcançado através da seguinte configuração de reescrita de URL.

**Condição** - Se a variável do servidor `uri_path` for igual ao padrão `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL reescrever cenário 2-1.":::

**Ação** - Definir caminho URL para `buy.aspx` e cadeia de consulta para `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL reescrever cenário 2-2.":::

Para obter um guia passo a passo para alcançar o cenário acima descrito, consulte [o URL de reescrita com o Gateway de aplicação utilizando o portal Azure](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL reescreve vs URL redirecionamento

Em caso de reescrita de URL, o Gateway de Aplicação reescreve o URL antes de o pedido ser enviado para o backend. Isto não irá alterar o que os utilizadores vêem no navegador porque as alterações estão escondidas do utilizador.

Em caso de redirecionamento de URL, o Application Gateway envia uma resposta de redirecionamento ao cliente com o novo URL. Isso, por sua vez, requer que o cliente reencamende o seu pedido para o novo URL fornecido no redirecionamento. URL que o utilizador vê no navegador irá atualizar para o novo URL

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Reescrever vs Redirecionamento.":::

## <a name="limitations"></a>Limitações

- Se uma resposta tiver mais do que um cabeçalho com o mesmo nome, então reescrever o valor de um desses cabeçalhos resultará em deixar cair os outros cabeçalhos na resposta. Isto pode geralmente acontecer com Set-Cookie cabeçalho, uma vez que pode ter mais de um Set-Cookie cabeçalho numa resposta. Um desses cenários é quando está a usar um serviço de aplicações com um gateway de aplicações e configurar afinidade de sessão baseada em cookies no gateway da aplicação. Neste caso, a resposta conterá dois cabeçalhos Set-Cookie: um utilizado pelo serviço de aplicações, por exemplo: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` e outro para afinidade do gateway de aplicações, por exemplo, `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Reescrever um dos cabeçalhos Set-Cookie neste cenário pode resultar na remoção do outro Set-Cookie cabeçalho da resposta.
- As reescritas não são suportadas quando o gateway de aplicações está configurado para redirecionar os pedidos ou para mostrar uma página de erro personalizada.
- Os nomes dos cabeçalhos podem conter quaisquer caracteres alfanuméricos e símbolos específicos, tal como definidos no [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Atualmente, não apoiamos o caráter especial sublinhado (_) em nomes de Cabeçalho.
- Os cabeçalhos de ligação e de upgrade não podem ser reescritos

## <a name="next-steps"></a>Passos seguintes

- [Saiba como reescrever cabeçalhos HTTP com O Gateway de Aplicação usando o portal Azure](rewrite-http-headers-portal.md)
- [Saiba como reescrever URL com o Portal de Aplicações usando o portal Azure](rewrite-url-portal.md)
