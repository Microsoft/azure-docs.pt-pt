---
title: VARIÁVEIs HTTP para motor de regras Azure CDN [ Microsoft Docs
description: As variáveis HTTP permitem-lhe recuperar metadados de pedido e resposta http.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: allensu
ms.openlocfilehash: 57a3bab06e4c0a1e4fd8df5d0794a89904a88954
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747641"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>VARIÁVEIs HTTP para motor de regras Azure CDN
As variáveis HTTP fornecem os meios através dos quais pode recuperar metadados de pedido e resposta http. Estes metadados podem então ser utilizados para alterar dinamicamente um pedido ou uma resposta. A utilização de variáveis HTTP limita-se às seguintes características do motor:

- [Reescrever a chave cache](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modificar cabeçalho de pedido de cliente](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modificar cabeçalho de resposta ao cliente](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [Redirecionamento url](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Reescrever](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Definições
A tabela seguinte descreve as variáveis HTTP suportadas. Um valor em branco é devolvido quando os metadados GEO (por exemplo, código postal) não estão disponíveis para um pedido específico.


| Name | Variável | Descrição | Valor da amostra |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indica o número as do solicitador. <br /><br />**Depreciado:** %{virt_dst_asnum}. <br />Esta variável foi depreciada a favor de %{geo_asnum}. Embora uma regra que usa esta variável depreciada continue a funcionar, deve atualizá-la para usar a nova variável. | AS15133 |
| Cidade (Requester) | %{geo_city} | Indica a cidade do solicitado. | Los Angeles |
| Continente (Requester) | %{geo_continent} | Indica o continente do solicitado através da sua abreviatura. <br />Os valores válidos são: <br />AF: África<br />AS: Ásia<br />UE: Europa<br />NA: América do Norte<br />OC: Oceânia<br />SA: América do Sul<br /><br />**Depreciado:** %{virt_dst_continent}. <br />Esta variável foi depreciada a favor de %{geo_continent}. <br />Embora uma regra que usa esta variável depreciada continue a funcionar, deve atualizá-la para usar a nova variável.| N/D |
| Valor cookie | %{cookie_Cookie} | Devolve o valor correspondente à chave de cookies identificada pelo termo Cookie. | Utilização da amostra: <br />%{cookie__utma}<br /><br />Valor da amostra:<br />111662281.2.10.1222100123 |
| País/Região (Requester) | %{geo_country} | Indica o país/região de origem do solicitado através do seu código país/região. <br />**Depreciado:** %{virt_dst_country}. <br /><br />Esta variável foi depreciada a favor de %{geo_country}. Embora uma regra que usa esta variável depreciada continue a funcionar, deve atualizá-la para usar a nova variável. | EUA |
| Área de mercado designada (Requester) | %{geo_dma_code} |Indica o mercado dos meios de comunicação social do solicitado pelo seu código de região. <br /><br />Este campo só se aplica a pedidos originários dos Estados Unidos.| 745 |
| Método de Pedido http | %{request_method} | Indica o método de pedido http. | GET |
| Código de Estado HTTP | %{status} | Indica o código de estado HTTP para a resposta. | 200 |
| Endereço IP (Requester) | %{virt_dst_addr} | Indica o endereço IP do solicitador. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indica a latitude do solicitador. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indica a longitude do solicitado. | -118.4143 |
| Área Estatística Metropolitana (Requester) | %{geo_metro_code} | Indica a área metropolitana do solicitado. <br /><br />Este campo só se aplica a pedidos originários dos Estados Unidos.<br />| 745 |
| Porto (Requester) | %{virt_dst_port} | Indica a porta efémera do solicitado. | 55885 |
| Código Postal (Requester) | %{geo_postal_code} | Indica o código postal do solicitador. | 90210 |
| Cadeia de consulta encontrada | %{is_args} | O valor desta variável varia de acordo com se o pedido contém uma corda de consulta.<br /><br />- Consulta de Corda Encontrada: ?<br />- Sem corda de consulta: NULO | ? |
| Parâmetro de corda de consulta encontrado | %{is_amp} | O valor desta variável varia de acordo com se o pedido contém pelo menos um parâmetro de corda de consulta.<br /><br />- Parâmetro encontrado: &<br />- Sem Parâmetros: NULO | & |
| Valor do parâmetro de corda de consulta | %{arg_ &lt; parâmetro &gt; } | Devolve o valor correspondente ao parâmetro de corda de consulta identificado pelo &lt; &gt; termo parâmetro. | Utilização da amostra: <br />%{arg_language}<br /><br />Parâmetro de corda de consulta de amostra: <br />?language=en<br /><br />Valor da amostra: en |
| Valor de corda de consulta | %{query_string} | Indica todo o valor de cadeia de consulta definido no URL de pedido. |key1=val1&key2=val2&key3=val3 |
| Domínio de remetente | %{referring_domain} | Indica o domínio definido no cabeçalho de pedido do Remetente. | <www.google.com> |
| Região (Requester) | %{geo_region} | Indica a região do solicitado (por exemplo, estado ou província) através da sua abreviatura alfanumérica. | CA |
| Valor do cabeçalho de pedido | %{http_RequestHeader} | Devolve o valor correspondente ao cabeçalho de pedido identificado pelo termo RequestHeader. <br /><br />Se o nome do cabeçalho de pedido contiver um traço (por exemplo, User-Agent), substitua-o por um sublinhado (por exemplo, User_Agent).| Utilização da amostra: %{http_Connection}<br /><br />Valor da amostra: Keep-Alive | 
| Anfitrião de Pedido | %{host} | Indica o anfitrião definido no URL de pedido. | <www.mydomain.com> |
| Protocolo de Pedido | %{request_protocol} | Indica o protocolo de pedido. | HTTP/1.1 |
| Regime de Pedidos | %{esquema} | Indica o esquema de pedido. |http |
| Pedido URI (Relativo) | %{request_uri} | Indica o caminho relativo, incluindo a cadeia de consulta, definido no pedido URI. | /marketing/foo.js?loggedin=true |
| Pedido URI (Parente sem corda de consulta) | %{uri} | Indica o caminho relativo para o conteúdo solicitado. <br /><br/>Informação-chave:<br />- Este caminho relativo exclui a corda de consulta.<br />- Este caminho relativo reflete reescritas de URL. Um URL será reescrito nas seguintes condições:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: Esta função reescreve o caminho relativo definido no pedido URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Edge CNAME: Este tipo de pedido é reescrito para o URL CDN correspondente. |/800001/corigin/rewrittendir/foo.js |
| URI do pedido | %{pedido} | Descreve o pedido. <br />Sintaxe: &lt; método HTTP caminho relativo http &gt; &lt; &gt; &lt; protocolo&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Valor do cabeçalho de resposta | %{resp_ &lt; ResponseHeader &gt; } | Devolve o valor correspondente ao cabeçalho de resposta identificado pelo &lt; termo &gt; ResponseHeader. <br /><br />Se o nome do cabeçalho de resposta contiver um traço (por exemplo, User-Agent), substitua-o por um sublinhado (por exemplo, User_Agent). | Utilização da amostra: %{resp_Content_Length}<br /><br />Valor da amostra: 100 |

## <a name="usage"></a>Utilização
O quadro seguinte descreve a sintaxe adequada para especificar uma variável HTTP.


| Sintaxe | Exemplo | Descrição |
| ------ | -------- | ---------- |
| %{ &lt; HTTPVariável &gt; } | %{host} | Utilize esta sintaxe para obter todo o valor correspondente ao httpvariável especificado &lt; &gt; . |
| %{ &lt; HTTPVariableDelimiter &gt; } | %{host,} | Utilize esta sintaxe para definir o caso para todo o valor correspondente ao &lt; httpvariabledelimiter especificado &gt; . |
| %{ &lt; HTTPVariableDelimiterExpression &gt; } | %{host/=^www \. ([^ \. ]+) \. ([^+])/cdn.$2.$3:80} \. | Utilize uma expressão regular para &lt; HTTPVariableDelimiterExpression para substituir, excluir ou manipular o &gt; valor de uma variável HTTP. |

Os nomes variáveis HTTP apenas suportam caracteres alfabéticos e sublinham. Converter caracteres não apoiados em sublinhados.

## <a name="delimiter-reference"></a>Referência delimitador
Um delimitador pode ser especificado após uma variável HTTP para obter qualquer um dos seguintes efeitos:

- Transforme o valor associado à variável.

     Exemplo: Converter todo o valor em minúsculas.

- Eliminar o valor associado à variável.

- Manipular o valor associado à variável.

     Exemplo: Utilize expressões regulares para alterar o valor associado à variável HTTP.

Os delimitadores são descritos na tabela seguinte.

| Delimitador | Descrição |
| --------- | ----------- |
| := | Indica que um valor predefinido será atribuído à variável quando for: <br />- Desaparecido <br />- Definido para NULO. |
| :+ | Indica que um valor predefinido será atribuído à variável quando um valor tiver sido atribuído à sua. |
| : | Indica que uma subcadeia do valor atribuído à variável será expandida. |
| # | Indica que o padrão especificado após este delimitador deve ser eliminado quando este for encontrado no início do valor associado à variável. |
| % | Indica que o padrão especificado após este delimitador deve ser eliminado quando este for encontrado no final do valor associado à variável. <br />Esta definição só é aplicável quando o símbolo % é utilizado como delimitador. |
| / | Delimita uma variável HTTP ou um padrão. |
| // | Encontre e substitua todas as instâncias do padrão especificado. |
| /= | Encontre, copie e reescreva todas as ocorrências do padrão especificado. |
| , | Converta o valor associado à variável HTTP para minúscula. |
| ^ | Converter o valor associado à variável HTTP para maiúsculas. |
| ,, | Converta todas as instâncias do carácter especificado no valor associado à variável HTTP para minúscula. |
| ^^ | Converter todas as instâncias do carácter especificado no valor associado à variável HTTP para maiúsculas. |

## <a name="exceptions"></a>Exceções
A tabela seguinte descreve as circunstâncias em que o texto especificado não é tratado como uma variável HTTP.

| Condição | Descrição | Exemplo |
| --------- | ----------- | --------|
| Escapar % símbolo | O símbolo percentual pode ser escapado através do uso de um backslash. <br />O valor da amostra à direita será tratado como um valor literal e não como uma variável HTTP.| \%{anfitrião} |
| Variáveis desconhecidas | Uma corda vazia é sempre devolvida para variáveis desconhecidas. | %{unknown_variable} |
| Caracteres inválidos ou sintaxe | As variáveis que contenham caracteres inválidos ou sintaxe são tratadas como valores literais. <br /><br />Exemplo #1: O valor especificado contém um carácter inválido (por exemplo, -). <br /><br />Exemplo #2: O valor especificado contém um conjunto duplo de aparelhos encaracolados. <br /><br />Exemplo #3: O valor especificado está a faltar uma cinta encaracolada de fecho.<br /> | Exemplo #1: %{resp_user-agent} <br /><br />Exemplo #2: %{{host}} <br /><br />Exemplo #3: %{host |
| Nome variável em falta | Um valor NULO é sempre devolvido quando uma variável não é especificada. | %{} |
| Personagens de trailing | Os caracteres que percorrem uma variável são tratados como valores literais. <br />O valor da amostra à direita contém uma cinta encaracolada que será tratada como um valor literal. | %{host}} |

## <a name="setting-default-header-values"></a>Definição de valores de cabeçalho padrão
Um valor predefinido pode ser atribuído a um cabeçalho quando satisfaz qualquer uma das seguintes condições:
- Desaparecido/desset
- Definido para NULO.

A tabela seguinte descreve como definir um valor predefinido.

| Condição | Sintaxe | Exemplo | Descrição |
| --------- | ------ | --------| ----------- |
| Defina um cabeçalho para um valor predefinido quando cumprir qualquer uma das seguintes condições: <br /><br />- Cabeçalho Desaparecido <br /><br />- O valor do cabeçalho está definido para NULO.| %{Variável:=Valor} | %{http_referrer:=não especificado} | O cabeçalho Do Referrer só será definido para *não especificado* quando estiver em falta ou definido para NULO. Não haverá ação se tiver sido definida. |
| Detete um cabeçalho para um valor predefinido quando estiver em falta. | %{Variável=Valor} | %{http_referrer=não especificado} | O cabeçalho de Referrer só será definido para *não especificado* quando estiver desaparecido. Não haverá ação se tiver sido definida. |
| Coloque o cabeçalho num valor predefinido quando não cumprir nenhuma das seguintes condições: <br /><br />- Desaparecido<br /><br /> - Definido para NULO. | %{Variável:+Valor} | %{http_referrer:+não especificado} | O cabeçalho Referrer só será definido para *não especificado* quando lhe for atribuído um valor. Não haverá qualquer ação se faltar ou for definido para NULO. |

## <a name="manipulating-variables"></a>Manipular variáveis
As variáveis podem ser manipuladas das seguintes formas:

- Substrings em expansão
- Remoção de padrões

### <a name="substring-expansion"></a>Expansão substring
Por padrão, uma variável expandir-se-á para o seu valor total. Utilize a seguinte sintaxe para expandir apenas uma subcadeia do valor da variável.

`%<Variable>:<Offset>:<Length>}`

Informação-chave:

- O valor atribuído ao termo Offset determina o carácter inicial da subcadeia:

     - Positivo: O carácter inicial da subcadeia é calculado a partir do primeiro personagem da cadeia.
     - Zero: O personagem inicial da subcadeia é o primeiro personagem da cadeia.
     - Negativo: O carácter inicial da subcadeia é calculado a partir do último personagem da cadeia.

- O comprimento da subcorda é determinado pelo termo *Comprimento:*

     - Omitida: Omitindo o termo Comprimento permite que o substring inclua todos os caracteres entre o personagem inicial e o fim da corda.
     - Positivo: Determina o comprimento do substring do carácter inicial para a direita.
     - Negativo: Determina o comprimento do substring do carácter inicial para a esquerda.

#### <a name="example"></a>Exemplo:

O exemplo seguinte baseia-se no seguinte URL de pedido de amostra:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A seguinte corda demonstra vários métodos para manipular variáveis:

https: \/ /www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Com base no URL de pedido de amostra, a manipulação variável acima referida produzirá o seguinte valor:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Remoção de padrões
O texto que corresponde a um padrão específico pode ser removido do início ou do fim do valor de uma variável.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variável#Padrão} | Remova o texto quando o padrão especificado for encontrado no início do valor de uma variável. |
| %{Variável%Padrão} | Remova o texto quando o padrão especificado for encontrado no final do valor de uma variável. |

#### <a name="example"></a>Exemplo:

Neste cenário de amostragem, a variável *request_uri* é definida para:

`/800001/myorigin/marketing/product.html?language=en-US`

A tabela que se segue demonstra como funciona esta sintaxe.

| Sintaxe da amostra | Resultados | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Porque a variável começa com o padrão, foi substituído. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Como a variável não termina com o padrão, não houve mudança.|

### <a name="find-and-replace"></a>Encontrar e substituir
A sintaxe find e substituir é descrita na tabela seguinte.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variável/Encontrar/Substituir} | Encontre e substitua a primeira ocorrência do padrão especificado. |
| %{Variável//Encontrar/Substituir} | Encontre e substitua todas as ocorrências do padrão especificado. |
| %{Variável^} |Converta todo o valor em maiúsculas. |
| %{Variável^Find} | Converta a primeira ocorrência do padrão especificado em maiúsculas. |
| %{Variável,} | Converta todo o valor em minúsculas. |
| %{Variável,Find} | Converta a primeira ocorrência do padrão especificado em minúscula. |

### <a name="find-and-rewrite"></a>Encontrar e reescrever
Para uma variação no find e substitua, utilize o texto que corresponde ao padrão especificado ao reescrevê-lo. A sintaxe de encontrar e reescrever é descrita na tabela seguinte.

| Sintaxe | Ação |
| ------ | ------ |
| %{Variável/=Encontrar/Reescrever} | Encontre, copie e reescreva todas as ocorrências do padrão especificado. |
| %{Variável/^Encontrar/Reescrever} | Encontre, copie e reescreva o padrão especificado quando ocorrer no início da variável. |
| %{Variável/$Find/Reescrever} | Encontre, copie e reescreva o padrão especificado quando ocorrer no final da variável. |
| %{Variável/Encontrar} | Encontre e elimine todas as ocorrências do padrão especificado. |

Informação-chave:

- Expandir texto que corresponda ao padrão especificado especificando um sinal de dólar seguido por um inteiro inteiro inteiro (por exemplo, $1).

- Vários padrões podem ser especificados. A ordem em que o padrão é especificado determina o inteiro que lhe será atribuído. No exemplo seguinte, o primeiro padrão corresponde a "www.", o segundo padrão corresponde ao domínio do segundo nível, e o terceiro padrão corresponde ao domínio de nível superior:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- O valor reescrito pode consistir em qualquer combinação de texto e estes espaços reservados.

    No exemplo anterior, o nome de anfitrião é reescrito `cdn.$2.$3:80` para (por exemplo, cdn.mydomain.com:80).

- O caso de um espaço reservado padrão (por exemplo, $1) pode ser modificado através das seguintes bandeiras:
     - U: Maiúsculo o valor expandido.

         Sintaxe da amostra:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Minúscula o valor expandido.

         Sintaxe da amostra:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Um operador deve ser especificado antes do padrão. O operador especificado determina o comportamento de captura de padrões:

     - `=`: Indica que todas as ocorrências do padrão especificado devem ser capturadas e reescritas.
     - `^`: Indica que só o texto que começa com o padrão especificado será capturado.
     - `$`: Indica que só o texto que termina com o padrão especificado será capturado.
 
- Se omite o valor */Reescrever,* o texto que corresponde ao padrão é eliminado.


