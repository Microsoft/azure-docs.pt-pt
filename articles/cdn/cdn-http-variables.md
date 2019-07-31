---
title: Variáveis HTTP para o mecanismo de regras da CDN do Azure | Microsoft Docs
description: Variáveis HTTP permitem que você recupere metadados de solicitação e resposta HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593823"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variáveis HTTP para o mecanismo de regras da CDN do Azure
As variáveis HTTP fornecem os meios pelos quais você pode recuperar metadados de solicitação e resposta HTTP. Esses metadados podem então ser usados para alterar dinamicamente uma solicitação ou uma resposta. O uso de variáveis HTTP é restrito aos seguintes recursos do mecanismo de regras:

- [Regravação de chave de cache](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modificar cabeçalho de solicitação do cliente](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modificar cabeçalho de resposta do cliente](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Redirecionamento de URL](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Regravação de URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definições
A tabela a seguir descreve as variáveis HTTP com suporte. Um valor em branco é retornado quando os metadados geográficos (por exemplo, código postal) não estão disponíveis para uma solicitação específica.


| Nome | Variável | Descrição | Valor da amostra |
| ---- | -------- | ----------- | ------------ |
| ASN (solicitante) | %{geo_asnum} | Indica o número as do solicitante. <br /><br />**Preterido:** % {virt_dst_asnum}. <br />Essa variável foi preterida em favor de% {geo_asnum}. Embora uma regra que use essa variável preterida continue a funcionar, você deve atualizá-la para usar a nova variável. | AS15133 |
| Cidade (solicitante) | %{geo_city} | Indica a cidade do solicitante. | Los Angeles |
| Continente (solicitante) | %{geo_continent} | Indica o continente do solicitante por meio de sua abreviação. <br />Valores válidos são: <br />AF África<br />COMO Ásia<br />UE Europa<br />NA América do Norte<br />OC: Oceania<br />ADMINISTRADOR América do Sul<br /><br />**Preterido:** % {virt_dst_continent}. <br />Essa variável foi preterida em favor de% {geo_continent}. <br />Embora uma regra que use essa variável preterida continue a funcionar, você deve atualizá-la para usar a nova variável.| N/A |
| Valor do cookie | %{cookie_Cookie} | Retorna o valor correspondente à chave de cookie identificada pelo termo do cookie. | Exemplo de uso: <br />%{cookie__utma}<br /><br />Valor de exemplo:<br />111662281.2.10.1222100123 |
| País (solicitante) | %{geo_country} | Indica o país de origem do solicitante por meio de seu código de país. <br />**Preterido:** % {virt_dst_country}. <br /><br />Essa variável foi preterida em favor de% {geo_country}. Embora uma regra que use essa variável preterida continue a funcionar, você deve atualizá-la para usar a nova variável. | EUA |
| Área de mercado designada (solicitante) | %{geo_dma_code} |Indica o mercado de mídia do solicitante por seu código de região. <br /><br />Esse campo só é aplicável a solicitações originadas no Estados Unidos.| 745 |
| Método de solicitação HTTP | %{request_method} | Indica o método de solicitação HTTP. | GET |
| Código de status HTTP | %{status} | Indica o código de status HTTP para a resposta. | 200 |
| Endereço IP (solicitante) | %{virt_dst_addr} | Indica o endereço IP do solicitante. | 192.168.1.1 |
| Latitude (solicitante) | %{geo_latitude} | Indica a latitude do solicitante. | 34, 995 |
| Longitude (solicitante) | %{geo_longitude} | Indica a longitude do solicitante. | -118,4143 |
| Área estatística Metropolitana (solicitante) | %{geo_metro_code} | Indica a área metropolitana do solicitante. <br /><br />Esse campo só é aplicável a solicitações originadas no Estados Unidos.<br />| 745 |
| Porta (solicitante) | %{virt_dst_port} | Indica a porta efêmera do solicitante. | 55885 |
| CEP (solicitante) | %{geo_postal_code} | Indica o código postal do solicitante. | 90210 |
| Cadeia de caracteres de consulta encontrada | %{is_args} | O valor dessa variável varia de acordo com se a solicitação contém uma cadeia de caracteres de consulta.<br /><br />-Cadeia de caracteres de consulta encontrada:?<br />-Sem cadeia de consulta: NULL | ? |
| Parâmetro de cadeia de caracteres de consulta encontrado | %{is_amp} | O valor dessa variável varia de acordo com o fato de a solicitação conter pelo menos um parâmetro de cadeia de caracteres de consulta.<br /><br />-Parâmetro encontrado: &<br />-Sem parâmetros: NULL | & |
| Valor do parâmetro da cadeia de caracteres de consulta | % {parâmetro&lt;&gt;arg_} | Retorna o valor correspondente ao parâmetro de cadeia de caracteres de consulta &lt;identificado&gt; pelo termo de parâmetro. | Exemplo de uso: <br />%{arg_language}<br /><br />Parâmetro de cadeia de consulta de exemplo: <br />?language=en<br /><br />Valor de exemplo: en |
| Valor da cadeia de caracteres de consulta | %{query_string} | Indica o valor da cadeia de caracteres de consulta inteiro definido na URL da solicitação. |key1=val1&key2=val2&key3=val3 |
| Domínio referenciador | %{referring_domain} | Indica o domínio definido no cabeçalho de solicitação de referenciador. | <www.google.com> |
| Região (solicitante) | %{geo_region} | Indica a região do solicitante (por exemplo, estado ou província) por meio de sua abreviação alfanumérica. | AC |
| Valor do cabeçalho da solicitação | %{http_RequestHeader} | Retorna o valor correspondente ao cabeçalho de solicitação identificado pelo termo RequestHeader. <br /><br />Se o nome do cabeçalho da solicitação contiver um traço (por exemplo, agente do usuário), substitua-o por um sublinhado (por exemplo, User_Agent).| Exemplo de uso:% {http_Connection}<br /><br />Valor de exemplo: Keep-Alive | 
| Solicitar host | % {host} | Indica o host definido na URL da solicitação. | <www.mydomain.com> |
| Protocolo de solicitação | %{request_protocol} | Indica o protocolo de solicitação. | HTTP/1.1 |
| Esquema de solicitação | %{scheme} | Indica o esquema de solicitação. |http |
| URI de solicitação (relativo) | %{request_uri} | Indica o caminho relativo, incluindo a cadeia de caracteres de consulta, definida no URI de solicitação. | /marketing/foo.js?loggedin=true |
| URI de solicitação (relativo sem cadeia de caracteres de consulta) | % {URI} | Indica o caminho relativo para o conteúdo solicitado. <br /><br/>Informações da chave:<br />-Esse caminho relativo exclui a cadeia de caracteres de consulta.<br />-Esse caminho relativo reflete as regravações de URL. Uma URL será reescrita sob as seguintes condições:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Recurso de regravação de URL: Esse recurso reescreve o caminho relativo definido no URI de solicitação.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL de CNAME de borda: Esse tipo de solicitação é regravado na URL correspondente da CDN. |/800001/corigin/rewrittendir/foo.js |
| URI de pedido | % {solicitação} | Descreve a solicitação. <br />Sintaxe &lt;Protocolo http&gt; do caminho&gt; &lt; relativodométodo&lt;http&gt; | OBTER/marketing/foo.js? LoggedInTemplate = verdadeiro HTTP/1.1 |
| Valor do cabeçalho de resposta | % {resp_&lt;ResponseHeader&gt;} | Retorna o valor correspondente ao cabeçalho de resposta identificado pelo &lt;termo ResponseHeader.&gt; <br /><br />Se o nome do cabeçalho de resposta contiver um traço (por exemplo, agente de usuário), substitua-o por um sublinhado (por exemplo, User_Agent). | Exemplo de uso:% {resp_Content_Length}<br /><br />Valor de exemplo: 100 |

## <a name="usage"></a>Utilização
A tabela a seguir descreve a sintaxe apropriada para especificar uma variável HTTP.


| Sintaxe | Exemplo | Descrição |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | % {host} | Use esta sintaxe para obter o valor inteiro correspondente ao HTTPVariable &lt;&gt;especificado. |
| %{&lt;HTTPVariableDelimiter&gt;} | % {host,} | Use essa sintaxe para definir o caso do valor inteiro correspondente ao HTTPVariableDelimiter &lt;&gt;especificado. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | % {host/= ^ www\.([^\.] +)\.([^\.:] +)/CDN. $2. $3:80} | Use uma expressão regular para &lt;HTTPVariableDelimiterExpression&gt; substituir, excluir ou manipular o valor de uma variável http. |

Os nomes de variáveis HTTP dão suporte apenas a caracteres alfabéticos e sublinhados. Converter caracteres sem suporte em sublinhados.

## <a name="delimiter-reference"></a>Referência de delimitador
Um delimitador pode ser especificado após uma variável HTTP para obter qualquer um dos seguintes efeitos:

- Transforme o valor associado à variável.

     Exemplo: Converta todo o valor em minúsculas.

- Exclua o valor associado à variável.

- Manipule o valor associado à variável.

     Exemplo: Use expressões regulares para alterar o valor associado à variável HTTP.

Os delimitadores são descritos na tabela a seguir.

| Delimitador | Descrição |
| --------- | ----------- |
| := | Indica que um valor padrão será atribuído à variável quando for: <br />-Ausente <br />-Definido como nulo. |
| :+ | Indica que um valor padrão será atribuído à variável quando um valor tiver sido atribuído a ele. |
| : | Indica que uma subcadeia de caracteres do valor atribuído à variável será expandida. |
| # | Indica que o padrão especificado após esse delimitador deve ser excluído quando for encontrado no início do valor associado à variável. |
| % | Indica que o padrão especificado após esse delimitador deve ser excluído quando for encontrado no final do valor associado à variável. <br />Essa definição só é aplicável quando o símbolo% é usado como um delimitador. |
| / | Delimita uma variável HTTP ou um padrão. |
| // | Localizar e substituir todas as instâncias do padrão especificado. |
| /= | Localizar, copiar e reescrever todas as ocorrências do padrão especificado. |
| , | Converta o valor associado à variável HTTP em minúsculas. |
| ^ | Converta o valor associado à variável HTTP em maiúsculas. |
| ,, | Converta todas as instâncias do caractere especificado no valor associado à variável HTTP para minúsculas. |
| ^^ | Converta todas as instâncias do caractere especificado no valor associado à variável HTTP para letras maiúsculas. |

## <a name="exceptions"></a>Exceções
A tabela a seguir descreve as circunstâncias sob as quais o texto especificado não é tratado como uma variável HTTP.

| Condição | Descrição | Exemplo |
| --------- | ----------- | --------|
| Símbolo de escape% | O símbolo de porcentagem pode ser ignorado com o uso de uma barra invertida. <br />O valor de exemplo à direita será tratado como um valor literal e não como uma variável HTTP.| \%hospedeira |
| Variáveis desconhecidas | Uma cadeia de caracteres vazia sempre é retornada para variáveis desconhecidas. | %{unknown_variable} |
| Caracteres ou sintaxe inválidos | As variáveis que contêm caracteres ou sintaxe inválidas são tratadas como valores literais. <br /><br />Exemplo #1: O valor especificado contém um caractere inválido (por exemplo,-). <br /><br />Exemplo #2: O valor especificado contém um conjunto duplo de chaves. <br /><br />Exemplo #3: O valor especificado não tem uma chave de fechamento.<br /> | Exemplo #1:% {resp_user-Agent} <br /><br />Exemplo #2:% {{host}} <br /><br />Exemplo #3:% {host |
| Nome de variável ausente | Um valor nulo sempre é retornado quando uma variável não é especificada. | %{} |
| Carateres à direita | Os caracteres que uma variável de trilha são tratados como valores literais. <br />O valor de exemplo à direita contém uma chave à esquerda que será tratada como um valor literal. | % {host}} |

