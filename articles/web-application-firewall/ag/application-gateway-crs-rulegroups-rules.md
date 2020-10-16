---
title: Grupos e regras de regras do CRS
titleSuffix: Azure Web Application Firewall
description: Esta página fornece informações sobre grupos e regras de regras de insise regras de crs de firewall de aplicações web.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 37e09612491d41887c5945920488569d3620bf0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85052013"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Grupos e regras de regras de CRS de firewall de aplicação web

A firewall da aplicação web Do Gateway (WAF) protege as aplicações web de vulnerabilidades e explorações comuns. Isto é feito através de regras definidas com base na regra principal da OWASP define 3.1, 3.0 ou 2.2.9. Estas regras podem ser desativadas por regra. Este artigo contém as regras e regras atuais oferecidas.

## <a name="core-rule-sets"></a>Conjuntos de regras principais

O Gateway WAF de aplicação vem pré-configurado com CRS 3.0 por padrão. Mas pode optar por utilizar o CRS 3.1 ou o CRS 2.2.9. O CRS 3.1 oferece novos conjuntos de regras que defendem contra infeções de Java, um conjunto inicial de verificações de upload de ficheiros, falsos positivos fixos, e muito mais. O CRS 3.0 oferece positivos falsos reduzidos em comparação com o CRS 2.2.9. Também pode [personalizar regras de acordo com as suas necessidades.](application-gateway-customize-waf-rules-portal.md)

