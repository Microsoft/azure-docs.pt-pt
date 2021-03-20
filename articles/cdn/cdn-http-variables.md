---
title: Variáveis HTTP para Azure CDN regras motor | Microsoft Docs
description: Saiba mais sobre variáveis HTTP, que lhe permitem obter metadados de pedido e resposta HTTP para algumas funcionalidades do motor de regras. Utilize metadados para alterar um pedido/resposta.
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
ms.openlocfilehash: a2d9fc98ba6f514afbd88e543a859a69e0fc6c6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88192669"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variáveis HTTP para motor de regras Azure CDN
As variáveis HTTP fornecem os meios através dos quais pode recuperar metadados http e resposta. Estes metadados podem então ser utilizados para alterar dinamicamente um pedido ou uma resposta. A utilização de variáveis HTTP limita-se às seguintes regras do motor:

- [Reescrita de chave de cache](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm)
- [Modificar o cabeçalho de pedido do cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm)
- [Modificar o cabeçalho de resposta do cliente](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm)
- [Redirecionamento de URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm)
- [Reescrita de URL](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm)

## <a name="definitions"></a>Definições
A tabela seguinte descreve as variáveis HTTP suportadas. Um valor em branco é devolvido quando os metadados GEO (por exemplo, código postal) não estão disponíveis para um pedido específico.


| Name | Variável | Descrição | Valor da amostra |
| ---- | -------- | ----------- | ------------ |
| ASN (Solicitador) | %{geo_asnum} | Indica o número de AS do solicitador. <br /><br />**Precado:** %{virt_dst_asnum}. <br />Esta variável foi depreciada a favor de %{geo_asnum}. Embora uma regra que usa esta variável prevadida continue a funcionar, você deve atualizá-la para usar a nova variável. | AS15133 |
| Cidade (Solicitador) | %{geo_city} | Indica a cidade do solicitador. | Los Angeles |
| Continente (Solicitador) | %{geo_continent} | Indica o continente do solicitador através da sua abreviatura. <br />Os valores válidos são: <br />AF: África<br />AS: Ásia<br />UE: Europa<br />América do Norte<br />OC: Oceania<br />SA: América do Sul<br /><br />**Precado:** %{virt_dst_continent}. <br />Esta variável foi depreciada a favor de %{geo_continent}. <br />Embora uma regra que usa esta variável prevadida continue a funcionar, você deve atualizá-la para usar a nova variável.| N/D |
| Valor de Cookies | %{cookie_Cookie} | Devolve o valor correspondente à chave de cookies identificada pelo termo Cookie. | Utilização da amostra: <br />%{cookie__utma}<br /><br />Valor da amostra:<br />111662281.2.10.1222100123 |
| País/Região (Solicitador) | %{geo_country} | Indica o país/região de origem do solicitador através do seu código país/região. <br />**Precado:** %{virt_dst_country}. <br /><br />Esta variável foi depreciada a favor de %{geo_country}. Embora uma regra que usa esta variável prevadida continue a funcionar, você deve atualizá-la para usar a nova variável. | EUA |
| Área de Mercado designada (Solicitador) | %{geo_dma_code} |Indica o mercado de media do solicitador pelo seu código de região. <br /><br />Este campo só é aplicável aos pedidos originários dos Estados Unidos.| 745 |
| Método de Pedido HTTP | %{request_method} | Indica o método de pedido HTTP. | GET |
| Código de Estado HTTP | %{status} | Indica o código de estado HTTP para a resposta. | 200 |
| Endereço IP (Solicitador) | %{virt_dst_addr} | Indica o endereço IP do solicitador. | 192.168.1.1 |
| Latitude (Solicitador) | %{geo_latitude} | Indica a latitude do solicitador. | 34.0995 |
| Longitude (Solicitador) | %{geo_longitude} | Indica a longitude do solicitador. | -118.4143 |
| Área Metropolitana de Estatística (Solicitador) | %{geo_metro_code} | Indica a área metropolitana do solicitador. <br /><br />Este campo só é aplicável aos pedidos originários dos Estados Unidos.<br />| 745 |
| Porta (Solicitador) | %{virt_dst_port} | Indica a porta efémera do solicitador. | 55885 |
| Código Postal (Solicitador) | %{geo_postal_code} | Indica o código postal do solicitador. | 90210 |
| Cadeia de consulta encontrada | %{is_args} | O valor desta variável varia de acordo com se o pedido contém uma cadeia de consulta.<br /><br />- Consulta string found: ?<br />- Sem cadeia de consulta: NU: NU | ? |
| Parâmetro de cadeia de consulta encontrado | %{is_amp} | O valor desta variável varia de acordo com o facto de o pedido conter pelo menos um parâmetro de cadeia de consulta.<br /><br />- Parâmetro encontrado: &<br />- Sem Parâmetros: NULO | & |
| Valor do parâmetro da cadeia de consulta | %{arg_ parâmetro &lt; &gt; } | Devolve o valor correspondente ao parâmetro de cadeia de consulta identificado pelo &lt; &gt; termo parâmetro. | Utilização da amostra: <br />%{arg_language}<br /><br />Parâmetro de cadeia de consulta de amostra: <br />?language=en<br /><br />Valor da amostra: en |
| Valor da cadeia de consulta | %{query_string} | Indica todo o valor da cadeia de consulta definida no URL de pedido. |key1=val1&key2=val2&key3=val3 |
| Domínio do Remetente | %{referring_domain} | Indica o domínio definido no cabeçalho de pedido do Referrante. | <www.google.com> |
| Região (Solicitador) | %{geo_region} | Indica a região do solicitador (por exemplo, estado ou província) através da sua abreviatura alfanumérica. | CA |
| Valor do Cabeçalho de Pedido | %{http_RequestHeader} | Devolve o valor correspondente ao cabeçalho de pedido identificado pelo termo RequestHeader. <br /><br />Se o nome do cabeçalho de pedido contiver um traço (por exemplo, User-Agent), substitua-o por um sublinhado (por exemplo, User_Agent).| Utilização da amostra: %{http_Connection}<br /><br />Valor da amostra: Keep-Alive | 
| Pedido anfitrião | %{anfitrião} | Indica o anfitrião definido no URL de pedido. | <www.mydomain.com> |
| Protocolo de Pedido | %{request_protocol} | Indica o protocolo de pedido. | HTTP/1.1 |
| Regime de Pedidos | %{esquema} | Indica o esquema de pedido. |http |
| Pedido URI (Relativo) | %{request_uri} | Indica o caminho relativo, incluindo a cadeia de consulta, definida no pedido URI. | /marketing/foo.js?loggedin=true |
| Pedido URI (Parente sem cadeia de consulta) | %{uri} | Indica o caminho relativo para o conteúdo solicitado. <br /><br/>Informações-chave:<br />- Este caminho relativo exclui a cadeia de consulta.<br />- Este caminho relativo reflete reescritas de URL. Um URL será reescrito nas seguintes condições:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Funcionalidade de Reescrita de URL: Esta funcionalidade reescreve o caminho relativo definido no pedido URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL EDGE CNAME: Este tipo de pedido é reescrito para o URL CDN correspondente. |/800001/corigin/reescrito/foo.js |
| URI do pedido | %{pedido} | Descreve o pedido. <br />Sintaxe: &lt; método HTTP &gt; &lt; protocolo &gt; &lt; HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Valor do cabeçalho de resposta | %{resp_ &lt; ResponseHeader &gt; } | Devolve o valor correspondente ao cabeçalho de resposta identificado pelo &lt; termo ResponseHeader. &gt; <br /><br />Se o nome do cabeçalho de resposta contiver um traço (por exemplo, User-Agent), substitua-o por um sublinhado (por exemplo, User_Agent). | Utilização da amostra: %{resp_Content_Length}<br /><br />Valor da amostra: 100 |