## <a name="setting-default-header-values"></a>Definindo valores de cabeçalho padrão
Um valor padrão pode ser atribuído a um cabeçalho quando ele atende a qualquer uma das seguintes condições:
- Ausente/desdefinido
- Defina como nulo.

A tabela a seguir descreve como definir um valor padrão.

| Condição | Sintaxe | Exemplo | Descrição |
| --------- | ------ | --------| ----------- |
| Defina um cabeçalho como um valor padrão quando ele atender a qualquer uma das seguintes condições: <br /><br />-Cabeçalho ausente <br /><br />-O valor do cabeçalho está definido como nulo.| % {Variável: = valor} | %{http_referrer:=unspecified} | O cabeçalho referenciador só será definido como não *especificado* quando estiver ausente ou definido como nulo. Nenhuma ação ocorrerá se tiver sido definida. |
| Defina um cabeçalho como um valor padrão quando ele estiver ausente. | %{Variable=Value} | %{http_referrer=unspecified} | O cabeçalho referenciador só será definido como não *especificado* quando estiver ausente. Nenhuma ação ocorrerá se tiver sido definida. |
| Defina o cabeçalho como um valor padrão quando ele não atender a nenhuma das seguintes condições: <br /><br />-Ausente<br /><br /> -Definido como nulo. | % {Variável: + valor} | %{http_referrer:+unspecified} | O cabeçalho referenciador só será definido como *não especificado* quando um valor tiver sido atribuído a ele. Nenhuma ação ocorrerá se ela estiver ausente ou definida como nula. |

