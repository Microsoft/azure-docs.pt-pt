---
title: Regra de firewall de aplicações do Azure web de Gateway de aplicação CRS, grupos e regras
description: Esta página fornece informações sobre grupos de regras CRS do web application firewall e regras.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/11/2019
ms.author: victorh
ms.openlocfilehash: 0ad5cc76c0f4631fd60eea7d0a57e4740b6a9db3
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523924"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Grupos de regras CRS do Web application firewall e regras

Firewall de aplicações da web de Gateway de aplicação (WAF) protege aplicações web contra vulnerabilidades mais comuns e exploits. Isso é feito por meio de regras que estão definidas com base em conjuntos de regras do núcleo OWASP 3.0 ou 2.2.9. Estas regras podem ser desativadas numa base de regra por regra. Este artigo contém as regras atuais e os rulesets oferecidos.

Os seguintes grupos de regras e as regras estão disponíveis ao utilizar o Gateway de aplicação com firewall de aplicações web.

# <a name="owasp-30tabowasp3"></a>[OWASP 3.0](#tab/owasp3)

## <a name="owasp30"></a> Conjuntos de regras

### <a name="General"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Limite de sem correspondência com várias partes possíveis.|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|911100|Método não é permitido pela política|


### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Descrição|
|---|---|
|913100|Foi encontrado o agente de utilizador associado com o Verificador de segurança|
|913110|Foi encontrado o cabeçalho do pedido associado com o Verificador de segurança|
|913120|Foi encontrado um pedido. o nome de ficheiro/argumento associado com o Verificador de segurança|
|913101|Foi encontrado o agente de utilizador associado com o cliente HTTP de script/genérico|
|913102|Foi encontrado o agente de utilizador associado à web crawler/bot|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de solicitação de HTTP inválido|
|920130|Falha ao analisar o corpo do pedido.|
|920140|Corpo do pedido com várias partes de falha de validação rigoroso|
|920160|Cabeçalho de HTTP de conteúdo-comprimento não é numérico.|
|920170|O pedido GET ou HEAD com conteúdo do corpo.|
|920180|Pedido POST em falta o cabeçalho Content-Length.|
|920190|Intervalo = o último valor de Byte inválido.|
|920210|Em conflito vários/foram encontrados dados de cabeçalho de ligação.|
|920220|Tentativa de ataque de abuso codificação do URL|
|920240|Tentativa de ataque de abuso codificação do URL|
|920250|UTF8 Tentativa de ataque de abuso de codificação|
|920260|Tentativa de ataque de abuso do Unicode completo/meia largura|
|920270|Caráter inválido no pedido (caractere null)|
|920280|Pedido em falta um cabeçalho de anfitrião|
|920290|Cabeçalho de anfitrião está vazio|
|920310|O pedido tem vazio aceitar cabeçalho|
|920311|O pedido tem vazio aceitar cabeçalho|
|920330|Cabeçalho do agente de utilizador vazia|
|920340|Pedir que contém conteúdo mas o cabeçalho Content-Type em falta|
|920350|Cabeçalho de anfitrião é um endereço IP numérico|
|920380|Demasiados argumentos no pedido|
|920360|Nome do argumento é demasiado longo|
|920370|Valor do argumento é demasiado longo|
|920390|Foi excedido o tamanho total de argumentos|
|920400|Tamanho do ficheiro carregado é demasiado grande|
|920410|Tamanho total de ficheiros carregados demasiado grande|
|920420|Tipo de conteúdo do pedido não é permitido pela política|
|920430|Versão do protocolo HTTP não é permitida pela política|
|920440|Extensão de ficheiro de URL é restrita pela diretiva|
|920450|Cabeçalho de HTTP é restrita pela diretiva (%@{MATCHED_VAR})|
|920200|Intervalo = demasiados muitos campos (6 ou mais)|
|920201|Intervalo = demasiados muitos campos para o pedido de pdf (35 ou mais)|
|920230|Codificação do URL vários detetado|
|920300|Pedido em falta um cabeçalho de aceitar|
|920271|Caráter inválido no pedido (carateres não imprimíveis)|
|920320|Cabeçalho do agente de utilizador em falta|
|920272|Caráter inválido no pedido (fora imprimíveis carateres abaixo ascii 127)|
|920202|Intervalo = demasiados muitos campos para o pedido de pdf (6 ou mais)|
|920273|Caráter inválido no pedido (fora do conjunto muito rigoroso)|
|920274|Caráter inválido nos cabeçalhos de pedido (fora do conjunto muito rigoroso)|
|920460|Caracteres de escape anormal|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921100|Ataque de contrabando de pedidos de HTTP.|
|921110|Ataque de contrabando de pedidos de HTTP|
|921120|Ataque de divisão de resposta de HTTP|
|921130|Ataque de divisão de resposta de HTTP|
|921140|Ataque de Injeção de cabeçalho HTTP por meio de cabeçalhos|
|921150|Ataque de Injeção de cabeçalho HTTP por meio de payload (CR/LF detetado)|
|921160|Ataque de Injeção de cabeçalho HTTP por meio de payload (CR/LF e nome de cabeçalho detetado)|
|921151|Ataque de Injeção de cabeçalho HTTP por meio de payload (CR/LF detetado)|
|921170|Poluição de parâmetro de HTTP|
|921180|Poluição de parâmetro de HTTP (% @{TX.1})|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque transversal do caminho (/... /)|
|930110|Ataque transversal do caminho (/... /)|
|930120|Tentativa de acesso do ficheiro de sistema operacional|
|930130|Tentativa de acesso do ficheiro restritas|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível ataque de inclusão de ficheiros remota (RFI) = o parâmetro de URL com o endereço IP|
|931110|Possível ataque de inclusão de ficheiros remota (RFI) = RFI vulnerável parâmetro nome comum utilizado w/URL de Payload|
|931120|Ataque de inclusão (RFI) de arquivos remoto possíveis URL Payload utilizado/à direita w pergunta Mark caractere = (?)|
|931130|Ataque de inclusão (RFI) de arquivos remoto possíveis = desativar domínio/ligação de referência|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Descrição|
|---|---|
|932120|A execução do comando remoto = encontrado de comando do Windows PowerShell|
|932130|A execução do comando remoto = expressão de Shell de Unix encontrado|
|932140|A execução do comando remoto = Windows para / se o comando encontrado|
|932160|A execução do comando remoto = código do Shell de Unix encontrado|
|932170|A execução do comando remoto = Shellshock (CVE-2014-6271)|
|932171|A execução do comando remoto = Shellshock (CVE-2014-6271)|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de Injeção PHP = Tag de abertura/fechamento encontrado|
|933110|Ataque de Injeção PHP = o carregamento de ficheiros de Script PHP encontrado|
|933120|Ataque de Injeção PHP = diretiva de configuração encontrada|
|933130|Ataque de Injeção PHP = variáveis encontradas|
|933150|Ataque de Injeção PHP = o nome da função de alto risco PHP encontrado|
|933160|Ataque de Injeção PHP = chamada de função de alto risco PHP encontrada|
|933180|Ataque de Injeção PHP = chamada de função variáveis encontrada|
|933151|Ataque de Injeção PHP = o nome da função de médio risco PHP encontrado|
|933131|Ataque de Injeção PHP = variáveis encontradas|
|933161|Ataque de Injeção PHP = chamada de função PHP de baixo valor encontrada|
|933111|Ataque de Injeção PHP = o carregamento de ficheiros de Script PHP encontrado|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataques de XSS detetado via libinjection|
|941110|Filtro XSS - categoria 1 = Vetor de marca de Script|
|941130|Filtro XSS - categoria 3 = Vetor de atributo|
|941140|Filtro XSS - categoria 4 = Vetor de URI de Javascript|
|941150|Filtro XSS - categoria 5 = os atributos de HTML não permitido|
|941180|Palavras-chave da lista de bloqueios de validador de nó|
|941190|XSS com folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando Javascript oculta|
|941220|XSS usando VBScript oculto|
|941230|XSS usando 'incorporar"etiqueta|
|941240|XSS usando o atributo "Importar" ou "implementação"|
|941260|XSS usando a marca 'meta'|
|941270|XSS usando "link" href|
|941280|XSS usando a marca 'base'|
|941290|XSS usando a marca de "miniaplicativo"|
|941300|XSS usando marca "objeto"|
|941310|US-ASCII com formato incorreto codificação Filtro XSS - ataque detetado.|
|941330|Filtros de IE XSS - ataque detetado.|
|941340|Filtros de IE XSS - ataque detetado.|
|941350|UTF-7 codificação IE XSS - ataque detetado.|
|941320|Ataques XSS possível detetada - processador de HTML Tag|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|SQL detetado de ataque de Injeção por meio de libinjection|
|942110|Ataque de Injeção de SQL: Teste detetado de Injeção de comuns|
|942130|Ataque de Injeção de SQL: Tautology SQL detetado.|
|942140|Ataque de Injeção de SQL = nomes comuns da DB detetados|
|942160|Deteta os testes de sqli cego usando sleep() ou benchmark().|
|942170|Deteta tentativas de injeção de SQL benchmark e suspensão incluindo consultas condicionais|
|942190|Deteta a execução de código MSSQL e tentativas de recolha de informações|
|942200|Deteta MySQL comentário- / oculto de espaço de injeções e terminação de acento grave|
|942230|Deteta tentativas de injeção de SQL condicionais|
|942260|Deteta a omissão de autenticação básica do SQL tenta 2/3|
|942270|À procura de injeção de sql básica. Cadeia de ataque comuns para o mysql oracle e outras pessoas.|
|942290|Localiza tentativas de injeção de SQL básica do MongoDB|
|942300|Deteta o MySQL comentários, condições e injeções ch (a) r|
|942320|Deteta o MySQL e PostgreSQL armazenados injeções de procedimento/função|
|942330|Deteta clássico probings de injeção de SQL 1/2|
|942340|Deteta a omissão de autenticação básica do SQL tenta 3/3|
|942350|Deteta a injeção de UDF do MySQL e outra manipulação de dados/estrutura tenta|
|942360|Deteta concatenada injeção de SQL básica e tentativas SQLLFI|
|942370|Deteta clássico probings de injeção de SQL 2/2|
|942150|Ataque de Injeção de SQL|
|942410|Ataque de Injeção de SQL|
|942430|Restrito a deteção de anomalias de caráter do SQL (args): n. º de carateres especiais excedido (12)|
|942440|Sequência de comentário do SQL detetada.|
|942450|SQL hexadecimal codificação identificados|
|942251|Deteta injeções HAVING|
|942460|Alerta de deteção de anomalias de meta-caractere - repetitivas não seja uma palavra carateres|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Ataque de fixação de sessão possível = definir valores de Cookie em HTML|
|943110|Ataque de fixação de sessão possível = nome do parâmetro de SessionID com Referenciador de fora do domínio|
|943120|Ataque de fixação de sessão possível = nome do parâmetro de SessionID com nenhuma Referenciador|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a> Conjuntos de regras

