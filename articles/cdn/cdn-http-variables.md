---
title: Variáveis HTTP para o motor de regras de CDN do Azure | Documentos da Microsoft
description: Variáveis HTTP permitem-lhe obter os metadados de solicitação e resposta HTTP.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593823"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variáveis HTTP para o motor de regras de CDN do Azure
As variáveis HTTP fornecem os meios através dos quais pode recuperar os metadados de solicitação e resposta HTTP. Estes metadados, em seguida, podem ser utilizado para alterar dinamicamente uma solicitação ou uma resposta. A utilização de variáveis HTTP é restrita para as seguintes funcionalidades do motor de regras:

- [Reescrita de chave de cache](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modificar o cabeçalho de pedido do cliente](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modificar o cabeçalho de resposta do cliente](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Redirecionamento de URL](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [Reescrever URL](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definições
A tabela seguinte descreve as variáveis HTTP suportadas. Um valor em branco é retornado quando metadados de Georreplicação (por exemplo, código postal) não estão disponível para uma determinada solicitação.


| Nome | Variável | Descrição | Valor da amostra |
| ---- | -------- | ----------- | ------------ |
| ASN (autor do pedido) | %{geo_asnum} | Indica o autor do pedido como número. <br /><br />**Preteridos:** % {virt_dst_asnum}. <br />Essa variável foi preterida em favor do % {geo_asnum}. Embora uma regra que utiliza esta variável preterido continuarão a funcionar, atualize-o para utilizar a nova variável. | AS15133 |
| Cidade (autor do pedido) | %{geo_city} | Indica a cidade do autor do pedido. | Los Angeles |
| Continente (autor do pedido) | %{geo_continent} | Indica o continente do autor do pedido através da respetiva abreviatura. <br />Valores válidos são: <br />AF: África<br />AS: Ásia<br />EU: Europa<br />NA: América do Norte<br />OC: Oceânia<br />SA: América do Sul<br /><br />**Preteridos:** % {virt_dst_continent}. <br />Essa variável foi preterida em favor do % {geo_continent}. <br />Embora uma regra que utiliza esta variável preterido continuarão a funcionar, atualize-o para utilizar a nova variável.| N/A |
| Valor do cookie | %{cookie_Cookie} | Devolve o valor correspondente para a chave de cookie identificada pelo termo de Cookie. | Utilização de exemplo: <br />%{cookie__utma}<br /><br />Valor de exemplo:<br />111662281.2.10.1222100123 |
| País (autor do pedido) | %{geo_country} | Indica o país do autor do pedido de origem por meio de seu código de país. <br />**Preteridos:** % {virt_dst_country}. <br /><br />Essa variável foi preterida em favor do % {geo_country}. Embora uma regra que utiliza esta variável preterido continuarão a funcionar, atualize-o para utilizar a nova variável. | EUA |
| Área de mercado designado (autor do pedido) | %{geo_dma_code} |Indica o mercado de mídia do autor do pedido pelo seu código de região. <br /><br />Este campo só é aplicável para pedidos provenientes dos Estados Unidos.| 745 |
| Método de pedido de HTTP | %{request_method} | Indica o método de pedido HTTP. | GET |
| Código de estado de HTTP | %{status} | Indica o código de estado HTTP para a resposta. | 200 |
| Endereço IP (autor do pedido) | %{virt_dst_addr} | Indica o endereço IP do autor do pedido. | 192.168.1.1 |
| Latitude (autor do pedido) | %{geo_latitude} | Indica a latitude do autor do pedido. | 34.0995 |
| Longitude (autor do pedido) | %{geo_longitude} | Indica a longitude do autor do pedido. | -118.4143 |
| Área metropolitana de estatística (autor do pedido) | %{geo_metro_code} | Indica a área metropolitana do autor do pedido. <br /><br />Este campo só é aplicável para pedidos provenientes dos Estados Unidos.<br />| 745 |
| Porta (autor do pedido) | %{virt_dst_port} | Indica as portas efémero do autor do pedido. | 55885 |
| Código postal (autor do pedido) | %{geo_postal_code} | Indica o CEP do autor do pedido. | 90210 |
| Cadeia de consulta encontrada | %{is_args} | O valor desta variável varia de acordo com se o pedido contém uma cadeia de consulta.<br /><br />-Foram encontrada de cadeia de consulta:?<br />-Sem cadeia de consulta: NULL | ? |
| Foi encontrado um parâmetro de cadeia de consulta | %{is_amp} | O valor desta variável varia de acordo com se o pedido contém, pelo menos, um parâmetro de cadeia de caracteres de consulta.<br /><br />-Parâmetro encontrado: &<br />-Sem parâmetros: NULL | & |
| Valor do parâmetro de cadeia de caracteres de consulta | %{arg_&lt;parameter&gt;} | Devolve o valor correspondente para o parâmetro de cadeia de caracteres de consulta identificado pelos &lt;parâmetro&gt; termo. | Utilização de exemplo: <br />%{arg_language}<br /><br />Parâmetro de cadeia de caracteres de consulta de exemplo: <br />?language=en<br /><br />Valor de exemplo: en |
| Valor de cadeia de caracteres de consulta | %{query_string} | Indica o valor de cadeia de caracteres de consulta inteira definido no URL do pedido. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indica o domínio definido no cabeçalho de pedido de referência. | <www.google.com> |
| Região (autor do pedido) | %{geo_region} | Indica a região do autor do pedido (por exemplo, estado ou província) por meio de sua abreviação de alfanumérica. | CA |
| Valor de cabeçalho do pedido | %{http_RequestHeader} | Devolve o valor correspondente para o cabeçalho do pedido identificado pelo termo RequestHeader. <br /><br />Se o nome do cabeçalho do pedido contém um travessão (por exemplo, agente do usuário), substitua-o com um caráter de sublinhado (por exemplo, User_Agent).| Utilização de exemplo: % {http_Connection}<br /><br />Valor de exemplo: Keep-Alive | 
| Anfitrião do pedido | %{host} | Indica o anfitrião definido no URL do pedido. | <www.mydomain.com> |
| Protocolo de pedido | %{request_protocol} | Indica o protocolo de pedido. | HTTP/1.1 |
| Esquema de pedido | %{scheme} | Indica o esquema de pedido. |http |
| Pedido de URI (relativo) | %{request_uri} | Indica o caminho relativo, incluindo a cadeia de consulta, definida no URI do pedido. | /marketing/foo.js?loggedin=true |
| Pedido de URI (relativo sem cadeia de consulta) | %{uri} | Indica o caminho relativo para o conteúdo solicitado. <br /><br/>Informações da chave:<br />-Este caminho relativo exclui a cadeia de consulta.<br />-Este caminho relativo reflete reescritas da URL. Um URL vai ser reescrito nas seguintes condições:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Recurso de reconfiguração URL: Esta funcionalidade reescreve o caminho relativo, definido no URI do pedido.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL de CNAME edge: Este tipo de pedido é reescrito para o URL de CDN correspondente. |/800001/corigin/rewrittendir/foo.js |
| URI do pedido | % {pedido} | Descreve o pedido. <br />Sintaxe: &lt;Método HTTP&gt; &lt;caminho relativo&gt; &lt;protocolo HTTP&gt; | OBTER /marketing/foo.js?loggedin=true HTTP/1.1. |
| Valor de cabeçalho de resposta | %{resp_&lt;ResponseHeader&gt;} | Devolve o valor correspondente para o cabeçalho de resposta identificado pelos &lt;ResponseHeader&gt; termo. <br /><br />Se o nome do cabeçalho de resposta contém um travessão (por exemplo, agente do usuário), substitua-o com um caráter de sublinhado (por exemplo, User_Agent). | Utilização de exemplo: % {resp_Content_Length}<br /><br />Valor de exemplo: 100 |

## <a name="usage"></a>Utilização
A tabela seguinte descreve a sintaxe correta para especificar uma variável de HTTP.


| Sintaxe | Exemplo | Descrição |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Utilizar esta sintaxe para obter o valor inteiro correspondente a especificado &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Utilize esta sintaxe para definir o caso para o valor inteiro correspondente a especificado &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | Usar uma expressão regular para &lt;HTTPVariableDelimiterExpression&gt; para substituir, eliminar ou manipular o valor da variável de HTTP. |

Os nomes de variáveis de HTTP só suportam carateres alfabéticos e carateres de sublinhado. Converta carateres não suportados para carateres de sublinhado.

## <a name="delimiter-reference"></a>Referência do delimitador
Um delimitador pode ser especificado após a uma variável de HTTP para executar um dos seguintes efeitos:

- Transforme o valor associado a variável.

     Exemplo: Converta o valor de todo em minúsculas.

- Elimine o valor associado a variável.

- Manipular o valor associado a variável.

     Exemplo: Utilize expressões regulares para alterar o valor associado a variável HTTP.

Os delimitadores são descritos na tabela seguinte.

| Delimitador | Descrição |
| --------- | ----------- |
| := | Indica que um valor predefinido será atribuído à variável quando está: <br />-Em falta <br />-Definido como nulo. |
| :+ | Indica que um valor predefinido será atribuído à variável, quando lhe foi atribuído um valor. |
| : | Indica que uma subcadeia do valor atribuído à variável será expandida. |
| # | Indica que o padrão especificado depois deste delimitador deve ser eliminado quando for encontrado no início do valor associado com a variável. |
| % | Indica que o padrão especificado depois deste delimitador deve ser eliminado quando for encontrado no final do valor associado com a variável. <br />Esta definição só é aplicável quando o símbolo de % é utilizado como um delimitador. |
| / | Delimits uma variável de HTTP ou um padrão. |
| // | Localizar e substituir todas as instâncias do padrão especificado. |
| /= | Localizar, copiar e reescrever todas as ocorrências do padrão especificado. |
| , | Converta o valor associado a variável HTTP em minúsculas. |
| ^ | Converta o valor associado a variável HTTP em maiúsculas. |
| ,, | Converta todas as instâncias do caractere especificado no valor associado com a variável HTTP em minúsculas. |
| ^^ | Converta todas as instâncias do caractere especificado no valor associado com a variável HTTP em maiúsculas. |

## <a name="exceptions"></a>Exceções
A tabela seguinte descreve as circunstâncias em que o texto especificado não é tratado como uma variável de HTTP.

| Condição | Descrição | Exemplo |
| --------- | ----------- | --------|
| Símbolo de % de carateres de escape | O símbolo de percentagem pode ser ignorado com o uso de uma barra invertida. <br />O valor de exemplo para a direita será tratado como um valor literal e não como uma variável de HTTP.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Variáveis desconhecidas | Uma cadeia vazia é sempre retornada para variáveis desconhecidas. | %{unknown_variable} |
| Sintaxe ou carateres inválidos | Variáveis que contêm sintaxe ou carateres inválidos são tratadas como valores literais. <br /><br />Exemplo #1: O valor especificado contém um caráter inválido (por exemplo,-). <br /><br />Exemplo #2: O valor especificado contém um conjunto duplo de chaves. <br /><br />Exemplo #3: O valor especificado está em falta uma chave de fechamento.<br /> | Exemplo #1: % {resp_user-agente} <br /><br />Exemplo #2: % {{anfitrião}} <br /><br />Exemplo #3: % {anfitrião |
| Nome da variável em falta | Um valor nulo sempre é retornado quando uma variável não for especificada. | %{} |
| Carateres à direita | Carateres que o início de uma variável são tratadas como valores literais. <br />O valor de exemplo para a direita contém uma chave à direita que será tratada como um valor literal. | %{host}} |

## <a name="setting-default-header-values"></a>Configurando valores de cabeçalho do padrão
Um valor padrão pode ser atribuído a um cabeçalho quando ele cumpre qualquer uma das seguintes condições:
- Em falta/não definida
- Definido como nulo.

A tabela seguinte descreve como definir um valor predefinido.

| Condição | Sintaxe | Exemplo | Descrição |
| --------- | ------ | --------| ----------- |
| Defina um cabeçalho para um valor padrão quando ele cumpre qualquer uma das seguintes condições: <br /><br />-Cabeçalho em falta <br /><br />-Valor de cabeçalho é definido como nulo.| %{Variable:=Value} | %{http_referrer:=unspecified} | O cabeçalho do referenciador apenas será definido como *não especificado* quando está em falta ou é definida como NULL. Nenhuma ação ocorrerá se foi definida. |
| Defina um cabeçalho para um valor padrão quando ele está em falta. | %{Variable=Value} | %{http_referrer=unspecified} | O cabeçalho do referenciador apenas será definido como *não especificado* quando está em falta. Nenhuma ação ocorrerá se foi definida. |
| Defina o cabeçalho para um valor predefinido quando não cumpre qualquer uma das seguintes condições: <br /><br />-Em falta<br /><br /> -Definido como nulo. | % {Variável: + valor} | %{http_referrer:+unspecified} | O cabeçalho do referenciador apenas será definido como *não especificado* quando foi atribuído um valor a ela. Nenhuma ação ocorrerá se estiver em falta ou é definida como NULL. |

## <a name="manipulating-variables"></a>Manipulação de variáveis
As variáveis podem ser manipuladas das seguintes formas:

- Expandir subcadeias de carateres
- Remoção de padrões

### <a name="substring-expansion"></a>Expansão de subcadeia
Por predefinição, uma variável será expandido para o valor total. Utilize a seguinte sintaxe para expandir apenas uma subseqüência de valor da variável.

`%<Variable>:<Offset>:<Length>}`

Informações da chave:

- O valor atribuído para o termo de deslocamento determina o caráter de partida da subseqüência:

     - Positiva: O caráter de partida da subcadeia é calculado a partir do primeiro caractere na cadeia de caracteres.
     - Zero: O caráter de partida da subcadeia é o primeiro caráter na cadeia de caracteres.
     - Negativo: O caráter de partida da subcadeia é calculado a partir do último caráter na cadeia de caracteres.

- O comprimento da subcadeia é determinado através da *comprimento* termo:

     - Omitido: Omitir o termo de comprimento, permite que a subcadeia incluir todos os caracteres entre o caráter de partida e o fim da cadeia de caracteres.
     - Positiva: Determina o comprimento da subseqüência do caráter de partida para a direita.
     - Negativo: Determina o comprimento da subseqüência do caráter de partida para a esquerda.

#### <a name="example"></a>Exemplo:

O exemplo seguinte baseia-se no URL do pedido de exemplo seguinte:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A seguinte cadeia demonstra vários métodos para manipulação de variáveis:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Com base no URL do pedido de exemplo, a manipulação de variável acima irá produzir o seguinte valor:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Remoção do padrão
Texto que corresponde a um padrão específico pode ser removido do início ou fim de valor de uma variável.

| Sintaxe | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remova o texto quando o padrão especificado é encontrado no início do valor de uma variável. |
| %{Variable%Pattern} | Remova o texto quando o padrão especificado encontra-se no final do valor de uma variável. |

#### <a name="example"></a>Exemplo:

Neste cenário de exemplo, o *request_uri* variável é definida como:

`/800001/myorigin/marketing/product.html?language=en-US`

A tabela a seguir demonstra como funciona esta sintaxe.

| Sintaxe de exemplo | Resultados | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Uma vez que a variável é iniciado com o padrão, foi substituído. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Uma vez que a variável não termina com o padrão, não havia nenhuma alteração.|

### <a name="find-and-replace"></a>Localizar e substituir
A sintaxe de localizar e substituir é descrita na tabela seguinte.

| Sintaxe | Action |
| ------ | ------ |
| %{Variable/Find/replace} | Localizar e substituir a primeira ocorrência do padrão especificado. |
| %{Variable//Find/replace} | Localizar e substituir todas as ocorrências do padrão especificado. |
| %{Variable^} |Converta o valor inteiro em maiúsculas. |
| %{Variable^Find} | Converta a primeira ocorrência do padrão especificado em maiúsculas. |
| % {Variável} | Converta o valor de todo em minúsculas. |
| % {Variável, encontre} | Converta a primeira ocorrência do padrão especificado em minúsculas. |

### <a name="find-and-rewrite"></a>Localizar e reescrever
Para uma variação de localizar e substituir, utilize o texto que corresponda ao padrão especificado quando reescrevê-los. A sintaxe de localizar e reescrever é descrita na tabela seguinte.

| Sintaxe | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Localizar, copiar e reescrever todas as ocorrências do padrão especificado. |
| %{Variable/^Find/Rewrite} | Localizar, copiar e reescrever o padrão especificado, quando ele ocorre no início da variável. |
| %{Variable/$Find/Rewrite} | Localizar, copiar e reescrever o padrão especificado quando ocorrer no final da variável. |
| %{Variable/Find} | Localize e elimine todas as ocorrências do padrão especificado. |

Informações da chave:

- Expanda o texto que corresponda ao padrão especificado, especificando um cifrão seguido por um inteiro de inteiro (por exemplo, US $1).

- Podem ser especificados vários padrões. A ordem em que é especificado o padrão determina o número inteiro que será atribuído a ele. No exemplo seguinte, as correspondências de padrão primeiro "www,". o segundo padrão corresponde ao domínio de segundo nível e o padrão de terceiro corresponde ao domínio de nível superior:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- O valor reescrito pode incluir qualquer combinação de texto e estes marcadores de posição.

    No exemplo anterior, o nome do anfitrião é reescrito para `cdn.$2.$3:80` (por exemplo, cdn.mydomain.com:80).

- O caso de um marcador de posição do padrão (por exemplo, US $1) pode ser modificado por meio dos seguintes sinalizadores:
     - U: O valor expandido letras em maiúscula.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Em minúsculas do valor expandido.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Um operador deve ser especificado antes do padrão. O operador especificado determina o comportamento de captura o padrão:

     - `=`: Indica que todas as ocorrências do padrão especificado tem de ser capturadas e reescritas.
     - `^`: Indica que apenas o texto que começa com o padrão especificado será capturado.
     - `$`: Indica que apenas o texto que termina com o padrão especificado será captura.
 
- Se omitir o */reescrever* valor, o texto que corresponda ao padrão é eliminado.