## <a name="manipulating-variables"></a>Manipulando variáveis
As variáveis podem ser manipuladas das seguintes maneiras:

- Expandindo subcadeias de caracteres
- Removendo padrões

### <a name="substring-expansion"></a>Expansão de subcadeia de caracteres
Por padrão, uma variável será expandida para seu valor completo. Use a sintaxe a seguir para expandir apenas uma subcadeia de caracteres do valor da variável.

`%<Variable>:<Offset>:<Length>}`

Informações da chave:

- O valor atribuído ao termo de deslocamento determina o caractere inicial da subcadeia de caracteres:

     - Negativo O caractere inicial da subcadeia de caracteres é calculado a partir do primeiro caractere na cadeia de caracteres.
     - Zero O caractere inicial da subcadeia de caracteres é o primeiro caractere na cadeia de caracteres.
     - Seriamente O caractere inicial da subcadeia de caracteres é calculado a partir do último caractere na cadeia de caracteres.

- O comprimento da subcadeia de caracteres é determinado pelo *período de duração* :

     - Exibido Omitir o comprimento prazo permite que a subcadeia de caracteres inclua todos os personagens entre o caractere inicial e o final da cadeia de caracteres.
     - Negativo Determina o comprimento da subcadeia de caracteres a partir do caractere inicial à direita.
     - Seriamente Determina o comprimento da subcadeia de caracteres a partir do caractere inicial à esquerda.

