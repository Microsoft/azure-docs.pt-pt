---
title: Grupos e regras de regras do CRS
titleSuffix: Azure Web Application Firewall
description: Esta página fornece informações sobre grupos e regras de regras CRS de firewall de aplicação web.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 0303f09e5e704a18576bf50d1f00007f7f86f320
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279250"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Grupos e regras de regras CRS de firewall de aplicação web

Application Gateway web application firewall (WAF) protege aplicações web de vulnerabilidades e explorações comuns. Isto é feito através de regras definidas com base na regra central da OWASP definidas 3.1, 3.0 ou 2.2.9. Estas regras podem ser desativadas numa base de regra por regra. Este artigo contém as regras e os conjuntos de regras atuais oferecidos.

## <a name="core-rule-sets"></a>Conjuntos de regras principais

O Gateway WAF da aplicação vem pré-configurado com CRS 3.0 por padrão. Mas pode optar por utilizar CRS 3.1 ou CRS 2.2.9. CrS 3.1 oferece novos conjuntos de regras defendendo contra infeções java, um conjunto inicial de verificações de upload de ficheiros, falsos positivos fixos, e muito mais. CrS 3.0 oferece falsos positivos reduzidos em comparação com CRS 2.2.9. Também pode [personalizar regras de acordo com as suas necessidades.](application-gateway-customize-waf-rules-portal.md)

