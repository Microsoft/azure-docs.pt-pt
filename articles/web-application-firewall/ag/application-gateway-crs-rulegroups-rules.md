---
title: Regras e grupos de regras do CRS
titleSuffix: Azure Web Application Firewall
description: Esta página fornece informações sobre regras e grupos de regras CRS do firewall do aplicativo Web.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 0303f09e5e704a18576bf50d1f00007f7f86f320
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075364"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Regras e grupos de regras CRS do firewall do aplicativo Web

O WAF (firewall do aplicativo Web) do gateway de aplicativo protege aplicativos Web contra vulnerabilidades e explorações comuns. Isso é feito por meio de regras que são definidas com base nos conjuntos de regras do OWASP Core 3,1, 3,0 ou 2.2.9. Essas regras podem ser desabilitadas com base em regra por regra. Este artigo contém as regras atuais e os conjuntos de regras oferecidos.

## <a name="core-rule-sets"></a>Conjuntos de regras principais

O WAF do gateway de aplicativo vem pré-configurado com o CRS 3,0 por padrão. Mas você pode optar por usar CRS 3,1 ou CRS 2.2.9 em vez disso. O CRS 3,1 oferece novos conjuntos de regras que se defendem contra infecções de Java, um conjunto inicial de verificações de upload de arquivo, falsos positivos fixos e muito mais. O CRS 3,0 oferece redução de falsos positivos em comparação com o CRS 2.2.9. Você também pode [Personalizar regras para atender às suas necessidades](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![gerencia regras](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

O WAF protege contra as seguintes vulnerabilidades da Web:

- Ataques de injeção de SQL
- Ataques de script entre sites
- Outros ataques comuns, como injeção de comando, indesejada de solicitação HTTP, divisão de resposta HTTP e inclusão de arquivo remoto
- Violações de protocolo HTTP
- Anomalias do protocolo HTTP, como o agente de usuário do host ausente e os cabeçalhos de aceitação
- Bots, rastreadores e scanners
- Configurações incorretas de aplicativo comuns (por exemplo, Apache e IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3,1

O CRS 3,1 inclui 13 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|Descrição|
|---|---|
|**[Genéricos](#general-31)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Proteger contra scanners de porta e de ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Proteger contra problemas de protocolo e codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Proteger contra injeção de cabeçalho, indesejada de solicitação e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Proteger contra ataques de arquivo e caminho|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Proteger contra ataques de RFI (inclusão de arquivo remoto)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Proteger novamente os ataques de execução remota de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Proteger contra ataques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Proteger contra ataques de injeção de SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Proteger contra ataques de fixação da de sessão|
|**[SOLICITAÇÃO-944-APLICATIVO-ATAQUE-SESSÃO-JAVA](#crs944-31)**|Proteger contra ataques JAVA|

### <a name="owasp-crs-30"></a>OWASP CRS 3.0

O CRS 3,0 inclui 12 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|Descrição|
|---|---|
|**[Genéricos](#general-30)**|Grupo geral|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Métodos de bloqueio (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Proteger contra scanners de porta e de ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Proteger contra problemas de protocolo e codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Proteger contra injeção de cabeçalho, indesejada de solicitação e divisão de resposta|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Proteger contra ataques de arquivo e caminho|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Proteger contra ataques de RFI (inclusão de arquivo remoto)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Proteger novamente os ataques de execução remota de código|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Proteger contra ataques de injeção de PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Proteger contra ataques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Proteger contra ataques de injeção de SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Proteger contra ataques de fixação da de sessão|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

O CRS 2.2.9 inclui 10 grupos de regras, conforme mostrado na tabela a seguir. Cada grupo contém várias regras, que podem ser desabilitadas.

|Grupo de regras|Descrição|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Proteger contra violações de protocolo (como caracteres inválidos ou GET com um corpo de solicitação)|
|**[crs_21_protocol_anomalies](#crs21)**|Proteger contra informações de cabeçalho incorretas|
|**[crs_23_request_limits](#crs23)**|Proteger contra argumentos ou arquivos que excedem as limitações|
|**[crs_30_http_policy](#crs30)**|Proteger contra métodos, cabeçalhos e tipos de arquivos restritos|
|**[crs_35_bad_robots](#crs35)**|Proteger contra rastreadores e scanners da Web|
|**[crs_40_generic_attacks](#crs40)**|Proteger contra ataques genéricos (como fixação da de sessão, inclusão de arquivo remoto e injeção de PHP)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Proteger contra ataques de injeção de SQL|
|**[crs_41_xss_attacks](#crs41xss)**|Proteger contra ataques de script entre sites|
|**[crs_42_tight_security](#crs42)**|Proteger contra ataques de percurso de caminho|
|**[crs_45_trojans](#crs45)**|Proteger contra cavalos de Tróia de Backdoor|

As regras e os grupos de regras a seguir estão disponíveis ao usar o Firewall do aplicativo Web no gateway de aplicativo.

# <a name="owasp-31tabowasp31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="owasp31"></a>Conjuntos de regras

### <a name="general-31"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Limite de várias partes sem correspondência possível.|

### <a name="crs911-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-911-MÉTODO-IMPOSIÇÃO</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="crs913-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-913-VERIFICAÇÃO DE DETECÇÃO</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado agente de usuário associado ao verificador de segurança|
|913101|Encontrado agente de usuário associado ao cliente HTTP genérico/script|
|913102|Encontrado agente de usuário associado ao rastreador/bot da Web|
|913110|Cabeçalho de solicitação encontrado associado ao verificador de segurança|
|913120|Encontrado nome de arquivo/argumento de solicitação associado ao verificador de segurança|


### <a name="crs920-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-920-PROTOCOLO-IMPOSIÇÃO</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de solicitação HTTP inválida|
|920120|Tentativa de ignorar dados de várias partes/formulário|
|920121|Tentativa de ignorar dados de várias partes/formulário|
|920130|Falha ao analisar o corpo da solicitação.|
|920140|Falha na validação estrita do corpo da solicitação de várias partes|
|920160|O cabeçalho HTTP Content-Length não é numérico.|
|920170|Solicitação GET ou HEAD com conteúdo do corpo.|
|920171|Solicitação GET ou HEAD com codificação de transferência.|
|920180|O cabeçalho Content-Length está faltando na solicitação POST.|
|920190|Intervalo = valor do último byte inválido.|
|920200|Intervalo = muitos campos (6 ou mais)|
|920201|Intervalo = muitos campos para a solicitação de PDF (35 ou mais)|
|920202|Intervalo = muitos campos para a solicitação de PDF (6 ou mais)|
|920210|Dados de cabeçalho de conexão múltiplos/conflitantes encontrados.|
|920220|Tentativa de ataque de abuso de codificação de URL|
|920230|Codificação de URL múltipla detectada|
|920240|Tentativa de ataque de abuso de codificação de URL|
|920250|Tentativa de ataque de abuso de codificação UTF8|
|920260|Tentativa de ataque de abuso de meia largura e total de Unicode|
|920270|Caractere inválido na solicitação (caractere nulo)|
|920271|Caractere inválido na solicitação (caracteres não imprimíveis)|
|920272|Caractere inválido na solicitação (fora dos caracteres imprimíveis abaixo do ASCII 127)|
|920273|Caractere inválido na solicitação (fora do conjunto muito estrito)|
|920274|Caractere inválido nos cabeçalhos de solicitação (fora do conjunto muito estrito)|
|920280|Solicitação ausente de cabeçalho de host|
|920290|Cabeçalho de host vazio|
|920300|Solicitar um cabeçalho Accept ausente|
|920310|A solicitação tem um cabeçalho Accept vazio|
|920311|A solicitação tem um cabeçalho Accept vazio|
|920320|Cabeçalho de agente do usuário ausente|
|920330|Cabeçalho de agente do usuário vazio|
|920340|Solicitação contendo conteúdo, mas cabeçalho Content-Type ausente|
|920341|Solicitação contendo conteúdo requer cabeçalho Content-Type|
|920350|O cabeçalho de host é um endereço IP numérico|
|920360|Nome de argumento muito longo|
|920370|Valor de argumento muito longo|
|920380|Muitos argumentos na solicitação|
|920390|Tamanho total de argumentos excedido|
|920400|Tamanho de arquivo carregado muito grande|
|920410|Tamanho total de arquivos carregados muito grande|
|920420|O tipo de conteúdo da solicitação não é permitido pela política|
|920430|A versão do protocolo HTTP não é permitida pela política|
|920440|A extensão do arquivo de URL é restrita pela política|
|920450|O cabeçalho HTTP é restrito pela política (% @ {MATCHED_VAR})|
|920460|Caracteres de escape anormal|
|920470|Cabeçalho Content-Type ilegal|
|920480|Restringir o parâmetro charset dentro do cabeçalho Content-Type|

### <a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921110|Ataque de indesejada de solicitação HTTP|
|921120|Ataque de divisão de resposta HTTP|
|921130|Ataque de divisão de resposta HTTP|
|921140|Ataque de injeção de cabeçalho HTTP por meio de cabeçalhos|
|921150|Ataque de injeção de cabeçalho HTTP via carga (CR/LF detectado)|
|921151|Ataque de injeção de cabeçalho HTTP via carga (CR/LF detectado)|
|921160|Ataque de injeção de cabeçalho HTTP por meio de Payload (CR/LF e Header-Name detectados)|
|921170|Poluição de parâmetro HTTP|
|921180|Poluição de parâmetro HTTP (% {TX. 1})|

### <a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque de percurso de caminho (/.. /)|
|930110|Ataque de percurso de caminho (/.. /)|
|930120|Tentativa de acesso ao arquivo do sistema operacional|
|930130|Tentativa de acesso de arquivo restrito|

### <a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível ataque de inclusão de arquivo remoto (RFI) = parâmetro de URL usando endereço IP|
|931110|Possível ataque de RFI (inclusão de arquivo remoto) = nome de parâmetro vulnerável RFI comum usado com carga de URL|
|931120|Possível ataque de inclusão de arquivo remoto (RFI) = carga de URL usada com caractere de ponto de interrogação à direita (?)|
|931130|Possível ataque de inclusão de arquivo remoto (RFI) = link/referência fora do domínio|

### <a name="crs932-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-932-APLICATIVO-ATAQUE-RCE</p>

|RuleId|Descrição|
|---|---|
|932100|Execução de comando remoto: injeção de comando Unix|
|932105|Execução de comando remoto: injeção de comando Unix|
|932106|Execução de comando remoto: injeção de comando Unix|
|932110|Execução de comando remoto: injeção de comando do Windows|
|932115|Execução de comando remoto: injeção de comando do Windows|
|932120|Execução de comando remoto = comando do Windows PowerShell encontrado|
|932130|Execução de comando remoto = expressão de shell do UNIX encontrada|
|932140|Execução do comando remoto = Windows para/se o comando foi encontrado|
|932160|Execução de comando remoto = código de shell do UNIX encontrado|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932180|Tentativa de carregamento de arquivo restrito|
|932190|Execução de comando remoto: tentativa de técnica de bypass de curinga|

### <a name="crs933-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-933-APLICATIVO-ATAQUE-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção de PHP = marca de abertura/fechamento encontrada|
|933110|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|
|933111|Ataque de injeção de PHP: carregamento de arquivo de script PHP encontrado|
|933120|Ataque de injeção de PHP = diretiva de configuração encontrada|
|933130|Ataque de injeção de PHP = variáveis encontradas|
|933131|Ataque de injeção de PHP: variáveis encontradas|
|933140|Ataque de injeção de PHP: fluxo de e/s encontrado|
|933150|Ataque de injeção de PHP = nome de função PHP de alto risco encontrado|
|933151|Ataque de injeção de PHP: nome de função PHP de médio risco encontrado|
|933160|Ataque de injeção de PHP = chamada de função PHP de alto risco encontrada|
|933161|Ataque de injeção de PHP: chamada de função PHP de valor baixo encontrada|
|933170|Ataque de injeção de PHP: injeção de objeto serializado|
|933180|Ataque de injeção de PHP = chamada de função de variável encontrada|
|933190|Ataque de injeção de PHP: marca de fechamento de PHP encontrada|

### <a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque XSS detectado via libinjection|
|941101|Ataque XSS detectado via libinjection|
|941110|Filtro XSS-categoria 1 = vetor de marca de script|
|941130|Filtro XSS-categoria 3 = vetor de atributo|
|941140|Filtro XSS-categoria 4 = vetor de URI de JavaScript|
|941150|Filtro XSS-categoria 5 = atributos HTML não permitidos|
|941160|NoScript XSS InjectionChecker: injeção de HTML|
|941170|NoScript XSS InjectionChecker: injeção de atributo|
|941180|Palavras-chave da lista de bloqueios do validador de nós|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando JavaScript ofuscado|
|941220|XSS usando script VB ofuscado|
|941230|XSS usando a marca ' embed '|
|941240|XSS usando o atributo ' import ' ou ' Implementation '|
|941250|Filtros XSS do IE – ataque detectado|
|941260|XSS usando a marca ' meta '|
|941270|XSS usando href ' link '|
|941280|XSS usando a marca ' base '|
|941290|XSS usando a marca ' applet '|
|941300|XSS usando a marca ' Object '|
|941310|Filtro XSS de codificação malformado US-ASCII-ataque detectado.|
|941320|Possível ataque de XSS detectado-manipulador de marca HTML|
|941330|Filtros XSS do IE – ataque detectado.|
|941340|Filtros XSS do IE – ataque detectado.|
|941350|Codificação UTF-7 IE XSS-ataque detectado.|


### <a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção de SQL detectado via libinjection|
|942110|Ataque de injeção de SQL: teste de injeção comum detectado|
|942130|Ataque de injeção de SQL: SQL tautology detectado.|
|942140|Ataque de injeção de SQL = nomes comuns de BD detectados|
|942150|Ataque de injeção de SQL|
|942160|Detecta testes sqli cegos usando Sleep () ou benchmark ().|
|942170|Detecta as tentativas de benchmark e de injeção de suspensão do SQL, incluindo consultas condicionais|
|942180|Detecta tentativas de bypass de autenticação SQL básica 1/3|
|942190|Detecta as tentativas de execução de código e coleta de informações do MSSQL|
|942200|Detecta as injeçãos de comentário do MySQL-/Space-Obfuscated e a terminação de tique|
|942210|Detecta tentativas de injeção de SQL encadeadas 1/2|
|942220|Procurando ataques de estouro de inteiros, eles são obtidos de skipfish, exceto 3.0.00738585072|
|942230|Detecta tentativas de injeção de SQL condicional|
|942240|Detecta a opção de conjunto de caracteres MySQL e tentativas do DoS MSSQL|
|942250|Detecta a correspondência em relação a, MESCLAr e executar injeçãos IMEDIATAs|
|942251|Detecta a existência de injeçãos|
|942260|Detecta tentativas de bypass de autenticação SQL básica 2/3|
|942270|Procurando por injeção básica de SQL. Cadeia de caracteres de ataque comum para MySQL Oracle e outros|
|942280|Detecta postgres injeção de pg_sleep, WAITFOR atrasa ataques e tentativas de desligamento de banco de dados|
|942290|Localiza tentativas básicas de injeção SQL do MongoDB|
|942300|Detecta comentários do MySQL, condições e as injeções de p (a) de r|
|942310|Detecta tentativas de injeção de SQL encadeadas 2/2|
|942320|Detecta injeções de função/procedimento armazenado MySQL e PostgreSQL|
|942330|Detecta sondagens de injeção SQL clássicas 1/2|
|942340|Detecta tentativas de bypass de autenticação SQL básica 3/3|
|942350|Detecta a injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Detecta a injeção de SQL básica concatenada e tentativas de SQLLFI|
|942361|Detecta a injeção de SQL básica com base na alteração de palavra-chave ou União|
|942370|Detecta sondagens de injeção SQL clássicas 2/2|
|942380|Ataque de injeção de SQL|
|942390|Ataque de injeção de SQL|
|942400|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942420|Detecção de anomalias de caracteres SQL restrita (cookies): número de caracteres especiais excedido (8)|
|942421|Detecção de anomalias de caracteres SQL restrita (cookies): número de caracteres especiais excedido (3)|
|942430|Detecção de anomalias de caracteres SQL restrita (args): número de caracteres especiais excedido (12)|
|942431|Detecção de anomalias de caracteres SQL restrita (args): número de caracteres especiais excedido (6)|
|942432|Detecção de anomalias de caracteres SQL restrita (args): número de caracteres especiais excedido (2)|
|942440|Sequência de comentário SQL detectada.|
|942450|Codificação hexadecimal do SQL identificada|
|942460|Alerta de detecção de anomalias de metacaracteres – caracteres repetitivos que não são palavras|
|942470|Ataque de injeção de SQL|
|942480|Ataque de injeção de SQL|
|942490|Detecta sondagens de injeção SQL clássicas 3/3|

### <a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação da de sessão = definindo valores de cookie em HTML|
|943110|Possível ataque de fixação da de sessão = nome de parâmetro de SessionID com referenciador fora do domínio|
|943120|Possível ataque de fixação da de sessão = nome de parâmetro de SessionID sem referenciador|

### <a name="crs944-31"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-944-APLICATIVO-ATAQUE-SESSÃO-JAVA</p>

|RuleId|Descrição|
|---|---|
|944120|Execução de carga possível e execução de comando remoto|
|944130|Classes Java suspeitas|
|944200|Exploração da desserialização do Java Apache Commons|

# <a name="owasp-30tabowasp30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="owasp30"></a>Conjuntos de regras

### <a name="general-30"></a> <p x-ms-format-detection="none">Geral</p>

|RuleId|Descrição|
|---|---|
|200004|Limite de várias partes sem correspondência possível.|

### <a name="crs911-30"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-911-MÉTODO-IMPOSIÇÃO</p>

|RuleId|Descrição|
|---|---|
|911100|O método não é permitido pela política|


### <a name="crs913-30"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-913-VERIFICAÇÃO DE DETECÇÃO</p>

|RuleId|Descrição|
|---|---|
|913100|Encontrado agente de usuário associado ao verificador de segurança|
|913110|Cabeçalho de solicitação encontrado associado ao verificador de segurança|
|913120|Encontrado nome de arquivo/argumento de solicitação associado ao verificador de segurança|
|913101|Encontrado agente de usuário associado ao cliente HTTP genérico/script|
|913102|Encontrado agente de usuário associado ao rastreador/bot da Web|

### <a name="crs920-30"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-920-PROTOCOLO-IMPOSIÇÃO</p>

|RuleId|Descrição|
|---|---|
|920100|Linha de solicitação HTTP inválida|
|920130|Falha ao analisar o corpo da solicitação.|
|920140|Falha na validação estrita do corpo da solicitação de várias partes|
|920160|O cabeçalho HTTP Content-Length não é numérico.|
|920170|Solicitação GET ou HEAD com conteúdo do corpo.|
|920180|O cabeçalho Content-Length está faltando na solicitação POST.|
|920190|Intervalo = valor do último byte inválido.|
|920210|Dados de cabeçalho de conexão múltiplos/conflitantes encontrados.|
|920220|Tentativa de ataque de abuso de codificação de URL|
|920240|Tentativa de ataque de abuso de codificação de URL|
|920250|Tentativa de ataque de abuso de codificação UTF8|
|920260|Tentativa de ataque de abuso de meia largura e total de Unicode|
|920270|Caractere inválido na solicitação (caractere nulo)|
|920280|Solicitação ausente de cabeçalho de host|
|920290|Cabeçalho de host vazio|
|920310|A solicitação tem um cabeçalho Accept vazio|
|920311|A solicitação tem um cabeçalho Accept vazio|
|920330|Cabeçalho de agente do usuário vazio|
|920340|Solicitação contendo conteúdo, mas cabeçalho Content-Type ausente|
|920350|O cabeçalho de host é um endereço IP numérico|
|920380|Muitos argumentos na solicitação|
|920360|Nome de argumento muito longo|
|920370|Valor de argumento muito longo|
|920390|Tamanho total de argumentos excedido|
|920400|Tamanho de arquivo carregado muito grande|
|920410|Tamanho total de arquivos carregados muito grande|
|920420|O tipo de conteúdo da solicitação não é permitido pela política|
|920430|A versão do protocolo HTTP não é permitida pela política|
|920440|A extensão do arquivo de URL é restrita pela política|
|920450|O cabeçalho HTTP é restrito pela política (% @ {MATCHED_VAR})|
|920200|Intervalo = muitos campos (6 ou mais)|
|920201|Intervalo = muitos campos para a solicitação de PDF (35 ou mais)|
|920230|Codificação de URL múltipla detectada|
|920300|Solicitar um cabeçalho Accept ausente|
|920271|Caractere inválido na solicitação (caracteres não imprimíveis)|
|920320|Cabeçalho de agente do usuário ausente|
|920272|Caractere inválido na solicitação (fora dos caracteres imprimíveis abaixo do ASCII 127)|
|920202|Intervalo = muitos campos para a solicitação de PDF (6 ou mais)|
|920273|Caractere inválido na solicitação (fora do conjunto muito estrito)|
|920274|Caractere inválido nos cabeçalhos de solicitação (fora do conjunto muito estrito)|
|920460|Caracteres de escape anormal|

### <a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Descrição|
|---|---|
|921100|Ataque de indesejada de solicitação HTTP.|
|921110|Ataque de indesejada de solicitação HTTP|
|921120|Ataque de divisão de resposta HTTP|
|921130|Ataque de divisão de resposta HTTP|
|921140|Ataque de injeção de cabeçalho HTTP por meio de cabeçalhos|
|921150|Ataque de injeção de cabeçalho HTTP via carga (CR/LF detectado)|
|921160|Ataque de injeção de cabeçalho HTTP por meio de Payload (CR/LF e Header-Name detectados)|
|921151|Ataque de injeção de cabeçalho HTTP via carga (CR/LF detectado)|
|921170|Poluição de parâmetro HTTP|
|921180|Poluição de parâmetro HTTP (% @ {TX. 1})|

### <a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Descrição|
|---|---|
|930100|Ataque de percurso de caminho (/.. /)|
|930110|Ataque de percurso de caminho (/.. /)|
|930120|Tentativa de acesso ao arquivo do sistema operacional|
|930130|Tentativa de acesso de arquivo restrito|

### <a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Descrição|
|---|---|
|931100|Possível ataque de inclusão de arquivo remoto (RFI) = parâmetro de URL usando endereço IP|
|931110|Possível ataque de RFI (inclusão de arquivo remoto) = nome de parâmetro vulnerável RFI comum usado com carga de URL|
|931120|Possível ataque de inclusão de arquivo remoto (RFI) = carga de URL usada com caractere de ponto de interrogação à direita (?)|
|931130|Possível ataque de inclusão de arquivo remoto (RFI) = link/referência fora do domínio|

### <a name="crs932-30"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-932-APLICATIVO-ATAQUE-RCE</p>

|RuleId|Descrição|
|---|---|
|932120|Execução de comando remoto = comando do Windows PowerShell encontrado|
|932130|Execução de comando remoto = expressão de shell do UNIX encontrada|
|932140|Execução do comando remoto = Windows para/se o comando foi encontrado|
|932160|Execução de comando remoto = código de shell do UNIX encontrado|
|932170|Execução de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Execução de comando remoto = Shellshock (CVE-2014-6271)|

### <a name="crs933-30"></a> <p x-ms-format-detection="none">SOLICITAÇÃO-933-APLICATIVO-ATAQUE-PHP</p>

|RuleId|Descrição|
|---|---|
|933100|Ataque de injeção de PHP = marca de abertura/fechamento encontrada|
|933110|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|
|933120|Ataque de injeção de PHP = diretiva de configuração encontrada|
|933130|Ataque de injeção de PHP = variáveis encontradas|
|933150|Ataque de injeção de PHP = nome de função PHP de alto risco encontrado|
|933160|Ataque de injeção de PHP = chamada de função PHP de alto risco encontrada|
|933180|Ataque de injeção de PHP = chamada de função de variável encontrada|
|933151|Ataque de injeção de PHP = nome de função PHP de médio risco encontrado|
|933131|Ataque de injeção de PHP = variáveis encontradas|
|933161|Ataque de injeção de PHP = chamada de função PHP de valor baixo encontrada|
|933111|Ataque de injeção de PHP = carregamento de arquivo de script PHP encontrado|

### <a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Descrição|
|---|---|
|941100|Ataque XSS detectado via libinjection|
|941110|Filtro XSS-categoria 1 = vetor de marca de script|
|941130|Filtro XSS-categoria 3 = vetor de atributo|
|941140|Filtro XSS-categoria 4 = vetor de URI de JavaScript|
|941150|Filtro XSS-categoria 5 = atributos HTML não permitidos|
|941180|Palavras-chave da lista de bloqueios do validador de nós|
|941190|XSS usando folhas de estilo|
|941200|XSS usando quadros VML|
|941210|XSS usando JavaScript ofuscado|
|941220|XSS usando script VB ofuscado|
|941230|XSS usando a marca ' embed '|
|941240|XSS usando o atributo ' import ' ou ' Implementation '|
|941260|XSS usando a marca ' meta '|
|941270|XSS usando href ' link '|
|941280|XSS usando a marca ' base '|
|941290|XSS usando a marca ' applet '|
|941300|XSS usando a marca ' Object '|
|941310|Filtro XSS de codificação malformado US-ASCII-ataque detectado.|
|941330|Filtros XSS do IE – ataque detectado.|
|941340|Filtros XSS do IE – ataque detectado.|
|941350|Codificação UTF-7 IE XSS-ataque detectado.|
|941320|Possível ataque de XSS detectado-manipulador de marca HTML|

### <a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Descrição|
|---|---|
|942100|Ataque de injeção de SQL detectado via libinjection|
|942110|Ataque de injeção de SQL: teste de injeção comum detectado|
|942130|Ataque de injeção de SQL: SQL tautology detectado.|
|942140|Ataque de injeção de SQL = nomes comuns de BD detectados|
|942160|Detecta testes sqli cegos usando Sleep () ou benchmark ().|
|942170|Detecta as tentativas de benchmark e de injeção de suspensão do SQL, incluindo consultas condicionais|
|942190|Detecta as tentativas de execução de código e coleta de informações do MSSQL|
|942200|Detecta as injeçãos de comentário do MySQL-/Space-Obfuscated e a terminação de tique|
|942230|Detecta tentativas de injeção de SQL condicional|
|942260|Detecta tentativas de bypass de autenticação SQL básica 2/3|
|942270|Procurando por injeção básica de SQL. Cadeia de caracteres de ataque comum para MySQL Oracle e outros.|
|942290|Localiza tentativas básicas de injeção SQL do MongoDB|
|942300|Detecta comentários do MySQL, condições e as injeções de p (a) de r|
|942310|Detecta tentativas de injeção de SQL encadeadas 2/2|
|942320|Detecta injeções de função/procedimento armazenado MySQL e PostgreSQL|
|942330|Detecta sondagens de injeção SQL clássicas 1/2|
|942340|Detecta tentativas de bypass de autenticação SQL básica 3/3|
|942350|Detecta a injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|
|942360|Detecta a injeção de SQL básica concatenada e tentativas de SQLLFI|
|942370|Detecta sondagens de injeção SQL clássicas 2/2|
|942150|Ataque de injeção de SQL|
|942410|Ataque de injeção de SQL|
|942430|Detecção de anomalias de caracteres SQL restrita (args): número de caracteres especiais excedido (12)|
|942440|Sequência de comentário SQL detectada.|
|942450|Codificação hexadecimal do SQL identificada|
|942251|Detecta a existência de injeçãos|
|942460|Alerta de detecção de anomalias de metacaracteres – caracteres repetitivos que não são palavras|

### <a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Descrição|
|---|---|
|943100|Possível ataque de fixação da de sessão = definindo valores de cookie em HTML|
|943110|Possível ataque de fixação da de sessão = nome de parâmetro de SessionID com referenciador fora do domínio|
|943120|Possível ataque de fixação da de sessão = nome de parâmetro de SessionID sem referenciador|

# <a name="owasp-229tabowasp2"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="owasp229"></a>Conjuntos de regras

### <a name="crs20"></a>crs_20_protocol_violations

|RuleId|Descrição|
|---|---|
|960911|Linha de solicitação HTTP inválida|
|981227|Erro do Apache = URI inválido na solicitação.|
|960912|Falha ao analisar o corpo da solicitação.|
|960914|Falha na validação estrita do corpo da solicitação de várias partes|
|960915|O analisador de várias partes detectou um possível limite não correspondente.|
|960016|O cabeçalho HTTP Content-Length não é numérico.|
|960011|Solicitação GET ou HEAD com conteúdo do corpo.|
|960012|O cabeçalho Content-Length está faltando na solicitação POST.|
|960902|Uso inválido de codificação de identidade.|
|960022|Cabeçalho esperado não permitido para HTTP 1,0.|
|960020|O cabeçalho pragma requer o cabeçalho Cache-Control para solicitações HTTP/1.1.|
|958291|Range = o campo existe e começa com 0.|
|958230|Intervalo = valor do último byte inválido.|
|958295|Dados de cabeçalho de conexão múltiplos/conflitantes encontrados.|
|950107|Tentativa de ataque de abuso de codificação de URL|
|950109|Codificação de URL múltipla detectada|
|950108|Tentativa de ataque de abuso de codificação de URL|
|950801|Tentativa de ataque de abuso de codificação UTF8|
|950116|Tentativa de ataque de abuso de meia largura e total de Unicode|
|960901|Caractere inválido na solicitação|
|960018|Caractere inválido na solicitação|

### <a name="crs21"></a>crs_21_protocol_anomalies

|RuleId|Descrição|
|---|---|
|960008|Solicitação ausente de cabeçalho de host|
|960007|Cabeçalho de host vazio|
|960015|Solicitar um cabeçalho Accept ausente|
|960021|A solicitação tem um cabeçalho Accept vazio|
|960009|Solicitar um cabeçalho de agente do usuário ausente|
|960006|Cabeçalho de agente do usuário vazio|
|960904|Solicitação contendo conteúdo, mas cabeçalho Content-Type ausente|
|960017|O cabeçalho de host é um endereço IP numérico|

### <a name="crs23"></a>crs_23_request_limits

|RuleId|Descrição|
|---|---|
|960209|Nome de argumento muito longo|
|960208|Valor de argumento muito longo|
|960335|Muitos argumentos na solicitação|
|960341|Tamanho total de argumentos excedido|
|960342|Tamanho de arquivo carregado muito grande|
|960343|Tamanho total de arquivos carregados muito grande|

### <a name="crs30"></a> crs_30_http_policy

|RuleId|Descrição|
|---|---|
|960032|O método não é permitido pela política|
|960010|O tipo de conteúdo da solicitação não é permitido pela política|
|960034|A versão do protocolo HTTP não é permitida pela política|
|960035|A extensão do arquivo de URL é restrita pela política|
|960038|O cabeçalho HTTP é restrito pela política|

### <a name="crs35"></a>crs_35_bad_robots

|RuleId|Descrição|
|---|---|
|990002|Solicitação indica que um verificador de segurança verificou o site|
|990901|Solicitação indica que um verificador de segurança verificou o site|
|990902|Solicitação indica que um verificador de segurança verificou o site|
|990012|Rastreador de site não autorizado|

### <a name="crs40"></a>crs_40_generic_attacks

|RuleId|Descrição|
|---|---|
|960024|Alerta de detecção de anomalias de metacaracteres – caracteres repetitivos que não são palavras|
|950008|Injeção de marcas do ColdFusion não documentadas|
|950010|Ataque de injeção de LDAP|
|950011|Ataque de injeção de SSI|
|950018|URL universal do PDF XSS detectada.|
|950019|Ataque de injeção de email|
|950012|Ataque de indesejada de solicitação HTTP.|
|950910|Ataque de divisão de resposta HTTP|
|950911|Ataque de divisão de resposta HTTP|
|950117|Ataque de inclusão de arquivo remoto|
|950118|Ataque de inclusão de arquivo remoto|
|950119|Ataque de inclusão de arquivo remoto|
|950120|Possível ataque de inclusão de arquivo remoto (RFI) = link/referência fora do domínio|
|981133|Regra 981133|
|981134|Regra 981134|
|950009|Ataque de fixação da de sessão|
|950003|Fixação da sessão|
|950000|Fixação da sessão|
|950005|Tentativa de acesso ao arquivo remoto|
|950002|Acesso de comando do sistema|
|950006|Injeção de comando do sistema|
|959151|Ataque de injeção de PHP|
|958976|Ataque de injeção de PHP|
|958977|Ataque de injeção de PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Descrição|
|---|---|
|981231|Sequência de comentário SQL detectada.|
|981260|Codificação hexadecimal do SQL identificada|
|981320|Ataque de injeção de SQL = nomes comuns de BD detectados|
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
|981317|Alerta de detecção de anomalias da instrução SELECT do SQL|
|950007|Ataque de injeção de SQL cego|
|950001|Ataque de injeção de SQL|
|950908|Ataque de injeção de SQL.|
|959073|Ataque de injeção de SQL|
|981272|Detecta testes sqli cegos usando Sleep () ou benchmark ().|
|981250|Detecta as tentativas de benchmark e de injeção de suspensão do SQL, incluindo consultas condicionais|
|981241|Detecta tentativas de injeção de SQL condicional|
|981276|Procurando por injeção básica de SQL. Cadeia de caracteres de ataque comum para MySQL Oracle e outros.|
|981270|Localiza tentativas básicas de injeção SQL do MongoDB|
|981253|Detecta injeções de função/procedimento armazenado MySQL e PostgreSQL|
|981251|Detecta a injeção de UDF MySQL e outras tentativas de manipulação de dados/estrutura|

### <a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Descrição|
|---|---|
|973336|Filtro XSS-categoria 1 = vetor de marca de script|
|973338|Filtro XSS-categoria 3 = vetor de URI do JavaScript|
|981136|Regra 981136|
|981018|Regra 981018|
|958016|Ataque XSS (script entre sites)|
|958414|Ataque XSS (script entre sites)|
|958032|Ataque XSS (script entre sites)|
|958026|Ataque XSS (script entre sites)|
|958027|Ataque XSS (script entre sites)|
|958054|Ataque XSS (script entre sites)|
|958418|Ataque XSS (script entre sites)|
|958034|Ataque XSS (script entre sites)|
|958019|Ataque XSS (script entre sites)|
|958013|Ataque XSS (script entre sites)|
|958408|Ataque XSS (script entre sites)|
|958012|Ataque XSS (script entre sites)|
|958423|Ataque XSS (script entre sites)|
|958002|Ataque XSS (script entre sites)|
|958017|Ataque XSS (script entre sites)|
|958007|Ataque XSS (script entre sites)|
|958047|Ataque XSS (script entre sites)|
|958410|Ataque XSS (script entre sites)|
|958415|Ataque XSS (script entre sites)|
|958022|Ataque XSS (script entre sites)|
|958405|Ataque XSS (script entre sites)|
|958419|Ataque XSS (script entre sites)|
|958028|Ataque XSS (script entre sites)|
|958057|Ataque XSS (script entre sites)|
|958031|Ataque XSS (script entre sites)|
|958006|Ataque XSS (script entre sites)|
|958033|Ataque XSS (script entre sites)|
|958038|Ataque XSS (script entre sites)|
|958409|Ataque XSS (script entre sites)|
|958001|Ataque XSS (script entre sites)|
|958005|Ataque XSS (script entre sites)|
|958404|Ataque XSS (script entre sites)|
|958023|Ataque XSS (script entre sites)|
|958010|Ataque XSS (script entre sites)|
|958411|Ataque XSS (script entre sites)|
|958422|Ataque XSS (script entre sites)|
|958036|Ataque XSS (script entre sites)|
|958000|Ataque XSS (script entre sites)|
|958018|Ataque XSS (script entre sites)|
|958406|Ataque XSS (script entre sites)|
|958040|Ataque XSS (script entre sites)|
|958052|Ataque XSS (script entre sites)|
|958037|Ataque XSS (script entre sites)|
|958049|Ataque XSS (script entre sites)|
|958030|Ataque XSS (script entre sites)|
|958041|Ataque XSS (script entre sites)|
|958416|Ataque XSS (script entre sites)|
|958024|Ataque XSS (script entre sites)|
|958059|Ataque XSS (script entre sites)|
|958417|Ataque XSS (script entre sites)|
|958020|Ataque XSS (script entre sites)|
|958045|Ataque XSS (script entre sites)|
|958004|Ataque XSS (script entre sites)|
|958421|Ataque XSS (script entre sites)|
|958009|Ataque XSS (script entre sites)|
|958025|Ataque XSS (script entre sites)|
|958413|Ataque XSS (script entre sites)|
|958051|Ataque XSS (script entre sites)|
|958420|Ataque XSS (script entre sites)|
|958407|Ataque XSS (script entre sites)|
|958056|Ataque XSS (script entre sites)|
|958011|Ataque XSS (script entre sites)|
|958412|Ataque XSS (script entre sites)|
|958008|Ataque XSS (script entre sites)|
|958046|Ataque XSS (script entre sites)|
|958039|Ataque XSS (script entre sites)|
|958003|Ataque XSS (script entre sites)|
|973300|Possível ataque de XSS detectado-manipulador de marca HTML|
|973301|Ataque XSS detectado|
|973302|Ataque XSS detectado|
|973303|Ataque XSS detectado|
|973304|Ataque XSS detectado|
|973305|Ataque XSS detectado|
|973306|Ataque XSS detectado|
|973307|Ataque XSS detectado|
|973308|Ataque XSS detectado|
|973309|Ataque XSS detectado|
|973311|Ataque XSS detectado|
|973313|Ataque XSS detectado|
|973314|Ataque XSS detectado|
|973331|Filtros XSS do IE – ataque detectado.|
|973315|Filtros XSS do IE – ataque detectado.|
|973330|Filtros XSS do IE – ataque detectado.|
|973327|Filtros XSS do IE – ataque detectado.|
|973326|Filtros XSS do IE – ataque detectado.|
|973346|Filtros XSS do IE – ataque detectado.|
|973345|Filtros XSS do IE – ataque detectado.|
|973324|Filtros XSS do IE – ataque detectado.|
|973323|Filtros XSS do IE – ataque detectado.|
|973348|Filtros XSS do IE – ataque detectado.|
|973321|Filtros XSS do IE – ataque detectado.|
|973320|Filtros XSS do IE – ataque detectado.|
|973318|Filtros XSS do IE – ataque detectado.|
|973317|Filtros XSS do IE – ataque detectado.|
|973329|Filtros XSS do IE – ataque detectado.|
|973328|Filtros XSS do IE – ataque detectado.|

### <a name="crs42"></a>crs_42_tight_security

|RuleId|Descrição|
|---|---|
|950103|Ataque de percurso de caminho|

### <a name="crs45"></a>crs_45_trojans

|RuleId|Descrição|
|---|---|
|950110|Acesso ao Backdoor|
|950921|Acesso ao Backdoor|
|950922|Acesso ao Backdoor|

---

## <a name="next-steps"></a>Passos seguintes

- [Personalizar regras de firewall do aplicativo Web usando o portal do Azure](application-gateway-customize-waf-rules-portal.md)