### <a name="crs20"></a> crs_20_protocol_violations

|RuleId|Descrição|
|---|---|
|960911|Linha de solicitação de HTTP inválido|
|981227|Erro de Apache = URI inválido no pedido.|
|960912|Falha ao analisar o corpo do pedido.|
|960914|Corpo do pedido com várias partes de falha de validação rigoroso|
|960915|Com várias partes parser detetou um limite sem correspondência possível.|
|960016|Cabeçalho de HTTP de conteúdo-comprimento não é numérico.|
|960011|O pedido GET ou HEAD com conteúdo do corpo.|
|960012|Pedido POST em falta o cabeçalho Content-Length.|
|960902|Utilização inválida de codificação de identidade.|
|960022|Espere cabeçalho não é permitido para HTTP 1.0.|
|960020|Cabeçalho Pragma requer o cabeçalho Cache-Control para pedidos HTTP/1.1.|
|958291|Intervalo = campo existe e que começa com 0.|
|958230|Intervalo = o último valor de Byte inválido.|
|958295|Em conflito vários/foram encontrados dados de cabeçalho de ligação.|
|950107|Tentativa de ataque de abuso codificação do URL|
|950109|Codificação do URL vários detetado|
|950108|Tentativa de ataque de abuso codificação do URL|
|950801|UTF8 Tentativa de ataque de abuso de codificação|
|950116|Tentativa de ataque de abuso do Unicode completo/meia largura|
|960901|Caráter inválido no pedido|
|960018|Caráter inválido no pedido|