#### <a name="example"></a>Exemplo:

O exemplo a seguir conta com a seguinte URL de solicitação de exemplo:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A cadeia de caracteres a seguir demonstra vários métodos para manipular variáveis:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Com base na URL de solicitação de exemplo, a manipulação de variável acima produzirá o seguinte valor:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Remoção de padrão
O texto que corresponde a um padrão específico pode ser removido do início ou do fim do valor de uma variável.

| Sintaxe | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove o texto quando o padrão especificado é encontrado no início do valor de uma variável. |
| %{Variable%Pattern} | Remove o texto quando o padrão especificado é encontrado no final do valor de uma variável. |

#### <a name="example"></a>Exemplo:

Neste cenário de exemplo, a variável *REQUEST_URI* é definida como:

`/800001/myorigin/marketing/product.html?language=en-US`

A tabela a seguir demonstra como essa sintaxe funciona.

| Sintaxe de exemplo | Resultados | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Como a variável começa com o padrão, ela foi substituída. |
| %{request_uri%html}htm | /800001/myorigin/marketing/Product.html? language = en-US | Como a variável não termina com o padrão, não havia nenhuma alteração.|

### <a name="find-and-replace"></a>Localizar e substituir
A sintaxe localizar e substituir é descrita na tabela a seguir.

| Sintaxe | Action |
| ------ | ------ |
| % {Variável/localizar/substituir} | Localizar e substituir a primeira ocorrência do padrão especificado. |
| % {Variável//localizar/substituir} | Localizar e substituir todas as ocorrências do padrão especificado. |
| %{Variable^} |Converta o valor inteiro em maiúsculas. |
| %{Variable^Find} | Converta a primeira ocorrência do padrão especificado em maiúsculas. |
| % {Variável,} | Converta todo o valor em minúsculas. |
| % {Variável, localizar} | Converta a primeira ocorrência do padrão especificado em minúsculas. |

### <a name="find-and-rewrite"></a>Localizar e reescrever
Para uma variação de localizar e substituir, use o texto que corresponde ao padrão especificado ao regravá-lo. A sintaxe de localizar e reescrever é descrita na tabela a seguir.

| Sintaxe | Action |
| ------ | ------ |
| % {Variable/= localizar/reescrever} | Localizar, copiar e reescrever todas as ocorrências do padrão especificado. |
| % {Variável/^ localizar/reescrever} | Localize, copie e reescreva o padrão especificado quando ele ocorrer no início da variável. |
| %{Variable/$Find/Rewrite} | Localize, copie e reescreva o padrão especificado quando ele ocorrer no final da variável. |
| % {Variável/localizar} | Localizar e excluir todas as ocorrências do padrão especificado. |

Informações da chave:

- Expanda o texto que corresponde ao padrão especificado especificando um cifrão seguido por um inteiro (por exemplo, $1).

- Vários padrões podem ser especificados. A ordem na qual o padrão é especificado determina o número inteiro que será atribuído a ele. No exemplo a seguir, o primeiro padrão corresponde a "www.," o segundo padrão corresponde ao domínio de segundo nível e o terceiro padrão corresponde ao domínio de nível superior:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- O valor reescrito pode consistir em qualquer combinação de texto e esses espaços reservados.

    No exemplo anterior, o nome do host é reescrito `cdn.$2.$3:80` para (por exemplo, CDN.mydomain.com:80).

- O caso de um espaço reservado de padrão (por exemplo, $1) pode ser modificado por meio dos seguintes sinalizadores:
     - U: Maiúsculas o valor expandido.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - DEBUG Minúsculas o valor expandido.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Um operador deve ser especificado antes do padrão. O operador especificado determina o comportamento de captura de padrões:

     - `=`: Indica que todas as ocorrências do padrão especificado devem ser capturadas e reescritas.
     - `^`: Indica que apenas o texto que começa com o padrão especificado será capturado.
     - `$`: Indica que somente o texto que termina com o padrão especificado será capturado.
 
- Se você omitir o valor */Rewrite* , o texto que corresponde ao padrão será excluído.