> [!div class="mx-imgBorder"]
> ![Gere regras](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

A WAF protege contra as seguintes vulnerabilidades web:

- Ataques de injeção DE SQL
- Ataques de scripts de sites cruzados
- Outros ataques comuns, tais como injeção de comando, pedido HTTP contrabando, divisão de resposta HTTP e inclusão de ficheiros remotos
- Violações do protocolo HTTP
- Anomalias do protocolo HTTP, tais como falta de agente de utilizadores do hospedeiro e aceitar cabeçalhos
- Bots, crawlers e scanners
- Configurações comuns de aplicações (por exemplo, Apache e IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3.1

O CRS 3.1 inclui 13 grupos de regras, como mostra a tabela seguinte. Cada grupo contém várias regras, que podem ser desativadas.

> [!NOTE]
> O CRS 3.1 só está disponível no WAF_v2 SKU.

|Grupo de regras|Descrição|
|---|---|
|**[Geral](#general-31)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Proteger contra scanners portuários e ambientais|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Proteger contra protocolos e questões de codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Proteger contra injeção de cabeçalho, solicitar contrabando e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Proteja contra ataques de ficheiros e caminhos|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Proteger contra ataques remotos de inclusão de ficheiros (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Proteja novamente ataques remotos de execução de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Proteger contra ataques de injeção php|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Proteja contra ataques de scripts trans-locais|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Proteger contra ataques de injeção DE SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Proteja contra ataques de fixação de sessão|
|**[PEDIDO-944-APLICAÇÃO-ATTACK-SESSION-JAVA](#crs944-31)**|Proteja contra ataques java|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

O CRS 3.0 inclui 12 grupos de regras, como mostra a tabela seguinte. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[Geral](#general-30)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Proteger contra scanners portuários e ambientais|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Proteger contra protocolos e questões de codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Proteger contra injeção de cabeçalho, solicitar contrabando e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Proteja contra ataques de ficheiros e caminhos|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Proteger contra ataques remotos de inclusão de ficheiros (RFI)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Proteja novamente ataques remotos de execução de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Proteger contra ataques de injeção php|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Proteja contra ataques de scripts trans-locais|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Proteger contra ataques de injeção DE SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Proteja contra ataques de fixação de sessão|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

O CRS 2.2.9 inclui 10 grupos de regras, como mostra o quadro seguinte. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Proteja contra violações de protocolos (tais como caracteres inválidos ou um GET com um corpo de pedido)|
|**[crs_21_protocol_anomalies](#crs21)**|Proteja contra informações incorretas do cabeçalho|
|**[crs_23_request_limits](#crs23)**|Proteger contra argumentos ou ficheiros que excedam limitações|
|**[crs_30_http_policy](#crs30)**|Proteger contra métodos restritos, cabeçalhos e tipos de ficheiros|
|**[crs_35_bad_robots](#crs35)**|Proteja contra web crawlers e scanners|
|**[crs_40_generic_attacks](#crs40)**|Proteger contra ataques genéricos (como fixação de sessão, inclusão de ficheiros remotos e injeção de PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Proteger contra ataques de injeção DE SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Proteja contra ataques de scripts trans-locais|
|**[crs_42_tight_security](#crs42)**|Proteja-se contra ataques de travessia de caminhos|
|**[crs_45_trojans](#crs45)**|Proteja contra cavalos de Troia backdoor|

Os seguintes grupos de regras e regras estão disponíveis quando se utilizam firewall de aplicação web no Gateway de aplicações.

# <a name="owasp-31"></a>[OWASP 3.1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Conjuntos de regras

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Possível Fronteira Incomparável Multipart.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado User-Agent associado ao scanner de segurança|
|913101|Encontrado User-Agent associados a script/genérico cliente HTTP|
|913102|Encontrado User-Agent associado com web crawler/bot|
|913110|Cabeçalho de pedido encontrado associado ao scanner de segurança|
|913120|Encontrado pedido filename/argumento associado ao scanner de segurança|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de pedido inválida HTTP|
|920120|Desvio multiparte/dados de formulários tentado|
|920121|Desvio multiparte/dados de formulários tentado|
|920130|Falhou em analisar o corpo do pedido.|
|920140|Corpo de pedido multipart falhou validação estrita|
|920160|O cabeçalho HTTP de comprimento de conteúdo não é numérico.|
|920170|PEDIDO GET ou HEAD com conteúdo corporal.|
|920171|Pedido GET ou HEAD com transferência-codificação.|
|920180|PEDIDO DE CORREIO Falta cabeçalho de comprimento de conteúdo.|
|920190|Alcance = Valor Inválido Último Byte.|
|920200|Alcance = Demasiados campos (6 ou mais)|
|920201|Gama = Demasiados campos para pedido pdf (35 ou mais)|
|920202|Gama = Demasiados campos para pedido pdf (6 ou mais)|
|920210|Dados de cabeçalho de ligação múltipla/conflituosa encontrados.|
|920220|URL Codificando tentativa de ataque de abuso|
|920230|Codificação de URL múltipla detetada|
|920240|URL Codificando tentativa de ataque de abuso|
|920250|UTF8 codificando tentativa de ataque de abuso|
|920260|Tentativa de ataque de abuso de largura total/meia|
|920270|Caráter inválido a pedido (carácter nulo)|
|920271|Caráter inválido a pedido (caracteres não imprimíveis)|
|920272|Caráter inválido a pedido (fora dos chars imprimíveis abaixo ascii 127)|
|920273|Caráter inválido a pedido (fora do conjunto muito rigoroso)|
|920274|Caráter inválido nos cabeçalhos de pedido (fora do conjunto muito rigoroso)|
|920280|Pedido faltando um cabeçalho anfitrião|
|920290|Cabeçalho de anfitrião vazio|
|920300|Pedido faltando um cabeçalho de aceitação|
|920310|Pedido tem um cabeçalho de aceitação vazio|
|920311|Pedido tem um cabeçalho de aceitação vazio|
|920320|Cabeçalho do agente de utilizador desaparecido|
|920330|Cabeçalho vazio do agente de utilizador|
|920340|Pedido contendo conteúdo mas cabeçalho de tipo de conteúdo em falta|
|920341|Pedido que contenha conteúdo requer cabeçalho de tipo de conteúdo|
|920350|Cabeçalho anfitrião é um endereço IP numérico|
|920360|Nome do argumento muito longo|
|920370|Valor do argumento demasiado longo|
|920380|Demasiados argumentos a pedido|
|920390|Tamanho total dos argumentos excedido|
|920400|Tamanho do ficheiro carregado muito grande|
|920410|Tamanho total de ficheiros carregados demasiado grande|
|920420|O tipo de conteúdo de pedido não é permitido pela política|
|920430|A versão do protocolo HTTP não é permitida pela política|
|920440|A extensão do ficheiro URL é restrita pela política|
|920450|O cabeçalho HTTP é restringido por política (%@{MATCHED_VAR})|
|920460|Personagens de fuga anormais|
|920470|Cabeçalho tipo conteúdo ilegal|
|920480|Restringir o parâmetro do conjunto de charset dentro do cabeçalho do tipo de conteúdo|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921110|HTTP Solicitação contrabando ataque|
|921120|HTTP Resposta Ataque de Divisão|
|921130|HTTP Resposta Ataque de Divisão|
|921140|ATAQUE DE Injeção de Cabeçalho HTTP através de cabeçalhos|
|921150|HTTP Ataque de injeção de cabeçalho através de carga útil (CR/LF detetado)|
|921151|HTTP Ataque de injeção de cabeçalho através de carga útil (CR/LF detetado)|
|921160|HTTP Ataque de injeção de cabeçalho através de carga útil (CR/LF e nome do cabeçalho detetado)|
|921170|Poluição por Parâmetros HTTP|
|921180|Poluição por parâmetros HTTP (%{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque Traversal do Caminho (/.. /)|
|930110|Ataque Traversal do Caminho (/.. /)|
|930120|Tentativa de Acesso a Ficheiros osS|
|930130|Tentativa restrita de acesso a ficheiros|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível Inclusão de Ficheiros Remotos (RFI) Ataque = Parâmetro URL usando endereço IP|
|931110|Possível inclusão de ficheiros remotos (RFI) Attack = Nome comum do parâmetro vulnerável da RFI usado c/URL Payload|
|931120|Possível Inclusão de Ficheiros Remotos (RFI) Ataque = Carga de PAGAMENTO DE URL Usada c/Trailing Question Mark Character (?)|
|931130|Possível inclusão de ficheiros remotos (RFI) Ataque = referência Off-Domain/link|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932100|Execução de comando remoto: Injeção de comando unix|
|932105|Execução de comando remoto: Injeção de comando unix|
|932106|Execução de comando remoto: Injeção de comando unix|
|932110|Execução de comando remoto: Injeção de comando do Windows|
|932115|Execução de comando remoto: Injeção de comando do Windows|
|932120|Execução de comando remoto = comando de powershell do Windows encontrado|
|932130|Execução de comando remoto = expressão unix shell encontrada|
|932140|Execução de comando remoto = comando Windows FOR/IF Encontrado|
|932150|Execução de Comando Remoto: Execução de Comando Unix Direto|
|932160|Execução de comando remoto = código de concha unix encontrado|
|932170|Execução de Comando Remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de Comando Remoto = Shellshock (CVE-2014-6271)|
|932180|Tentativa restrita de upload de ficheiros|
|932190|Execução de comando remoto: tentativa técnica de bypass wildcard|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção PHP = Abertura/Etiqueta de fecho Encontrada|
|933110|Ataque de injeção PHP = upload de ficheiro de script PHP encontrado|
|933111|Ataque de injeção PHP: Upload de ficheiro de script PHP encontrado|
|933120|Ataque de injeção PHP = Diretiva de configuração encontrada|
|933130|Ataque de injeção php = variáveis encontradas|
|933131|Ataque de injeção php: variáveis encontradas|
|933140|Ataque de injeção php: fluxo de I/O encontrado|
|933150|Ataque de injeção PHP = High-Risk nome da função PHP encontrado|
|933151|Ataque de injeção PHP: Medium-Risk nome da função PHP encontrado|
|933160|Ataque de injeção PHP = High-Risk chamada de função PHP encontrada|
|933161|Ataque de injeção PHP: Low-Value chamada de função PHP encontrada|
|933170|Ataque de injeção php: injeção de objeto serializado|
|933180|Ataque de injeção PHP = Chamada de Função Variável Encontrada|
|933190|Ataque de injeção PHP: Tag de fecho PHP encontrada|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque XSS Detetado por libinjecção|
|941101|Ataque XSS Detetado por libinjecção|
|941110|Filtro XSS - Categoria 1 = Vetor de etiquetas de script|
|941130|Filtro XSS - Categoria 3 = Vetor de atributos|
|941140|Filtro XSS - Categoria 4 = Vetor URI javascript|
|941150|Filtro XSS - Categoria 5 = Atributos HTML não permitidos|
|941160|Verificador de injeção NoScript XSS: Injeção HTML|
|941170|Verificador de injeção noScript XSS: Injeção de atributo|
|941180|Node-Validator palavras-chave da lista negra|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando Javascript obfuscado|
|941220|XSS usando script VB obfuscado|
|941230|XSS usando tag 'embed'|
|941240|XSS usando atributo de "importação" ou "implementação"|
|941250|Filtros IE XSS - Ataque detetado|
|941260|XSS usando tag 'meta'|
|941270|XSS usando 'link' href|
|941280|XSS usando tag 'base'|
|941290|XSS usando tag 'applet'|
|941300|XSS usando a etiqueta 'objeto'|
|941310|Us-ASCII Malforme Codificação Filtro XSS - Ataque detetado.|
|941320|Possível ataque XSS detetado - MANIPULADOR de tag HTML|
|941330|Filtros IE XSS - Ataque detetado.|
|941340|Filtros IE XSS - Ataque detetado.|
|941350|UTF-7 Codificação IE XSS - Ataque Detetado.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção SQL detetado por libinjecção|
|942110|Ataque de injeção SQL: Testes comuns de injeção detetados|
|942120|Ataque de injeção SQL: Operador SQL detetado|
|942130|Ataque de injeção SQL: Tautologia SQL Detetada.|
|942140|Sql Injection Attack = Nomes DB comuns detetados|
|942150|Ataque de injeção DE SQL|
|942160|Deteta testes de sqli cegos utilizando o sono() ou benchmark().|
|942170|Deteta tentativas de referência de SQL e de injeção de sono, incluindo consultas condicionais|
|942180|Deteta tentativas básicas de bypass de autenticação SQL 1/3|
|942190|Deteta tentativas de execução de código MSSQL e de recolha de informações|
|942200|Deteta o comentário mySQL/injeções obfustadas pelo espaço e a rescisão de retrocesso|
|942210|Deteta tentativas de injeção de SQL acorrentadas 1/2|
|942220|À procura de ataques inteiros de transbordamento, estes são retirados de peixes-peixe, exceto 3.0.00738585072|
|942230|Deteta tentativas de injeção de SQL condicionais|
|942240|Deteta o interruptor de charset MySQL e as tentativas do DOS MSSQL|
|942250|Deteta injeções DE JOGO CONTRA, FUSÃO E EXECUÇÃO IMEDIATA|
|942251|Deteta injeções DE TER|
|942260|Deteta tentativas básicas de bypass de autenticação SQL 2/3|
|942270|À procura de uma injeção básica de sql. Corda de ataque comum para oráculo mysql e outros|
|942280|Deteta a injeção de pg_sleep postgres, aguarda por ataques de atraso e tentativas de encerramento de bases de dados|
|942290|Encontra tentativas básicas de injeção mongoDB SQL|
|942300|Deteta comentários, condições e injeções de mySQL|
|942310|Deteta tentativas de injeção de SQL acorrentadas 2/2|
|942320|Deteta injeções de procedimento/função armazenados no MySQL e postgresQL|
|942330|Deteta sondas clássicas de injeção DE SQL 1/2|
|942340|Deteta tentativas básicas de bypass de autenticação SQL 3/3|
|942350|Deteta a injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Deteta a injeção básica de SQL e as tentativas de SQLLFI concatenated|
|942361|Deteta a injeção básica de SQL com base no alter ou união de palavras-chave|
|942370|Deteta sondas clássicas de injeção DE SQL 2/2|
|942380|Ataque de injeção DE SQL|
|942390|Ataque de injeção DE SQL|
|942400|Ataque de injeção DE SQL|
|942410|Ataque de injeção DE SQL|
|942420|Deteção restrita de anomalias de caracteres SQL (cookies): # de caracteres especiais excedidos (8)|
|942421|Deteção restrita de anomalias de caracteres SQL (cookies): # de caracteres especiais excedidos (3)|
|942430|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12)|
|942431|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (6)|
|942432|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (2)|
|942440|Sequência de comentários SQL detetada.|
|942450|Codificação de hex SQL identificada|
|942460|Meta-Character Alerta de Deteção de Anomalias - Personagens repetitivos não-palavra|
|942470|Ataque de injeção DE SQL|
|942480|Ataque de injeção DE SQL|
|942490|Deteta sondas clássicas de injeção DE SQL 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação de sessão = Definição de valores de cookies em HTML|
|943110|Possível ataque de fixação de sessão = nome do parâmetro sessionID com Off-Domain referrer|
|943120|Possível ataque de fixação de sessão = Nome do parâmetro SessionID sem referrante|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">PEDIDO-944-APLICAÇÃO-ATTACK-SESSION-JAVA</p>

|RuleId|Descrição|
|---|---|
|944120|Possível execução de carga útil e execução de comando remoto|
|944130|Aulas suspeitas de Java|
|944200|Exploração da deserialização de Java Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Conjuntos de regras

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Possível Fronteira Incomparável Multipart.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado User-Agent associado ao scanner de segurança|
|913110|Cabeçalho de pedido encontrado associado ao scanner de segurança|
|913120|Encontrado pedido filename/argumento associado ao scanner de segurança|
|913101|Encontrado User-Agent associados a script/genérico cliente HTTP|
|913102|Encontrado User-Agent associado com web crawler/bot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de pedido inválida HTTP|
|920130|Falhou em analisar o corpo do pedido.|
|920140|Corpo de pedido multipart falhou validação estrita|
|920160|O cabeçalho HTTP de comprimento de conteúdo não é numérico.|
|920170|PEDIDO GET ou HEAD com conteúdo corporal.|
|920180|PEDIDO DE CORREIO Falta cabeçalho de comprimento de conteúdo.|
|920190|Alcance = Valor Inválido Último Byte.|
|920210|Dados de cabeçalho de ligação múltipla/conflituosa encontrados.|
|920220|URL Codificando tentativa de ataque de abuso|
|920240|URL Codificando tentativa de ataque de abuso|
|920250|UTF8 codificando tentativa de ataque de abuso|
|920260|Tentativa de ataque de abuso de largura total/meia|
|920270|Caráter inválido a pedido (carácter nulo)|
|920280|Pedido faltando um cabeçalho anfitrião|
|920290|Cabeçalho de anfitrião vazio|
|920310|Pedido tem um cabeçalho de aceitação vazio|
|920311|Pedido tem um cabeçalho de aceitação vazio|
|920330|Cabeçalho vazio do agente de utilizador|
|920340|Pedido contendo conteúdo mas cabeçalho de tipo de conteúdo em falta|
|920350|Cabeçalho anfitrião é um endereço IP numérico|
|920380|Demasiados argumentos a pedido|
|920360|Nome do argumento muito longo|
|920370|Valor do argumento demasiado longo|
|920390|Tamanho total dos argumentos excedido|
|920400|Tamanho do ficheiro carregado muito grande|
|920410|Tamanho total de ficheiros carregados demasiado grande|
|920420|O tipo de conteúdo de pedido não é permitido pela política|
|920430|A versão do protocolo HTTP não é permitida pela política|
|920440|A extensão do ficheiro URL é restrita pela política|
|920450|O cabeçalho HTTP é restringido por política (%@{MATCHED_VAR})|
|920200|Alcance = Demasiados campos (6 ou mais)|
|920201|Gama = Demasiados campos para pedido pdf (35 ou mais)|
|920230|Codificação de URL múltipla detetada|
|920300|Pedido faltando um cabeçalho de aceitação|
|920271|Caráter inválido a pedido (caracteres não imprimíveis)|
|920320|Cabeçalho do agente de utilizador desaparecido|
|920272|Caráter inválido a pedido (fora dos chars imprimíveis abaixo ascii 127)|
|920202|Gama = Demasiados campos para pedido pdf (6 ou mais)|
|920273|Caráter inválido a pedido (fora do conjunto muito rigoroso)|
|920274|Caráter inválido nos cabeçalhos de pedido (fora do conjunto muito rigoroso)|
|920460|Personagens de fuga anormais|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921100|HTTP Solicitação de Ataque de Contrabando.|
|921110|HTTP Solicitação contrabando ataque|
|921120|HTTP Resposta Ataque de Divisão|
|921130|HTTP Resposta Ataque de Divisão|
|921140|ATAQUE DE Injeção de Cabeçalho HTTP através de cabeçalhos|
|921150|HTTP Ataque de injeção de cabeçalho através de carga útil (CR/LF detetado)|
|921160|HTTP Ataque de injeção de cabeçalho através de carga útil (CR/LF e nome do cabeçalho detetado)|
|921151|HTTP Ataque de injeção de cabeçalho através de carga útil (CR/LF detetado)|
|921170|Poluição por Parâmetros HTTP|
|921180|Poluição por parâmetros HTTP (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque Traversal do Caminho (/.. /)|
|930110|Ataque Traversal do Caminho (/.. /)|
|930120|Tentativa de Acesso a Ficheiros osS|
|930130|Tentativa restrita de acesso a ficheiros|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível Inclusão de Ficheiros Remotos (RFI) Ataque = Parâmetro URL usando endereço IP|
|931110|Possível inclusão de ficheiros remotos (RFI) Attack = Nome comum do parâmetro vulnerável da RFI usado c/URL Payload|
|931120|Possível Inclusão de Ficheiros Remotos (RFI) Ataque = Carga de PAGAMENTO DE URL Usada c/Trailing Question Mark Character (?)|
|931130|Possível inclusão de ficheiros remotos (RFI) Ataque = referência Off-Domain/link|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932120|Execução de comando remoto = comando de powershell do Windows encontrado|
|932130|Execução de comando remoto = expressão unix shell encontrada|
|932140|Execução de comando remoto = comando Windows FOR/IF Encontrado|
|932160|Execução de comando remoto = código de concha unix encontrado|
|932170|Execução de Comando Remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de Comando Remoto = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção PHP = Abertura/Etiqueta de fecho Encontrada|
|933110|Ataque de injeção PHP = upload de ficheiro de script PHP encontrado|
|933120|Ataque de injeção PHP = Diretiva de configuração encontrada|
|933130|Ataque de injeção php = variáveis encontradas|
|933150|Ataque de injeção PHP = High-Risk nome da função PHP encontrado|
|933160|Ataque de injeção PHP = High-Risk chamada de função PHP encontrada|
|933180|Ataque de injeção PHP = Chamada de Função Variável Encontrada|
|933151|Ataque de injeção PHP = Medium-Risk nome da função PHP encontrado|
|933131|Ataque de injeção php = variáveis encontradas|
|933161|Ataque de injeção PHP = Low-Value chamada de função PHP encontrada|
|933111|Ataque de injeção PHP = upload de ficheiro de script PHP encontrado|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque XSS Detetado por libinjecção|
|941110|Filtro XSS - Categoria 1 = Vetor de etiquetas de script|
|941130|Filtro XSS - Categoria 3 = Vetor de atributos|
|941140|Filtro XSS - Categoria 4 = Vetor URI javascript|
|941150|Filtro XSS - Categoria 5 = Atributos HTML não permitidos|
|941180|Node-Validator palavras-chave da lista negra|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando Javascript obfuscado|
|941220|XSS usando script VB obfuscado|
|941230|XSS usando tag 'embed'|
|941240|XSS usando atributo de "importação" ou "implementação"|
|941260|XSS usando tag 'meta'|
|941270|XSS usando 'link' href|
|941280|XSS usando tag 'base'|
|941290|XSS usando tag 'applet'|
|941300|XSS usando a etiqueta 'objeto'|
|941310|Us-ASCII Malforme Codificação Filtro XSS - Ataque detetado.|
|941330|Filtros IE XSS - Ataque detetado.|
|941340|Filtros IE XSS - Ataque detetado.|
|941350|UTF-7 Codificação IE XSS - Ataque Detetado.|
|941320|Possível ataque XSS detetado - MANIPULADOR de tag HTML|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção SQL detetado por libinjecção|
|942110|Ataque de injeção SQL: Testes comuns de injeção detetados|
|942130|Ataque de injeção SQL: Tautologia SQL Detetada.|
|942140|Sql Injection Attack = Nomes DB comuns detetados|
|942160|Deteta testes de sqli cegos utilizando o sono() ou benchmark().|
|942170|Deteta tentativas de referência de SQL e de injeção de sono, incluindo consultas condicionais|
|942190|Deteta tentativas de execução de código MSSQL e de recolha de informações|
|942200|Deteta o comentário mySQL/injeções obfustadas pelo espaço e a rescisão de retrocesso|
|942230|Deteta tentativas de injeção de SQL condicionais|
|942260|Deteta tentativas básicas de bypass de autenticação SQL 2/3|
|942270|À procura de uma injeção básica de sql. Corda de ataque comum para oráculo mysql e outros.|
|942290|Encontra tentativas básicas de injeção mongoDB SQL|
|942300|Deteta comentários, condições e injeções de mySQL|
|942310|Deteta tentativas de injeção de SQL acorrentadas 2/2|
|942320|Deteta injeções de procedimento/função armazenados no MySQL e postgresQL|
|942330|Deteta sondas clássicas de injeção DE SQL 1/2|
|942340|Deteta tentativas básicas de bypass de autenticação SQL 3/3|
|942350|Deteta a injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Deteta a injeção básica de SQL e as tentativas de SQLLFI concatenated|
|942370|Deteta sondas clássicas de injeção DE SQL 2/2|
|942150|Ataque de injeção DE SQL|
|942410|Ataque de injeção DE SQL|
|942430|Deteção restrita de anomalias de caracteres SQL (args): # de caracteres especiais excedidos (12)|
|942440|Sequência de comentários SQL detetada.|
|942450|Codificação de hex SQL identificada|
|942251|Deteta injeções DE TER|
|942460|Meta-Character Alerta de Deteção de Anomalias - Personagens repetitivos não-palavra|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação de sessão = Definição de valores de cookies em HTML|
|943110|Possível ataque de fixação de sessão = nome do parâmetro sessionID com Off-Domain referrer|
|943120|Possível ataque de fixação de sessão = Nome do parâmetro SessionID sem referrante|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Conjuntos de regras

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Descrição|
|---|---|
|960911|Linha de pedido inválida HTTP|
|981227|Erro Apache = URI Inválido no Pedido.|
|960912|Falhou em analisar o corpo do pedido.|
|960914|Corpo de pedido multipart falhou validação estrita|
|960915|O parser multipart detetou um possível limite incomparável.|
|960016|O cabeçalho HTTP de comprimento de conteúdo não é numérico.|
|960011|PEDIDO GET ou HEAD com conteúdo corporal.|
|960012|PEDIDO DE CORREIO Falta cabeçalho de comprimento de conteúdo.|
|960902|Utilização inválida da codificação de identidade.|
|960022|Espere cabeçalho não permitido para HTTP 1.0.|
|960020|O Cabeçalho pragma requer Cache-Control Header para pedidos HTTP/1.1.|
|958291|Alcance = campo existe e começa com 0.|
|958230|Alcance = Valor Inválido Último Byte.|
|958295|Dados de cabeçalho de ligação múltipla/conflituosa encontrados.|
|950107|URL Codificando tentativa de ataque de abuso|
|950109|Codificação de URL múltipla detetada|
|950108|URL Codificando tentativa de ataque de abuso|
|950801|UTF8 codificando tentativa de ataque de abuso|
|950116|Tentativa de ataque de abuso de largura total/meia|
|960901|Caráter inválido a pedido|
|960018|Caráter inválido a pedido|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Descrição|
|---|---|
|960008|Pedido faltando um cabeçalho anfitrião|
|960007|Cabeçalho de anfitrião vazio|
|960015|Pedido faltando um cabeçalho de aceitação|
|960021|Pedido tem um cabeçalho de aceitação vazio|
|960009|Pedido faltando um cabeçalho de agente de utilizador|
|960006|Cabeçalho vazio do agente de utilizador|
|960904|Pedido contendo conteúdo mas cabeçalho de tipo de conteúdo em falta|
|960017|Cabeçalho anfitrião é um endereço IP numérico|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Descrição|
|---|---|
|960209|Nome do argumento muito longo|
|960208|Valor do argumento demasiado longo|
|960335|Demasiados argumentos a pedido|
|960341|Tamanho total dos argumentos excedido|
|960342|Tamanho do ficheiro carregado muito grande|
|960343|Tamanho total de ficheiros carregados demasiado grande|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Descrição|
|---|---|
|960032|O método não é permitido pela política|
|960010|O tipo de conteúdo de pedido não é permitido pela política|
|960034|A versão do protocolo HTTP não é permitida pela política|
|960035|A extensão do ficheiro URL é restrita pela política|
|960038|O cabeçalho HTTP é restringido pela política|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Descrição|
|---|---|
|990002|Pedido indica um scanner de segurança digitalizado no site|
|990901|Pedido indica um scanner de segurança digitalizado no site|
|990902|Pedido indica um scanner de segurança digitalizado no site|
|990012|Crawler de site fraudulento|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Descrição|
|---|---|
|960024|Meta-Character Alerta de Deteção de Anomalias - Personagens repetitivos não-palavra|
|950008|Injeção de Marcas de Fusão De Frio Não Documentada|
|950010|Ataque de injeção de LDAP|
|950011|Ataque de injeção de SSI|
|950018|URL PDF XSS Universal detetado.|
|950019|Ataque de injeção de e-mail|
|950012|HTTP Solicitação de Ataque de Contrabando.|
|950910|HTTP Resposta Ataque de Divisão|
|950911|HTTP Resposta Ataque de Divisão|
|950117|Ataque remoto de inclusão de ficheiros|
|950118|Ataque remoto de inclusão de ficheiros|
|950119|Ataque remoto de inclusão de ficheiros|
|950120|Possível inclusão de ficheiros remotos (RFI) Ataque = referência Off-Domain/link|
|981133|Regra 981133|
|981134|Regra 981134|
|950009|Ataque de fixação de sessão|
|950003|Fixação da Sessão|
|950000|Fixação da Sessão|
|950005|Tentativa de acesso a ficheiros remotos|
|950002|Acesso ao Comando do Sistema|
|950006|Injeção de Comando do Sistema|
|959151|Ataque de injeção php|
|958976|Ataque de injeção php|
|958977|Ataque de injeção php|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Descrição|
|---|---|
|981231|Sequência de comentários SQL detetada.|
|981260|Codificação de hex SQL identificada|
|981320|Sql Injection Attack = Nomes DB comuns detetados|
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
|981317|Alerta de deteção de anomalia seletiva SQL|
|950007|Ataque de injeção de SQL cego|
|950001|Ataque de injeção DE SQL|
|950908|Ataque de injeção SQL.|
|959073|Ataque de injeção DE SQL|
|981272|Deteta testes de sqli cegos utilizando o sono() ou benchmark().|
|981250|Deteta tentativas de referência de SQL e de injeção de sono, incluindo consultas condicionais|
|981241|Deteta tentativas de injeção de SQL condicionais|
|981276|À procura de uma injeção básica de sql. Corda de ataque comum para oráculo mysql e outros.|
|981270|Encontra tentativas básicas de injeção mongoDB SQL|
|981253|Deteta injeções de procedimento/função armazenados no MySQL e postgresQL|
|981251|Deteta a injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Descrição|
|---|---|
|973336|Filtro XSS - Categoria 1 = Vetor de etiquetas de script|
|973338|Filtro XSS - Categoria 3 = Vetor URI javascript|
|981136|Regra 981136|
|981018|Regra 981018|
|958016|Ataque de Scripting Cross-Site (XSS)|
|958414|Ataque de Scripting Cross-Site (XSS)|
|958032|Ataque de Scripting Cross-Site (XSS)|
|958026|Ataque de Scripting Cross-Site (XSS)|
|958027|Ataque de Scripting Cross-Site (XSS)|
|958054|Ataque de Scripting Cross-Site (XSS)|
|958418|Ataque de Scripting Cross-Site (XSS)|
|958034|Ataque de Scripting Cross-Site (XSS)|
|958019|Ataque de Scripting Cross-Site (XSS)|
|958013|Ataque de Scripting Cross-Site (XSS)|
|958408|Ataque de Scripting Cross-Site (XSS)|
|958012|Ataque de Scripting Cross-Site (XSS)|
|958423|Ataque de Scripting Cross-Site (XSS)|
|958002|Ataque de Scripting Cross-Site (XSS)|
|958017|Ataque de Scripting Cross-Site (XSS)|
|958007|Ataque de Scripting Cross-Site (XSS)|
|958047|Ataque de Scripting Cross-Site (XSS)|
|958410|Ataque de Scripting Cross-Site (XSS)|
|958415|Ataque de Scripting Cross-Site (XSS)|
|958022|Ataque de Scripting Cross-Site (XSS)|
|958405|Ataque de Scripting Cross-Site (XSS)|
|958419|Ataque de Scripting Cross-Site (XSS)|
|958028|Ataque de Scripting Cross-Site (XSS)|
|958057|Ataque de Scripting Cross-Site (XSS)|
|958031|Ataque de Scripting Cross-Site (XSS)|
|958006|Ataque de Scripting Cross-Site (XSS)|
|958033|Ataque de Scripting Cross-Site (XSS)|
|958038|Ataque de Scripting Cross-Site (XSS)|
|958409|Ataque de Scripting Cross-Site (XSS)|
|958001|Ataque de Scripting Cross-Site (XSS)|
|958005|Ataque de Scripting Cross-Site (XSS)|
|958404|Ataque de Scripting Cross-Site (XSS)|
|958023|Ataque de Scripting Cross-Site (XSS)|
|958010|Ataque de Scripting Cross-Site (XSS)|
|958411|Ataque de Scripting Cross-Site (XSS)|
|958422|Ataque de Scripting Cross-Site (XSS)|
|958036|Ataque de Scripting Cross-Site (XSS)|
|958000|Ataque de Scripting Cross-Site (XSS)|
|958018|Ataque de Scripting Cross-Site (XSS)|
|958406|Ataque de Scripting Cross-Site (XSS)|
|958040|Ataque de Scripting Cross-Site (XSS)|
|958052|Ataque de Scripting Cross-Site (XSS)|
|958037|Ataque de Scripting Cross-Site (XSS)|
|958049|Ataque de Scripting Cross-Site (XSS)|
|958030|Ataque de Scripting Cross-Site (XSS)|
|958041|Ataque de Scripting Cross-Site (XSS)|
|958416|Ataque de Scripting Cross-Site (XSS)|
|958024|Ataque de Scripting Cross-Site (XSS)|
|958059|Ataque de Scripting Cross-Site (XSS)|
|958417|Ataque de Scripting Cross-Site (XSS)|
|958020|Ataque de Scripting Cross-Site (XSS)|
|958045|Ataque de Scripting Cross-Site (XSS)|
|958004|Ataque de Scripting Cross-Site (XSS)|
|958421|Ataque de Scripting Cross-Site (XSS)|
|958009|Ataque de Scripting Cross-Site (XSS)|
|958025|Ataque de Scripting Cross-Site (XSS)|
|958413|Ataque de Scripting Cross-Site (XSS)|
|958051|Ataque de Scripting Cross-Site (XSS)|
|958420|Ataque de Scripting Cross-Site (XSS)|
|958407|Ataque de Scripting Cross-Site (XSS)|
|958056|Ataque de Scripting Cross-Site (XSS)|
|958011|Ataque de Scripting Cross-Site (XSS)|
|958412|Ataque de Scripting Cross-Site (XSS)|
|958008|Ataque de Scripting Cross-Site (XSS)|
|958046|Ataque de Scripting Cross-Site (XSS)|
|958039|Ataque de Scripting Cross-Site (XSS)|
|958003|Ataque de Scripting Cross-Site (XSS)|
|973300|Possível ataque XSS detetado - MANIPULADOR de tag HTML|
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

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Descrição|
|---|---|
|950103|Ataque Traversal do Caminho|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Descrição|
|---|---|
|950110|Acesso backdoor|
|950921|Acesso backdoor|
|950922|Acesso backdoor|

---

## <a name="next-steps"></a>Passos seguintes

- [Personalize as regras de Firewall de aplicações web usando o portal Azure](application-gateway-customize-waf-rules-portal.md)