## <a name="usage"></a>Utilização
A tabela seguinte descreve a sintaxe adequada para especificar uma variável HTTP.


| Syntax | Exemplo | Descrição |
| ------ | -------- | ---------- |
| %{ &lt; HTTPVariable &gt; } | %{anfitrião} | Utilize esta sintaxe para obter o valor total correspondente ao &lt; HTTPVariable &gt; especificado. |
| %{ &lt; HTTPVariableDelimiter &gt; } | %{anfitrião,} | Utilize esta sintaxe para definir a caixa para o valor total correspondente ao  &lt; httpvariableDelimiter especificado &gt; . |
| %{ &lt; HTTPVariableDelimiterExpression &gt; } | %{host/=^www \. \. ([^]+) \. ([^:+)/cdn.$2.$3:80} \. | Utilize uma expressão regular para &lt; HTTPVariableDelimiterExpression &gt; para substituir, eliminar ou manipular o valor de uma variável HTTP. |

Os nomes variáveis HTTP apenas suportam caracteres alfabéticos e sublinhados. Converter caracteres não suportados em sublinhados.

## <a name="delimiter-reference"></a>Referência delimiter
Um delimiter pode ser especificado após uma variável HTTP para obter qualquer um dos seguintes efeitos:

- Transforme o valor associado à variável.

     Exemplo: Converter todo o valor em minúsculas.

- Eliminar o valor associado à variável.

- Manipular o valor associado à variável.

     Exemplo: Utilize expressões regulares para alterar o valor associado à variável HTTP.

Os delimiters são descritos na tabela seguinte.

| Delimitador | Description |
| --------- | ----------- |
| := | Indica que um valor predefinido será atribuído à variável quando for: <br />- Desaparecido <br />- Pronto para NU. |
| :+ | Indica que um valor predefinido será atribuído à variável quando lhe foi atribuído um valor. |
| : | Indica que será expandido um sublamento do valor atribuído à variável. |
| # | Indica que o padrão especificado após este delimiter deve ser eliminado quando é encontrado no início do valor associado à variável. |
| % | Indica que o padrão especificado após este delimiter deve ser eliminado quando é encontrado no final do valor associado à variável. <br />Esta definição só é aplicável quando o símbolo % é utilizado como umlimiter. |
| / | Delimita uma variável HTTP ou um padrão. |
| // | Encontre e substitua todas as instâncias do padrão especificado. |
| /= | Encontre, copie e reescreva todas as ocorrências do padrão especificado. |
| , | Converter o valor associado à variável HTTP para minúscula. |
| ^ | Converter o valor associado à variável HTTP para maiúscula. |
| ,, | Converter todas as instâncias do carácter especificado no valor associado à variável HTTP para minúscula. |
| ^^ | Converter todas as instâncias do carácter especificado no valor associado à variável HTTP para maiúscula. |

## <a name="exceptions"></a>Exceções
A tabela a seguir descreve circunstâncias em que o texto especificado não é tratado como uma variável HTTP.

| Condição | Descrição | Exemplo |
| --------- | ----------- | --------|
| Símbolo de fuga % | O símbolo percentual pode ser escapado através da utilização de uma pestana. <br />O valor da amostra à direita será tratado como um valor literal e não como uma variável HTTP.| \%{anfitrião} |
| Variáveis desconhecidas | Uma corda vazia é sempre devolvida para variáveis desconhecidas. | %{unknown_variable} |
| Caracteres inválidos ou sintaxe | As variáveis que contêm caracteres inválidos ou sintaxe são tratadas como valores literais. <br /><br />Exemplo #1: O valor especificado contém um carácter inválido (por exemplo, -). <br /><br />Exemplo #2: O valor especificado contém um duplo conjunto de aparelhos encaracolados. <br /><br />Exemplo #3: Falta um aparelho de fecho curly.<br /> | Exemplo #1: %{resp_user-agent} <br /><br />Exemplo #2: %{{host}} <br /><br />Exemplo #3: %{host |
| Nome variável em falta | Um valor NULO é sempre devolvido quando uma variável não é especificada. | %{} |
| Personagens de rasto | Os caracteres que percorrem uma variável são tratados como valores literais. <br />O valor da amostra à direita contém uma cinta encaracolada que será tratada como um valor literal. | %{host}} |