### <a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Descrição|
|---|---|
|960008|Pedido em falta um cabeçalho de anfitrião|
|960007|Cabeçalho de anfitrião está vazio|
|960015|Pedido em falta um cabeçalho de aceitar|
|960021|O pedido tem vazio aceitar cabeçalho|
|960009|Pedido em falta um cabeçalho do agente de utilizador|
|960006|Cabeçalho do agente de utilizador vazia|
|960904|Pedir que contém conteúdo mas o cabeçalho Content-Type em falta|
|960017|Cabeçalho de anfitrião é um endereço IP numérico|

### <a name="crs23"></a> crs_23_request_limits

|RuleId|Descrição|
|---|---|
|960209|Nome do argumento é demasiado longo|
|960208|Valor do argumento é demasiado longo|
|960335|Demasiados argumentos no pedido|
|960341|Foi excedido o tamanho total de argumentos|
|960342|Tamanho do ficheiro carregado é demasiado grande|
|960343|Tamanho total de ficheiros carregados demasiado grande|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Descrição|
|---|---|
|960032|Método não é permitido pela política|
|960010|Tipo de conteúdo do pedido não é permitido pela política|
|960034|Versão do protocolo HTTP não é permitida pela política|
|960035|Extensão de ficheiro de URL é restrita pela diretiva|
|960038|Cabeçalho de HTTP é restrita pela diretiva|