> [!div class="mx-imgBorder"]
> ![gere as regras](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

O WAF protege contra as seguintes vulnerabilidades web:

- Ataques de injeção SQL
- Ataques de scripts de sites cruzados
- Outros ataques comuns, tais como injeção de comando, HTTP solicitam contrabando, divisão de resposta HTTP e inclusão de ficheiros remotos
- Violações do protocolo HTTP
- Anomalias protocolares HTTP, tais como falta de agente de utilizador anfitrião e cabeçalhos
- Bots, rastejadores e scanners
- Configurações de aplicações comuns (por exemplo, Apache e IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3.1

O CRS 3.1 inclui 13 grupos de regras, como mostra a tabela seguinte. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[Geral](#general-31)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Proteger contra scanners portuários e ambientais|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Proteger contra questões de protocolo e codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Proteja contra a injeção de cabeçalho, peça contrabando e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Proteja contra ataques de ficheiros e caminhos|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Proteja contra ataques de inclusão de ficheiros remotos (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Proteja novamente os ataques de execução de código remoto|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Proteja contra ataques de scripts de sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Proteja contra ataques de injeção SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Proteger contra ataques de fixação de sessão|
|**[PEDIDO-944-APPLICATION-ATTACK-SESSION-JAVA](#crs944-31)**|Proteger contra ataques JAVA|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

O CRS 3.0 inclui 12 grupos de regras, como mostra a tabela seguinte. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[Geral](#general-30)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Proteger contra scanners portuários e ambientais|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Proteger contra questões de protocolo e codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Proteja contra a injeção de cabeçalho, peça contrabando e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Proteja contra ataques de ficheiros e caminhos|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Proteja contra ataques de inclusão de ficheiros remotos (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Proteja novamente os ataques de execução de código remoto|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Proteja contra ataques de scripts de sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Proteja contra ataques de injeção SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Proteger contra ataques de fixação de sessão|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

CrS 2.2.9 inclui 10 grupos de regras, como mostra a tabela seguinte. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Proteja contra violações de protocolos (tais como caracteres inválidos ou um GET com um corpo de pedido)|
|**[crs_21_protocol_anomalies](#crs21)**|Proteja contra informações incorretas do cabeçalho|
|**[crs_23_request_limits](#crs23)**|Proteja contra argumentos ou ficheiros que excedam limitações|
|**[crs_30_http_policy](#crs30)**|Proteger contra métodos, cabeçalhos e tipos de ficheiros restritos|
|**[crs_35_bad_robots](#crs35)**|Proteja contra web crawlers e scanners|
|**[crs_40_generic_attacks](#crs40)**|Proteger contra ataques genéricos (tais como fixação de sessão, inclusão de ficheiros remotos e injeção de PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Proteja contra ataques de injeção SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Proteja contra ataques de scripts de sites|
|**[crs_42_tight_security](#crs42)**|Proteger contra ataques de via saque|
|**[crs_45_trojans](#crs45)**|Proteja contra cavalos de Troia backdoor|

Os seguintes grupos e regras estão disponíveis ao utilizar firewall de aplicação web no Gateway da aplicação.

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="owasp31"></a>Conjuntos de regras

### <a name="general-31"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Possível limite multipart incomparável.|

### <a name="crs911-31"></a> <p x-ms-format-detection="none">PEDIDO-911-APLICAÇÃO DO MÉTODO</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECÇÃO</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado Utilizador-Agente associado ao scanner de segurança|
|913101|Agente de utilizador encontrado associado a scripts/cliente genérico http|
|913102|Encontrado Utilizador-Agente associado com web crawler/bot|
|913110|Cabeçalho de pedido encontrado associado ao scanner de segurança|
|913120|Nome/argumento de pedido encontrado associado ao scanner de segurança|


### <a name="crs920-31"></a> <p x-ms-format-detection="none">PEDIDO-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de Pedido http inválida|
|920120|Tentativa de bypass multiparte/forma-data|
|920121|Tentativa de bypass multiparte/forma-data|
|920130|Falhou em analisar o corpo de pedido.|
|920140|Corpo de pedido multipart falhou validação rigorosa|
|920160|O cabeçalho HTTP do comprimento do conteúdo não é numérico.|
|920170|PEDIDO DE GET ou HEAD com conteúdo corporal.|
|920171|PEDIDO GET ou HEAD com Codificação de Transferência.|
|920180|POST request missing Content-Length Header.|
|920190|Gama = Valor Inválido último Byte.|
|920200|Alcance = Demasiados campos (6 ou mais)|
|920201|Alcance = Demasiados campos para pedido pdf (35 ou mais)|
|920202|Alcance = Demasiados campos para pedido pdf (6 ou mais)|
|920210|Encontrados dados de cabeçalho de ligação múltiplos/conflituosos.|
|920220|URL codificando tentativa de ataque de abuso|
|920230|Deteção de codificação de URL múltiplas|
|920240|URL codificando tentativa de ataque de abuso|
|920250|UTF8 codificando tentativa de ataque de abuso|
|920260|Tentativa de ataque de abuso de largura/meio-código unicódigo|
|920270|Carácter inválido a pedido (carácter nulo)|
|920271|Carácter inválido a pedido (caracteres não imprimíveis)|
|920272|Caráter inválido a pedido (fora de caritão imprimível abaixo do ascii 127)|
|920273|Caráter inválido a pedido (fora do conjunto muito rigoroso)|
|920274|Caráter inválido em cabeçalhos de pedido (fora do conjunto muito rigoroso)|
|920280|Pedido faltando um cabeçalho anfitrião|
|920290|Cabeçalho de anfitrião vazio|
|920300|Pedido faltando um cabeçalho aceitar|
|920310|Pedido tem um cabeçalho de aceitação vazio|
|920311|Pedido tem um cabeçalho de aceitação vazio|
|920320|Cabeçalho do agente do utilizador desaparecido|
|920330|Cabeçalho de agente de utilizador vazio|
|920340|Pedido contendo conteúdo, mas cabeçalho do tipo de conteúdo em falta|
|920341|Pedido que contenha conteúdo requer cabeçalho tipo conteúdo|
|920350|Cabeçalho de anfitrião é um endereço IP numérico|
|920360|Nome do argumento muito longo|
|920370|Valor do argumento demasiado longo|
|920380|Demasiados argumentos a pedido|
|920390|Tamanho total dos argumentos ultrapassado|
|920400|Tamanho de ficheiro carregado muito grande|
|920410|Total de ficheiros carregados tamanho muito grande|
|920420|O tipo de conteúdo de pedido não é permitido por política|
|920430|A versão protocole HTTP não é permitida pela política|
|920440|A extensão do ficheiro URL é restrita pela política|
|920450|O cabeçalho HTTP é restringido pela política (%@{MATCHED_VAR})|
|920460|Personagens de fuga anormais|
|920470|Cabeçalho tipo conteúdo ilegal|
|920480|Restringir o parâmetro de charset dentro do cabeçalho do tipo de conteúdo|

### <a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921110|HTTP Solicitar contrabando de ataque|
|921120|HTTP Resposta ataque de divisão|
|921130|HTTP Resposta ataque de divisão|
|921140|Ataque de injeção de cabeçalho http através de cabeçalhos|
|921150|ATAQUE DE Injeção de Cabeçalho HTTP via carga útil (CR/LF detetado)|
|921151|ATAQUE DE Injeção de Cabeçalho HTTP via carga útil (CR/LF detetado)|
|921160|HTTP Header Injection Attack via carga útil (CR/LF e nome cabeçalho detetado)|
|921170|Poluição dos parâmetros HTTP|
|921180|POLUIÇÃO DE parâmetros HTTP (%{TX.1})|

### <a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque Traversal do Caminho (/.. /)|
|930110|Ataque Traversal do Caminho (/.. /)|
|930120|Tentativa de acesso a ficheiros OS|
|930130|Tentativa restrita de acesso a ficheiros|

### <a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível inclusão de ficheiros remotos (RFI) Ataque = Parâmetro URL usando endereço IP|
|931110|Possível inclusão de ficheiros remotos (RFI) Ataque = Nome de parâmetro vulnerável RFI comum usado c/URL Carga útil|
|931120|Possível inclusão de ficheiros remotos (RFI) Ataque = URL Load Used c/Trailing Question Mark Character (?)|
|931130|Possível ataque de inclusão de ficheiros remotos (RFI) = Referência/Link off-domínio|

### <a name="crs932-31"></a> <p x-ms-format-detection="none">PEDIDO-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932100|Execução de comando remoto: Injeção de Comando Unix|
|932105|Execução de comando remoto: Injeção de Comando Unix|
|932106|Execução de comando remoto: Injeção de Comando Unix|
|932110|Execução de comando remoto: Injeção de comando windows|
|932115|Execução de comando remoto: Injeção de comando windows|
|932120|Execução de comando remoto = Comando Windows PowerShell encontrado|
|932130|Execução de comando remoto = Expressão da concha Unix encontrada|
|932140|Execução de comando remoto = janelas para/se o comando encontrado|
|932160|Execução de comando remoto = Código Unix Shell encontrado|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932180|Tentativa restrita de upload de ficheiros|
|932190|Execução de comando remoto: tentativa de técnica de bypass wildcard|

### <a name="crs933-31"></a> <p x-ms-format-detection="none">PEDIDO-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção PHP = Abertura/etiqueta de fecho encontrada|
|933110|Ataque de injeção PHP = Upload de ficheiro de script PHP encontrado|
|933111|Ataque de injeção PHP: Upload de ficheiro de script PHP encontrado|
|933120|Ataque de injeção PHP = Diretiva de configuração encontrada|
|933130|Ataque de injeção PHP = Variáveis encontradas|
|933131|Ataque de injeção php: Variáveis encontradas|
|933140|Ataque de injeção php: fluxo de I/O encontrado|
|933150|Ataque de injeção PHP = nome de função PHP de alto risco encontrado|
|933151|Ataque de injeção PHP: Nome da função PHP de risco médio encontrado|
|933160|Ataque de injeção PHP = Chamada de função PHP de alto risco encontrada|
|933161|Ataque de injeção PHP: Chamada de função PHP de baixo valor encontrada|
|933170|Ataque de injeção PHP: Injeção de objetoserializado|
|933180|Ataque de injeção PHP = Chamada de função variável encontrada|
|933190|Ataque de injeção PHP: Etiqueta de fecho PHP encontrada|

### <a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque XSS detetado por injeção por libinjecção|
|941101|Ataque XSS detetado por injeção por libinjecção|
|941110|Filtro XSS - categoria 1 = Vetor de etiqueta script|
|941130|Filtro XSS - Categoria 3 = Vetor de atributo|
|941140|Filtro XSS - Categoria 4 = Vetor JAVAScript URI|
|941150|Filtro XSS - Categoria 5 = Atributos HTML não autorizados|
|941160|NoScript XSS Injectionchecker: Injetor HTML|
|941170|NoScript XSS InjectionChecker: Atributo Injection|
|941180|Palavras-chave da lista de bloqueios do validador de nós|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando javascript obfuscated|
|941220|XSS usando roteiro VB obfuscado|
|941230|XSS usando etiqueta 'incorporado'|
|941240|XSS utilizando atributo de "importação" ou "implementação"|
|941250|Filtros IE XSS - Ataque detetado|
|941260|XSS usando tag 'meta'|
|941270|XSS usando o href 'link'|
|941280|XSS usando etiqueta 'base'|
|941290|XSS usando tag 'applet'|
|941300|XSS usando etiqueta 'object'|
|941310|Filtro XSS de codificação mal formada DOS EUA-ASCII - Ataque detetado.|
|941320|Possível ataque XSS detetado - MANIPULADOR DE EtiquetaHTML|
|941330|Filtros IE XSS - Ataque detetado.|
|941340|Filtros IE XSS - Ataque detetado.|
|941350|UTF-7 Codificação IE XSS - Ataque detetado.|


### <a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção SQL detetado por injeção de libré|
|942110|Ataque de injeção SQL: Testes comuns de injeção detetados|
|942130|Ataque de injeção SQL: Tautology SQL Detetado.|
|942140|Ataque de injeção SQL = Nomes comuns de DB detetados|
|942150|Ataque de injeção de SQL|
|942160|Deteta testes de sqli cegos utilizando o sono() ou o benchmark().|
|942170|Deteta tentativas de referência sql e injeção de sono, incluindo consultas condicionais|
|942180|Deteta tentativas básicas de bypass de autenticação SQL 1/3|
|942190|Deteta tentativas de execução de código MSSQL e tentativas de recolha de informação|
|942200|Deteta injeções de comentários MySQL/space-obfuscated e rescisão de backtick|
|942210|Deteta tentativas de injeção SQL acorrentadas 1/2|
|942220|À procura de ataques de transbordo inteiro, estes são retirados de peixe-balão, exceto 3.00738585072|
|942230|Deteta tentativas condicionais de injeção de SQL|
|942240|Deteta o interruptor de charset MySQL e as tentativas do MSSQL DoS|
|942250|Deteta MATCH CONTRA, MERGE e EXECUTA injeções imediatas|
|942251|Deteta injeções de INE|
|942260|Deteta tentativas básicas de bypass de autenticação SQL 2/3|
|942270|À procura de uma injeção básica de sql. Corda de ataque comum para oorás místicos e outros|
|942280|Deteta postgres pg_sleep injeção, espera por ataques de atraso e tentativas de encerramento de bases de dados|
|942290|Encontra tentativas básicas de injeção MongoDB SQL|
|942300|Deteta comentários, condições e injeções de MySQL|
|942310|Deteta tentativas de injeção SQL acorrentadas 2/2|
|942320|Deteta injeções de procedimento/função armazenadas MySQL e PostgreSQL|
|942330|Deteta sondagens clássicas de injeção SQL 1/2|
|942340|Deteta tentativas básicas de bypass de autenticação SQL 3/3|
|942350|Deteta a injeção da UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Deteta injeções básicas de SQL concatenadas e tentativas de SQLLFI|
|942361|Deteta a injeção básica de SQL com base na alteração ou união de palavras-chave|
|942370|Deteta sondagens clássicas de injeção SQL 2/2|
|942380|Ataque de injeção de SQL|
|942390|Ataque de injeção de SQL|
|942400|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942420|Deteção restrita de anomalias de caracteres SQL (cookies): # de caracteres especiais ultrapassados (8)|
|942421|Deteção restrita de anomalias de caracteres SQL (cookies): # de caracteres especiais ultrapassados (3)|
|942430|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais ultrapassados (12)|
|942431|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais ultrapassados (6)|
|942432|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais ultrapassados (2)|
|942440|Sequência de comentários SQL detetada.|
|942450|Codificação de hexéis SQL identificada|
|942460|Alerta de deteção de anomalias de meta-carácter - caracteres repetitivos não-palavra|
|942470|Ataque de injeção de SQL|
|942480|Ataque de injeção de SQL|
|942490|Deteta sondagens clássicas de injeção SQL 3/3|

### <a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação de sessão = definição de valores de cookies em HTML|
|943110|Possível ataque de fixação de sessão = Nome do parâmetro SessionID com referente off-domain|
|943120|Possível ataque de fixação de sessão = Nome do parâmetro SessionID sem referrer|

### <a name="crs944-31"></a> <p x-ms-format-detection="none">PEDIDO-944-APPLICATION-ATTACK-SESSION-JAVA</p>

|RuleId|Descrição|
|---|---|
|944120|Possível execução de carga útil e execução de comando remoto|
|944130|Aulas suspeitas de Java|
|944200|Exploração da desserialização de Java Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="owasp30"></a>Conjuntos de regras

### <a name="general-30"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Possível limite multipart incomparável.|

### <a name="crs911-30"></a> <p x-ms-format-detection="none">PEDIDO-911-APLICAÇÃO DO MÉTODO</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECÇÃO</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado Utilizador-Agente associado ao scanner de segurança|
|913110|Cabeçalho de pedido encontrado associado ao scanner de segurança|
|913120|Nome/argumento de pedido encontrado associado ao scanner de segurança|
|913101|Agente de utilizador encontrado associado a scripts/cliente genérico http|
|913102|Encontrado Utilizador-Agente associado com web crawler/bot|

### <a name="crs920-30"></a> <p x-ms-format-detection="none">PEDIDO-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de Pedido http inválida|
|920130|Falhou em analisar o corpo de pedido.|
|920140|Corpo de pedido multipart falhou validação rigorosa|
|920160|O cabeçalho HTTP do comprimento do conteúdo não é numérico.|
|920170|PEDIDO DE GET ou HEAD com conteúdo corporal.|
|920180|POST request missing Content-Length Header.|
|920190|Gama = Valor Inválido último Byte.|
|920210|Encontrados dados de cabeçalho de ligação múltiplos/conflituosos.|
|920220|URL codificando tentativa de ataque de abuso|
|920240|URL codificando tentativa de ataque de abuso|
|920250|UTF8 codificando tentativa de ataque de abuso|
|920260|Tentativa de ataque de abuso de largura/meio-código unicódigo|
|920270|Carácter inválido a pedido (carácter nulo)|
|920280|Pedido faltando um cabeçalho anfitrião|
|920290|Cabeçalho de anfitrião vazio|
|920310|Pedido tem um cabeçalho de aceitação vazio|
|920311|Pedido tem um cabeçalho de aceitação vazio|
|920330|Cabeçalho de agente de utilizador vazio|
|920340|Pedido contendo conteúdo, mas cabeçalho do tipo de conteúdo em falta|
|920350|Cabeçalho de anfitrião é um endereço IP numérico|
|920380|Demasiados argumentos a pedido|
|920360|Nome do argumento muito longo|
|920370|Valor do argumento demasiado longo|
|920390|Tamanho total dos argumentos ultrapassado|
|920400|Tamanho de ficheiro carregado muito grande|
|920410|Total de ficheiros carregados tamanho muito grande|
|920420|O tipo de conteúdo de pedido não é permitido por política|
|920430|A versão protocole HTTP não é permitida pela política|
|920440|A extensão do ficheiro URL é restrita pela política|
|920450|O cabeçalho HTTP é restringido pela política (%@{MATCHED_VAR})|
|920200|Alcance = Demasiados campos (6 ou mais)|
|920201|Alcance = Demasiados campos para pedido pdf (35 ou mais)|
|920230|Deteção de codificação de URL múltiplas|
|920300|Pedido faltando um cabeçalho aceitar|
|920271|Carácter inválido a pedido (caracteres não imprimíveis)|
|920320|Cabeçalho do agente do utilizador desaparecido|
|920272|Caráter inválido a pedido (fora de caritão imprimível abaixo do ascii 127)|
|920202|Alcance = Demasiados campos para pedido pdf (6 ou mais)|
|920273|Caráter inválido a pedido (fora do conjunto muito rigoroso)|
|920274|Caráter inválido em cabeçalhos de pedido (fora do conjunto muito rigoroso)|
|920460|Personagens de fuga anormais|

### <a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921100|HTTP Solicite contrabando de ataque.|
|921110|HTTP Solicitar contrabando de ataque|
|921120|HTTP Resposta ataque de divisão|
|921130|HTTP Resposta ataque de divisão|
|921140|Ataque de injeção de cabeçalho http através de cabeçalhos|
|921150|ATAQUE DE Injeção de Cabeçalho HTTP via carga útil (CR/LF detetado)|
|921160|HTTP Header Injection Attack via carga útil (CR/LF e nome cabeçalho detetado)|
|921151|ATAQUE DE Injeção de Cabeçalho HTTP via carga útil (CR/LF detetado)|
|921170|Poluição dos parâmetros HTTP|
|921180|POLUIÇÃO DE PARÂMETROS HTTP (%@{TX.1})|

### <a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque Traversal do Caminho (/.. /)|
|930110|Ataque Traversal do Caminho (/.. /)|
|930120|Tentativa de acesso a ficheiros OS|
|930130|Tentativa restrita de acesso a ficheiros|

### <a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível inclusão de ficheiros remotos (RFI) Ataque = Parâmetro URL usando endereço IP|
|931110|Possível inclusão de ficheiros remotos (RFI) Ataque = Nome de parâmetro vulnerável RFI comum usado c/URL Carga útil|
|931120|Possível inclusão de ficheiros remotos (RFI) Ataque = URL Load Used c/Trailing Question Mark Character (?)|
|931130|Possível ataque de inclusão de ficheiros remotos (RFI) = Referência/Link off-domínio|

### <a name="crs932-30"></a> <p x-ms-format-detection="none">PEDIDO-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932120|Execução de comando remoto = Comando Windows PowerShell encontrado|
|932130|Execução de comando remoto = Expressão da concha Unix encontrada|
|932140|Execução de comando remoto = janelas para/se o comando encontrado|
|932160|Execução de comando remoto = Código Unix Shell encontrado|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|

### <a name="crs933-30"></a> <p x-ms-format-detection="none">PEDIDO-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção PHP = Abertura/etiqueta de fecho encontrada|
|933110|Ataque de injeção PHP = Upload de ficheiro de script PHP encontrado|
|933120|Ataque de injeção PHP = Diretiva de configuração encontrada|
|933130|Ataque de injeção PHP = Variáveis encontradas|
|933150|Ataque de injeção PHP = nome de função PHP de alto risco encontrado|
|933160|Ataque de injeção PHP = Chamada de função PHP de alto risco encontrada|
|933180|Ataque de injeção PHP = Chamada de função variável encontrada|
|933151|Ataque de injeção PHP = Nome da função PHP de risco médio encontrado|
|933131|Ataque de injeção PHP = Variáveis encontradas|
|933161|Ataque de injeção PHP = chamada de função PHP de baixo valor encontrada|
|933111|Ataque de injeção PHP = Upload de ficheiro de script PHP encontrado|

### <a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque XSS detetado por injeção por libinjecção|
|941110|Filtro XSS - categoria 1 = Vetor de etiqueta script|
|941130|Filtro XSS - Categoria 3 = Vetor de atributo|
|941140|Filtro XSS - Categoria 4 = Vetor JAVAScript URI|
|941150|Filtro XSS - Categoria 5 = Atributos HTML não autorizados|
|941180|Palavras-chave da lista de bloqueios do validador de nós|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando javascript obfuscated|
|941220|XSS usando roteiro VB obfuscado|
|941230|XSS usando etiqueta 'incorporado'|
|941240|XSS utilizando atributo de "importação" ou "implementação"|
|941260|XSS usando tag 'meta'|
|941270|XSS usando o href 'link'|
|941280|XSS usando etiqueta 'base'|
|941290|XSS usando tag 'applet'|
|941300|XSS usando etiqueta 'object'|
|941310|Filtro XSS de codificação mal formada DOS EUA-ASCII - Ataque detetado.|
|941330|Filtros IE XSS - Ataque detetado.|
|941340|Filtros IE XSS - Ataque detetado.|
|941350|UTF-7 Codificação IE XSS - Ataque detetado.|
|941320|Possível ataque XSS detetado - MANIPULADOR DE EtiquetaHTML|

### <a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção SQL detetado por injeção de libré|
|942110|Ataque de injeção SQL: Testes comuns de injeção detetados|
|942130|Ataque de injeção SQL: Tautology SQL Detetado.|
|942140|Ataque de injeção SQL = Nomes comuns de DB detetados|
|942160|Deteta testes de sqli cegos utilizando o sono() ou o benchmark().|
|942170|Deteta tentativas de referência sql e injeção de sono, incluindo consultas condicionais|
|942190|Deteta tentativas de execução de código MSSQL e tentativas de recolha de informação|
|942200|Deteta injeções de comentários MySQL/space-obfuscated e rescisão de backtick|
|942230|Deteta tentativas condicionais de injeção de SQL|
|942260|Deteta tentativas básicas de bypass de autenticação SQL 2/3|
|942270|À procura de uma injeção básica de sql. Corda de ataque comum para o oráculo mistículo e outros.|
|942290|Encontra tentativas básicas de injeção MongoDB SQL|
|942300|Deteta comentários, condições e injeções de MySQL|
|942310|Deteta tentativas de injeção SQL acorrentadas 2/2|
|942320|Deteta injeções de procedimento/função armazenadas MySQL e PostgreSQL|
|942330|Deteta sondagens clássicas de injeção SQL 1/2|
|942340|Deteta tentativas básicas de bypass de autenticação SQL 3/3|
|942350|Deteta a injeção da UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Deteta injeções básicas de SQL concatenadas e tentativas de SQLLFI|
|942370|Deteta sondagens clássicas de injeção SQL 2/2|
|942150|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942430|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais ultrapassados (12)|
|942440|Sequência de comentários SQL detetada.|
|942450|Codificação de hexéis SQL identificada|
|942251|Deteta injeções de INE|
|942460|Alerta de deteção de anomalias de meta-carácter - caracteres repetitivos não-palavra|

### <a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação de sessão = definição de valores de cookies em HTML|
|943110|Possível ataque de fixação de sessão = Nome do parâmetro SessionID com referente off-domain|
|943120|Possível ataque de fixação de sessão = Nome do parâmetro SessionID sem referrer|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a>Conjuntos de regras

### <a name="crs20"></a>crs_20_protocol_violations

|RuleId|Descrição|
|---|---|
|960911|Linha de Pedido http inválida|
|981227|Erro Apache = URI inválido em Pedido.|
|960912|Falhou em analisar o corpo de pedido.|
|960914|Corpo de pedido multipart falhou validação rigorosa|
|960915|O parser multipart detetou um possível limite inigualável.|
|960016|O cabeçalho HTTP do comprimento do conteúdo não é numérico.|
|960011|PEDIDO DE GET ou HEAD com conteúdo corporal.|
|960012|POST request missing Content-Length Header.|
|960902|Utilização inválida da codificação de identidade.|
|960022|Espere cabeçalho não permitido para HTTP 1.0.|
|960020|O Cabeçalho Pragma requer cabeçalho de controlo de cache para pedidos HTTP/1.1.|
|958291|Alcance = campo existe e começa com 0.|
|958230|Gama = Valor Inválido último Byte.|
|958295|Encontrados dados de cabeçalho de ligação múltiplos/conflituosos.|
|950107|URL codificando tentativa de ataque de abuso|
|950109|Deteção de codificação de URL múltiplas|
|950108|URL codificando tentativa de ataque de abuso|
|950801|UTF8 codificando tentativa de ataque de abuso|
|950116|Tentativa de ataque de abuso de largura/meio-código unicódigo|
|960901|Caráter inválido a pedido|
|960018|Caráter inválido a pedido|

### <a name="crs21"></a>crs_21_protocol_anomalies

|RuleId|Descrição|
|---|---|
|960008|Pedido faltando um cabeçalho anfitrião|
|960007|Cabeçalho de anfitrião vazio|
|960015|Pedido faltando um cabeçalho aceitar|
|960021|Pedido tem um cabeçalho de aceitação vazio|
|960009|Pedido faltando um cabeçalho de agente de utilizador|
|960006|Cabeçalho de agente de utilizador vazio|
|960904|Pedido contendo conteúdo, mas cabeçalho do tipo de conteúdo em falta|
|960017|Cabeçalho de anfitrião é um endereço IP numérico|

### <a name="crs23"></a>crs_23_request_limits

|RuleId|Descrição|
|---|---|
|960209|Nome do argumento muito longo|
|960208|Valor do argumento demasiado longo|
|960335|Demasiados argumentos a pedido|
|960341|Tamanho total dos argumentos ultrapassado|
|960342|Tamanho de ficheiro carregado muito grande|
|960343|Total de ficheiros carregados tamanho muito grande|

### <a name="crs30"></a>crs_30_http_policy

|RuleId|Descrição|
|---|---|
|960032|O método não é permitido pela política|
|960010|O tipo de conteúdo de pedido não é permitido por política|
|960034|A versão protocole HTTP não é permitida pela política|
|960035|A extensão do ficheiro URL é restrita pela política|
|960038|O cabeçalho HTTP é restringido pela política|

### <a name="crs35"></a>crs_35_bad_robots

|RuleId|Descrição|
|---|---|
|990002|Pedido indica um scanner de segurança digitalizado no site|
|990901|Pedido indica um scanner de segurança digitalizado no site|
|990902|Pedido indica um scanner de segurança digitalizado no site|
|990012|Crawler site fraudulento|

### <a name="crs40"></a>crs_40_generic_attacks

|RuleId|Descrição|
|---|---|
|960024|Alerta de deteção de anomalias de meta-carácter - caracteres repetitivos não-palavra|
|950008|Injeção de etiquetas de fusão fria não documentadas|
|950010|Ataque de injeção lDAP|
|950011|Ataque de injeção SSI|
|950018|URL Universal PDF XSS Detetado.|
|950019|Ataque de injeção de e-mail|
|950012|HTTP Solicite contrabando de ataque.|
|950910|HTTP Resposta ataque de divisão|
|950911|HTTP Resposta ataque de divisão|
|950117|Ataque de inclusão de ficheiros remotos|
|950118|Ataque de inclusão de ficheiros remotos|
|950119|Ataque de inclusão de ficheiros remotos|
|950120|Possível ataque de inclusão de ficheiros remotos (RFI) = Referência/Link off-domínio|
|981133|Regra 981133|
|981134|Regra 981134|
|950009|Ataque de fixação de sessão|
|950003|Fixação de Sessão|
|950000|Fixação de Sessão|
|950005|Tentativa de acesso a ficheiros remotos|
|950002|Acesso ao Comando do Sistema|
|950006|Injeção de comando do sistema|
|959151|Ataque de injeção php|
|958976|Ataque de injeção php|
|958977|Ataque de injeção php|

### <a name="crs41sql"></a>crs_41_sql_injection_attacks

|RuleId|Descrição|
|---|---|
|981231|Sequência de comentários SQL detetada.|
|981260|Codificação de hexéis SQL identificada|
|981320|Ataque de injeção SQL = Nomes comuns de DB detetados|
|981300|Regra 981300|
|981301|Regra 981301|
|981302|Regra 981302|
|981303|Regra 981303|
|981304|Regra 981304|
|981305|Regra 981305|
|981306|Regra 981306|
|981307|Regra 981307|
|981308|Regra 981308|
|981309|Regra 981309|
|981310|Regra 981310|
|981311|Regra 981311|
|981312|Regra 981312|
|981313|Regra 981313|
|981314|Regra 981314|
|981315|Regra 981315|
|981316|Regra 981316|
|981317|Alerta de deteção de anomalias de declaração sql SELECT|
|950007|Ataque de injeção de sql cego|
|950001|Ataque de injeção de SQL|
|950908|Ataque de injeção sql.|
|959073|Ataque de injeção de SQL|
|981272|Deteta testes de sqli cegos utilizando o sono() ou o benchmark().|
|981250|Deteta tentativas de referência sql e injeção de sono, incluindo consultas condicionais|
|981241|Deteta tentativas condicionais de injeção de SQL|
|981276|À procura de uma injeção básica de sql. Corda de ataque comum para o oráculo mistículo e outros.|
|981270|Encontra tentativas básicas de injeção MongoDB SQL|
|981253|Deteta injeções de procedimento/função armazenadas MySQL e PostgreSQL|
|981251|Deteta a injeção da UDF MySQL e outras tentativas de manipulação de dados/estrutura|

### <a name="crs41xss"></a>crs_41_xss_attacks

|RuleId|Descrição|
|---|---|
|973336|Filtro XSS - categoria 1 = Vetor de etiqueta script|
|973338|Filtro XSS - Categoria 3 = Vetor JAVAScript URI|
|981136|Regra 981136|
|981018|Regra 981018|
|958016|Ataque de scripting transversal (XSS)|
|958414|Ataque de scripting transversal (XSS)|
|958032|Ataque de scripting transversal (XSS)|
|958026|Ataque de scripting transversal (XSS)|
|958027|Ataque de scripting transversal (XSS)|
|958054|Ataque de scripting transversal (XSS)|
|958418|Ataque de scripting transversal (XSS)|
|958034|Ataque de scripting transversal (XSS)|
|958019|Ataque de scripting transversal (XSS)|
|958013|Ataque de scripting transversal (XSS)|
|958408|Ataque de scripting transversal (XSS)|
|958012|Ataque de scripting transversal (XSS)|
|958423|Ataque de scripting transversal (XSS)|
|958002|Ataque de scripting transversal (XSS)|
|958017|Ataque de scripting transversal (XSS)|
|958007|Ataque de scripting transversal (XSS)|
|958047|Ataque de scripting transversal (XSS)|
|958410|Ataque de scripting transversal (XSS)|
|958415|Ataque de scripting transversal (XSS)|
|958022|Ataque de scripting transversal (XSS)|
|958405|Ataque de scripting transversal (XSS)|
|958419|Ataque de scripting transversal (XSS)|
|958028|Ataque de scripting transversal (XSS)|
|958057|Ataque de scripting transversal (XSS)|
|958031|Ataque de scripting transversal (XSS)|
|958006|Ataque de scripting transversal (XSS)|
|958033|Ataque de scripting transversal (XSS)|
|958038|Ataque de scripting transversal (XSS)|
|958409|Ataque de scripting transversal (XSS)|
|958001|Ataque de scripting transversal (XSS)|
|958005|Ataque de scripting transversal (XSS)|
|958404|Ataque de scripting transversal (XSS)|
|958023|Ataque de scripting transversal (XSS)|
|958010|Ataque de scripting transversal (XSS)|
|958411|Ataque de scripting transversal (XSS)|
|958422|Ataque de scripting transversal (XSS)|
|958036|Ataque de scripting transversal (XSS)|
|958000|Ataque de scripting transversal (XSS)|
|958018|Ataque de scripting transversal (XSS)|
|958406|Ataque de scripting transversal (XSS)|
|958040|Ataque de scripting transversal (XSS)|
|958052|Ataque de scripting transversal (XSS)|
|958037|Ataque de scripting transversal (XSS)|
|958049|Ataque de scripting transversal (XSS)|
|958030|Ataque de scripting transversal (XSS)|
|958041|Ataque de scripting transversal (XSS)|
|958416|Ataque de scripting transversal (XSS)|
|958024|Ataque de scripting transversal (XSS)|
|958059|Ataque de scripting transversal (XSS)|
|958417|Ataque de scripting transversal (XSS)|
|958020|Ataque de scripting transversal (XSS)|
|958045|Ataque de scripting transversal (XSS)|
|958004|Ataque de scripting transversal (XSS)|
|958421|Ataque de scripting transversal (XSS)|
|958009|Ataque de scripting transversal (XSS)|
|958025|Ataque de scripting transversal (XSS)|
|958413|Ataque de scripting transversal (XSS)|
|958051|Ataque de scripting transversal (XSS)|
|958420|Ataque de scripting transversal (XSS)|
|958407|Ataque de scripting transversal (XSS)|
|958056|Ataque de scripting transversal (XSS)|
|958011|Ataque de scripting transversal (XSS)|
|958412|Ataque de scripting transversal (XSS)|
|958008|Ataque de scripting transversal (XSS)|
|958046|Ataque de scripting transversal (XSS)|
|958039|Ataque de scripting transversal (XSS)|
|958003|Ataque de scripting transversal (XSS)|
|973300|Possível ataque XSS detetado - MANIPULADOR DE EtiquetaHTML|
|973301|Ataque XSS detetado|
|973302|Ataque XSS detetado|
|973303|Ataque XSS detetado|
|973304|Ataque XSS detetado|
|973305|Ataque XSS detetado|
|973306|Ataque XSS detetado|
|973307|Ataque XSS detetado|
|973308|Ataque XSS detetado|
|973309|Ataque XSS detetado|
|973311|Ataque XSS detetado|
|973313|Ataque XSS detetado|
|973314|Ataque XSS detetado|
|973331|Filtros IE XSS - Ataque detetado.|
|973315|Filtros IE XSS - Ataque detetado.|
|973330|Filtros IE XSS - Ataque detetado.|
|973327|Filtros IE XSS - Ataque detetado.|
|973326|Filtros IE XSS - Ataque detetado.|
|973346|Filtros IE XSS - Ataque detetado.|
|973345|Filtros IE XSS - Ataque detetado.|
|973324|Filtros IE XSS - Ataque detetado.|
|973323|Filtros IE XSS - Ataque detetado.|
|973348|Filtros IE XSS - Ataque detetado.|
|973321|Filtros IE XSS - Ataque detetado.|
|973320|Filtros IE XSS - Ataque detetado.|
|973318|Filtros IE XSS - Ataque detetado.|
|973317|Filtros IE XSS - Ataque detetado.|
|973329|Filtros IE XSS - Ataque detetado.|
|973328|Filtros IE XSS - Ataque detetado.|

### <a name="crs42"></a>crs_42_tight_security

|RuleId|Descrição|
|---|---|
|950103|Ataque de caminho traverso|

### <a name="crs45"></a>crs_45_trojans

|RuleId|Descrição|
|---|---|
|950110|Acesso à porta dos fundos|
|950921|Acesso à porta dos fundos|
|950922|Acesso à porta dos fundos|

---

## <a name="next-steps"></a>Passos seguintes

- [Personalize as regras de firewall de aplicação web usando o portal Azure](application-gateway-customize-waf-rules-portal.md)