## <a name="setting-default-header-values"></a>Definição de valores de cabeçalho predefinido
Um valor predefinido pode ser atribuído a um cabeçalho quando satisfaz qualquer uma das seguintes condições:
- Desaparecido/desaparato
- Definido para NU.

A tabela seguinte descreve como definir um valor padrão.

| Condição | Syntax | Exemplo | Descrição |
| --------- | ------ | --------| ----------- |
| Desafine um cabeçalho para um valor predefinido quando satisfaça qualquer uma das seguintes condições: <br /><br />- Cabeçalho desaparecido <br /><br />- O valor do cabeçalho está definido para NU.| %{Variável:=Valor} | %{http_referrer:=não especificado} | O cabeçalho do remetente só será definido para *não especificado* quando estiver em falta ou definido para NU. Não haverá ação se tiver sido definida. |
| Desa parte para um valor predefinido quando estiver em falta. | %{Variável=Valor} | %{http_referrer=não especificado} | O cabeçalho do remetente só será definido para *não especificado* quando estiver em falta. Não haverá ação se tiver sido definida. |
| Desa quando não satisfaça nenhuma das seguintes condições: <br /><br />- Desaparecido<br /><br /> - Pronto para NU. | %{Variável:+Valor} | %{http_referrer:+não especificado} | O cabeçalho do referrante só será definido para *não especificado* quando lhe for atribuído um valor. Não haverá ação se estiver desaparecida ou definida para NU. |

## <a name="manipulating-variables"></a>Manipulação de variáveis
As variáveis podem ser manipuladas das seguintes formas:

- Sublagem em expansão
- Remoção de padrões

### <a name="substring-expansion"></a>Expansão do sub-adundo
Por padrão, uma variável expandir-se-á para o seu valor total. Utilize a seguinte sintaxe para expandir apenas um sublamento do valor da variável.

`%<Variable>:<Offset>:<Length>}`

Informações-chave:

- O valor atribuído ao termo Offset determina o carácter inicial do sublmotivo:

     - Positivo: O carácter inicial do sublcípe em questão é calculado a partir do primeiro carácter da cadeia.
     - Zero: O personagem inicial do sublpeso é o primeiro personagem da corda.
     - Negativo: O carácter inicial do sublcípe em questão é calculado a partir do último carácter da cadeia.

- O comprimento do subluto é determinado pelo *termo comprimento:*

     - Omitido: Omitir o termo Comprimento permite que o sub-comprimento inclua todos os caracteres entre o personagem inicial e a extremidade da cadeia.
     - Positivo: Determina o comprimento do sublamamento do personagem inicial para a direita.
     - Negativo: Determina o comprimento do sublamamento do personagem inicial para a esquerda.

#### <a name="example"></a>Exemplo:

O exemplo a seguir baseia-se no seguinte URL de pedido de amostra:

https: \/ /cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A seguinte cadeia demonstra vários métodos de manipulação de variáveis:

https: \/ /www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Com base no URL de pedido de amostra, a manipulação variável acima produzirá o seguinte valor:

https: \/ /www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Remoção de padrão
O texto que corresponde a um padrão específico pode ser removido do início ou do fim do valor de uma variável.

| Syntax | Ação |
| ------ | ------ |
| %{Variável#Padrão} | Remova o texto quando o padrão especificado for encontrado no início do valor de uma variável. |
| %{Variável%padrão} | Remova o texto quando o padrão especificado for encontrado no final do valor de uma variável. |

#### <a name="example"></a>Exemplo:

Neste cenário de amostra, a variável *request_uri* é definida para:

`/800001/myorigin/marketing/product.html?language=en-US`

A tabela a seguir demonstra como funciona esta sintaxe.

| Sintaxe da amostra | Resultados | Description |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Como a variável começa com o padrão, foi substituída. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Como a variável não termina com o padrão, não houve mudança.|

### <a name="find-and-replace"></a>Encontrar e substituir
A sintaxe de encontrar e substituir é descrita na tabela seguinte.

| Syntax | Ação |
| ------ | ------ |
| %{Variável/Localizar/Substituir} | Encontre e substitua a primeira ocorrência do padrão especificado. |
| %{Variável//Localizar/Substituir} | Encontre e substitua todas as ocorrências do padrão especificado. |
| %{Variável^} |Converta todo o valor em maiúsculas. |
| %{Variable^Find} | Converter a primeira ocorrência do padrão especificado para maiúscula. |
| %{Variável,} | Converta todo o valor em minúsculas. |
| %{Variável,Encontre} | Converter a primeira ocorrência do padrão especificado para minúscula. |

### <a name="find-and-rewrite"></a>Encontrar e reescrever
Para obter uma variação do achado e substituir, utilize o texto que corresponda ao padrão especificado ao reescrevê-lo. A sintaxe de encontrar e reescrever é descrita na tabela seguinte.

| Syntax | Ação |
| ------ | ------ |
| %{Variável/=Localizar/Reescrever} | Encontre, copie e reescreva todas as ocorrências do padrão especificado. |
| %{Variável/^Localizar/Reescrever} | Encontre, copie e reescreva o padrão especificado quando ocorre no início da variável. |
| %{Variável/$Find/Reescrever} | Encontre, copie e reescreva o padrão especificado quando ocorre no final da variável. |
| %{Variável/Encontrar} | Encontre e elimine todas as ocorrências do padrão especificado. |

Informações-chave:

- Expandir texto que corresponda ao padrão especificado especificando um sinal de dólar seguido de um inteiro inteiro (por exemplo, $1).

- Vários padrões podem ser especificados. A ordem pela qual o padrão é especificado determina o número inteiro que lhe será atribuído. No exemplo seguinte, o primeiro padrão corresponde a "www.", o segundo padrão corresponde ao domínio do segundo nível, e o terceiro padrão corresponde ao domínio de nível superior:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- O valor reescrito pode consistir em qualquer combinação de texto e estes espaços reservados.

    No exemplo anterior, o nome de anfitrião é reescrito `cdn.$2.$3:80` para (por exemplo, cdn.mydomain.com:80).

- O caso de um espaço reservado para padrão (por exemplo, $1) pode ser modificado através das seguintes bandeiras:
     - U: Maiúscula o valor expandido.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Minúscula o valor expandido.

         Sintaxe de exemplo:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Um operador deve ser especificado antes do padrão. O operador especificado determina o comportamento de captura de padrões:

     - `=`: Indica que todas as ocorrências do padrão especificado devem ser capturadas e reescritas.
     - `^`: Indica que apenas o texto que começa com o padrão especificado será capturado.
     - `$`: Indica que apenas o texto que termina com o padrão especificado será capturado.
 
- Se omitir o valor */Reescrever,* o texto que corresponde ao padrão é eliminado.