### <a name="crs35"></a> crs_35_bad_robots

|RuleId|Descrição|
|---|---|
|990002|Pedido indica que um verificador de segurança analisados o Site|
|990901|Pedido indica que um verificador de segurança analisados o Site|
|990902|Pedido indica que um verificador de segurança analisados o Site|
|990012|Crawler de web site de adesão|

### <a name="crs40"></a> crs_40_generic_attacks

|RuleId|Descrição|
|---|---|
|960024|Alerta de deteção de anomalias de meta-caractere - repetitivas não seja uma palavra carateres|
|950008|Injeção de etiquetas do ColdFusion não documentado|
|950010|Ataque de Injeção de LDAP|
|950011|Ataque de injeção de SSI|
|950018|URL de XSS de PDF Universal detetado.|
|950019|Ataque de Injeção de e-mail|
|950012|Ataque de contrabando de pedidos de HTTP.|
|950910|Ataque de divisão de resposta de HTTP|
|950911|Ataque de divisão de resposta de HTTP|
|950117|Ataque de inclusão de ficheiros remota|
|950118|Ataque de inclusão de ficheiros remota|
|950119|Ataque de inclusão de ficheiros remota|
|950120|Ataque de inclusão (RFI) de arquivos remoto possíveis = desativar domínio/ligação de referência|
|981133|Regra 981133|
|981134|Regra 981134|
|950009|Ataque de fixação de sessão|
|950003|Fixação de sessão|
|950000|Fixação de sessão|
|950005|Tentativa de acesso do ficheiro remoto|
|950002|Acesso ao sistema de comando|
|950006|Injeção de comandos de sistema|
|959151|Ataque de Injeção PHP|
|958976|Ataque de Injeção PHP|
|958977|Ataque de Injeção PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Descrição|
|---|---|
|981231|Sequência de comentário do SQL detetada.|
|981260|SQL hexadecimal codificação identificados|
|981320|Ataque de Injeção de SQL = nomes comuns da DB detetados|
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
|981317|Alerta de deteção de anomalias de instrução SELECT SQL|
|950007|Ataque de Injeção de SQL cega|
|950001|Ataque de Injeção de SQL|
|950908|Ataque de Injeção de SQL.|
|959073|Ataque de Injeção de SQL|
|981272|Deteta os testes de sqli cego usando sleep() ou benchmark().|
|981250|Deteta tentativas de injeção de SQL benchmark e suspensão incluindo consultas condicionais|
|981241|Deteta tentativas de injeção de SQL condicionais|
|981276|À procura de injeção de sql básica. Cadeia de ataque comuns para o mysql oracle e outras pessoas.|
|981270|Localiza tentativas de injeção de SQL básica do MongoDB|
|981253|Deteta o MySQL e PostgreSQL armazenados injeções de procedimento/função|
|981251|Deteta a injeção de UDF do MySQL e outra manipulação de dados/estrutura tenta|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Descrição|
|---|---|
|973336|Filtro XSS - categoria 1 = Vetor de marca de Script|
|973338|Filtro XSS - categoria 3 = Vetor de URI de Javascript|
|981136|Regra 981136|
|981018|Regra 981018|
|958016|Ataque de criação de scripts entre sites (XSS)|
|958414|Ataque de criação de scripts entre sites (XSS)|
|958032|Ataque de criação de scripts entre sites (XSS)|
|958026|Ataque de criação de scripts entre sites (XSS)|
|958027|Ataque de criação de scripts entre sites (XSS)|
|958054|Ataque de criação de scripts entre sites (XSS)|
|958418|Ataque de criação de scripts entre sites (XSS)|
|958034|Ataque de criação de scripts entre sites (XSS)|
|958019|Ataque de criação de scripts entre sites (XSS)|
|958013|Ataque de criação de scripts entre sites (XSS)|
|958408|Ataque de criação de scripts entre sites (XSS)|
|958012|Ataque de criação de scripts entre sites (XSS)|
|958423|Ataque de criação de scripts entre sites (XSS)|
|958002|Ataque de criação de scripts entre sites (XSS)|
|958017|Ataque de criação de scripts entre sites (XSS)|
|958007|Ataque de criação de scripts entre sites (XSS)|
|958047|Ataque de criação de scripts entre sites (XSS)|
|958410|Ataque de criação de scripts entre sites (XSS)|
|958415|Ataque de criação de scripts entre sites (XSS)|
|958022|Ataque de criação de scripts entre sites (XSS)|
|958405|Ataque de criação de scripts entre sites (XSS)|
|958419|Ataque de criação de scripts entre sites (XSS)|
|958028|Ataque de criação de scripts entre sites (XSS)|
|958057|Ataque de criação de scripts entre sites (XSS)|
|958031|Ataque de criação de scripts entre sites (XSS)|
|958006|Ataque de criação de scripts entre sites (XSS)|
|958033|Ataque de criação de scripts entre sites (XSS)|
|958038|Ataque de criação de scripts entre sites (XSS)|
|958409|Ataque de criação de scripts entre sites (XSS)|
|958001|Ataque de criação de scripts entre sites (XSS)|
|958005|Ataque de criação de scripts entre sites (XSS)|
|958404|Ataque de criação de scripts entre sites (XSS)|
|958023|Ataque de criação de scripts entre sites (XSS)|
|958010|Ataque de criação de scripts entre sites (XSS)|
|958411|Ataque de criação de scripts entre sites (XSS)|
|958422|Ataque de criação de scripts entre sites (XSS)|
|958036|Ataque de criação de scripts entre sites (XSS)|
|958000|Ataque de criação de scripts entre sites (XSS)|
|958018|Ataque de criação de scripts entre sites (XSS)|
|958406|Ataque de criação de scripts entre sites (XSS)|
|958040|Ataque de criação de scripts entre sites (XSS)|
|958052|Ataque de criação de scripts entre sites (XSS)|
|958037|Ataque de criação de scripts entre sites (XSS)|
|958049|Ataque de criação de scripts entre sites (XSS)|
|958030|Ataque de criação de scripts entre sites (XSS)|
|958041|Ataque de criação de scripts entre sites (XSS)|
|958416|Ataque de criação de scripts entre sites (XSS)|
|958024|Ataque de criação de scripts entre sites (XSS)|
|958059|Ataque de criação de scripts entre sites (XSS)|
|958417|Ataque de criação de scripts entre sites (XSS)|
|958020|Ataque de criação de scripts entre sites (XSS)|
|958045|Ataque de criação de scripts entre sites (XSS)|
|958004|Ataque de criação de scripts entre sites (XSS)|
|958421|Ataque de criação de scripts entre sites (XSS)|
|958009|Ataque de criação de scripts entre sites (XSS)|
|958025|Ataque de criação de scripts entre sites (XSS)|
|958413|Ataque de criação de scripts entre sites (XSS)|
|958051|Ataque de criação de scripts entre sites (XSS)|
|958420|Ataque de criação de scripts entre sites (XSS)|
|958407|Ataque de criação de scripts entre sites (XSS)|
|958056|Ataque de criação de scripts entre sites (XSS)|
|958011|Ataque de criação de scripts entre sites (XSS)|
|958412|Ataque de criação de scripts entre sites (XSS)|
|958008|Ataque de criação de scripts entre sites (XSS)|
|958046|Ataque de criação de scripts entre sites (XSS)|
|958039|Ataque de criação de scripts entre sites (XSS)|
|958003|Ataque de criação de scripts entre sites (XSS)|
|973300|Ataques XSS possível detetada - processador de HTML Tag|
|973301|Ataques XSS detetado|
|973302|Ataques XSS detetado|
|973303|Ataques XSS detetado|
|973304|Ataques XSS detetado|
|973305|Ataques XSS detetado|
|973306|Ataques XSS detetado|
|973307|Ataques XSS detetado|
|973308|Ataques XSS detetado|
|973309|Ataques XSS detetado|
|973311|Ataques XSS detetado|
|973313|Ataques XSS detetado|
|973314|Ataques XSS detetado|
|973331|Filtros de IE XSS - ataque detetado.|
|973315|Filtros de IE XSS - ataque detetado.|
|973330|Filtros de IE XSS - ataque detetado.|
|973327|Filtros de IE XSS - ataque detetado.|
|973326|Filtros de IE XSS - ataque detetado.|
|973346|Filtros de IE XSS - ataque detetado.|
|973345|Filtros de IE XSS - ataque detetado.|
|973324|Filtros de IE XSS - ataque detetado.|
|973323|Filtros de IE XSS - ataque detetado.|
|973348|Filtros de IE XSS - ataque detetado.|
|973321|Filtros de IE XSS - ataque detetado.|
|973320|Filtros de IE XSS - ataque detetado.|
|973318|Filtros de IE XSS - ataque detetado.|
|973317|Filtros de IE XSS - ataque detetado.|
|973329|Filtros de IE XSS - ataque detetado.|
|973328|Filtros de IE XSS - ataque detetado.|

### <a name="crs42"></a> crs_42_tight_security

|RuleId|Descrição|
|---|---|
|950103|Caminho de ataque transversal|

### <a name="crs45"></a> crs_45_trojans

|RuleId|Descrição|
|---|---|
|950110|Acesso Backdoor|
|950921|Acesso Backdoor|
|950922|Acesso Backdoor|

---

## <a name="next-steps"></a>Passos Seguintes

Saiba como desativar regras WAF: [Personalizar regras de WAF](application-gateway-customize-waf-rules-portal.md)