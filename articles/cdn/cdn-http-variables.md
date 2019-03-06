---
title: Variáveis HTTP para o motor de regras de CDN do Azure | Documentos da Microsoft
description: Variáveis HTTP permitem-lhe obter os metadados de solicitação e resposta HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 70154a50086fabc84671e7e540d8496490ab5b2d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436634"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variáveis HTTP para o motor de regras de CDN do Azure
As variáveis HTTP fornecem os meios através dos quais pode recuperar os metadados de solicitação e resposta HTTP. Estes metadados, em seguida, podem ser utilizado para alterar dinamicamente uma solicitação ou uma resposta. A utilização de variáveis HTTP é restrita para as seguintes funcionalidades do motor de regras:

- [Reescrita de chave de cache](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Modificar o cabeçalho de pedido do cliente](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Modificar o cabeçalho de resposta do cliente](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Redirecionamento de URL](cdn-rules-engine-reference-features.md#url-redirect)
- [Reescrever URL](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definições
A tabela seguinte descreve as variáveis HTTP suportadas. Um valor em branco é retornado quando metadados de Georreplicação (por exemplo, código postal) não estão disponível para uma determinada solicitação.


| Name | Variável | Descrição | Valor da amostra |
| ---- | -------- | ----------- | ------------ |
| ASN (autor do pedido) | %{geo_asnum} | Indica o autor do pedido como número. <br /><br />**Preteridos:** % {virt_dst_asnum}. <br />Essa variável foi preterida em favor do % {geo_asnum}. Embora uma regra que utiliza esta variável preterido continuarão a funcionar, atualize-o para utilizar a nova variável. | AS15133 |
| Cidade (autor do pedido) | %{geo_city} | Indica a cidade do autor do pedido. | Los Angeles |
| Continente (autor do pedido) | %{geo_continent} | Indica o continente do autor do pedido através da respetiva abreviatura. <br />Valores válidos são: <br />AF: África<br />AS: Ásia<br />EU: Europa<br />NA: América do Norte<br />OC: Oceânia<br />SA: América do Sul<br /><br />**Preteridos:** % {virt_dst_continent}. <ber />Essa variável foi preterida em favor do % {geo_continent}. <br />Embora uma regra que utiliza esta variável preterido continuarão a funcionar, atualize-o para utilizar a nova variável.| N/A |
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
| Referrer Domain | %{referring_domain} | Indica o domínio definido no cabeçalho de pedido de referência. | www.google.com |
| Região (autor do pedido) | %{geo_region} | Indica a região do autor do pedido (por exemplo, estado ou província) por meio de sua abreviação de alfanumérica. | CA |
| Valor de cabeçalho do pedido | %{http_RequestHeader} | Devolve o valor correspondente para o cabeçalho do pedido identificado pelo termo RequestHeader. <br /><br />Se o nome do cabeçalho do pedido contém um travessão (por exemplo, agente do usuário), substitua-o com um caráter de sublinhado (por exemplo, User_Agent).| Utilização de exemplo: % {http_Connection}<br /><br />Valor de exemplo: Keep-Alive | 
| Anfitrião do pedido | %{host} | Indica o anfitrião definido no URL do pedido. | www.mydomain.com |
| Protocolo de pedido | %{request_protocol} | Indica o protocolo de pedido. | HTTP/1.1 |
| Esquema de pedido | %{scheme} | Indica o esquema de pedido. |http |
| Pedido de URI (relativo) | %{request_uri} | Indica o caminho relativo, incluindo a cadeia de consulta, definida no URI do pedido. | /marketing/foo.js?loggedin=true |
| Pedido de URI (relativo sem cadeia de consulta) | %{uri} | Indica o caminho relativo para o conteúdo solicitado. <br /><br/>Informações da chave:<br />-Este caminho relativo exclui a cadeia de consulta.<br />-Este caminho relativo reflete reescritas da URL. Um URL vai ser reescrito nas seguintes condições:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Recurso de reconfiguração URL: Esta funcionalidade reescreve o caminho relativo, definido no URI do pedido.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL de CNAME edge: Este tipo de pedido é reescrito para o URL de CDN correspondente. |/800001/corigin/rewrittendir/foo.js |
| URI de pedido | % {pedido} | Descreve o pedido. <br />Sintaxe: &lt;Método HTTP&gt; &lt;caminho relativo&gt; &lt;protocolo HTTP&gt; | OBTER /marketing/foo.js?loggedin=true HTTP/1.1. |
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
| ,  | Converta o valor associado a variável HTTP em minúsculas. |
| ^ | Converta o valor associado a variável HTTP em maiúsculas. |
| ,, | Converta todas as instâncias do caractere especificado no valor associado com a variável HTTP em minúsculas. |
| ^^ | Converta todas as instâncias do caractere especificado no valor associado com a variável HTTP em maiúsculas. |

## <a name="exceptions"></a>Exceções
A tabela seguinte descreve as circunstâncias em que o texto especificado não é tratado como uma variável de HTTP.

| Condição | Descrição | Exemplo |
| --------- | ----------- | --------|
| Símbolo de % de carateres de escape | O símbolo de percentagem pode ser ignorado com o uso de uma barra invertida. <br />O valor de exemplo para a direita será tratado como um valor literal e não como uma variável de HTTP.| \%{host} |
| Variáveis desconhecidas | Uma cadeia vazia é sempre retornada para variáveis desconhecidas. | % {unknownvariable} |
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
| Defina um cabeçalho para um valor padrão quando ele cumpre qualquer uma das seguintes condições: <br /><br />-Cabeçalho em falta <br /><br />-Valor de cabeçalho é definido como nulo.| %{Variable:=Value} | %{http_referer:=unspecified} | O cabeçalho do referenciador apenas será definido como *não especificado* quando está em falta ou é definida como NULL. Nenhuma ação ocorrerá se foi definida. |
| Defina um cabeçalho para um valor padrão quando ele está em falta. | %{Variable=Value} | %{http_referer=unspecified} | O cabeçalho do referenciador apenas será definido como *não especificado* quando está em falta. Nenhuma ação ocorrerá se foi definida. |
| Defina o cabeçalho para um valor predefinido quando não cumpre qualquer uma das seguintes condições: <br /><br />-Em falta<br /><br /> -Definido como nulo. | % {Variável: + valor} | %{http_referer:+unspecified} | O cabeçalho do referenciador apenas será definido como *não especificado* quando foi atribuído um valor a ela. Nenhuma ação ocorrerá se estiver em falta ou é definida como NULL. |

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

| Sintaxe | Ação |
| ------ | ------ |
| %{Variable#Pattern} | Remova o texto quando o padrão especificado é encontrado no início do valor de uma variável. |
| %{Variable%Pattern} | Remova o texto quando o padrão especificado encontra-se no final do valor de uma variável. |

#### <a name="example"></a>Exemplo:

Neste cenário de exemplo, o *request_uri* variável é definida como:

`/800001/myorigin/marketing/product.html?language=en-US`

A tabela a seguir demonstra como funciona esta sintaxe.

| Sintaxe de exemplo | Resultados |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Uma vez que a variável é iniciado com o padrão, foi substituído. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Uma vez que a variável não termina com o padrão, não havia nenhuma alteração.|

### <a name="find-and-replace"></a>Localizar e substituir
A sintaxe de localizar e substituir é descrita na tabela seguinte.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variable/Find/replace} | Localizar e substituir a primeira ocorrência do padrão especificado. |
| %{Variable//Find/replace} | Localizar e substituir todas as ocorrências do padrão especificado. |
| %{Variable^} |Converta o valor inteiro em maiúsculas. |
| %{Variable^Find} | Converta a primeira ocorrência do padrão especificado em maiúsculas. |
| % {Variável} | Converta o valor de todo em minúsculas. |
| % {Variável, encontre} | Converta a primeira ocorrência do padrão especificado em minúsculas. |

### <a name="find-and-rewrite"></a>Localizar e reescrever
Para uma variação de localizar e substituir, utilize o texto que corresponda ao padrão especificado quando reescrevê-los. A sintaxe de localizar e reescrever é descrita na tabela seguinte.

| Sintaxe | Ação |
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


