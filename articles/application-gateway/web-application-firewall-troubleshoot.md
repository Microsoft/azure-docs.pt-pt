---
title: Resolver problemas relacionados com a Firewall de aplicações Web para o Gateway de aplicação do Azure
description: Este artigo fornece informações de resolução de problemas de Firewall de aplicações Web (WAF) para o Gateway de aplicação do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082446"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Resolver problemas de Firewall de aplicações Web (WAF) para o Gateway de aplicação do Azure

Existem algumas coisas que pode fazer se os pedidos que devem passar por meio de seu Firewall de aplicações Web (WAF) são bloqueados.

Em primeiro lugar, certifique-se de que leu a [descrição geral de WAF](waf-overview.md) e o [configuração de WAF](application-gateway-waf-configuration.md) documentos. Além disso, certifique-se de que ativou [monitorização da WAF](application-gateway-diagnostics.md) estes artigos explicam como o WAF funciona, como a regra de WAF define o trabalho e como aceder aos registos da WAF.

## <a name="understanding-waf-logs"></a>Registos de compreensão WAF

A finalidade de registos WAF é mostrar todos os pedidos que é correspondido ou bloqueado pela WAF. É um livro razão de todos os pedidos avaliados que são correspondidos ou bloqueados. Se notar que o WAF bloqueia um pedido que não deveria (um falso positivo), pode fazer algumas coisas. Em primeiro lugar, restringir e encontre o pedido específico. Examine os registos para localizar o ID de URI, timestamp ou a transação específico da solicitação. Quando encontrar as entradas de registo associado, pode começar a tomar decisões sobre falsos positivos.

Por exemplo, digamos que tenha um tráfego legítimo que contém a cadeia de caracteres *1 = 1* que pretende passar o WAF. Se tentar o pedido, o WAF bloqueia o tráfego que contém seu *1 = 1* cadeia de caracteres em qualquer parâmetro ou o campo. Esta é uma cadeia de caracteres, muitas vezes, associada um ataque de injeção de SQL. Pode examinar os registos e ver o carimbo de hora do pedido e as regras de bloqueado/correspondentes.

No exemplo a seguir, pode ver que quatro regras são acionadas durante a mesma solicitação (usando o campo de TransactionId). Primeiro diz correspondente porque o utilizador utilizou um IP/numérico URL do pedido, o que aumenta a pontuação de anomalias em três, uma vez que é um aviso. A regra seguinte correspondentes é 942130, que é a que está procurando. Pode ver o *1 = 1* no `details.data` campo. Isso aumenta ainda mais a pontuação de anomalias em três novamente, como também é um aviso. Em geral, cada regra que tenha a ação **Matched** aumenta a anomalias de pontuação e, neste momento a classificação de anomalia seria seis. Para obter mais informações, consulte [modo de classificação de anomalia](waf-overview.md#anomaly-scoring-mode).

As entradas de registo de dois final mostram que o pedido foi bloqueado porque a classificação de anomalia era suficientemente elevada. Estas entradas têm uma ação diferente do que os outros dois. Mostrem, na verdade, eles *bloqueado* o pedido. Estas regras são obrigatórias e não podem ser desativadas. Eles não devem ser considerados como regras, mas mais como infraestrutura de núcleo das operações internas do WAF.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Correção de falsos positivos

Com estas informações e o conhecimento de que a regra 942130 é aquele que corresponde a *1 = 1* cadeia de caracteres, pode fazer algumas coisas para parar o tráfego de bloqueios:

- Utilizar uma lista de exclusão

   Ver [configuração do WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) para obter mais informações sobre a exclusão lista.
- Desative a regra.

### <a name="using-an-exclusion-list"></a>Utilizar uma lista de exclusão

Para tomar uma decisão informada sobre o processamento de um falso positivo, é importante para se familiarizar com as tecnologias a sua aplicação utiliza. Por exemplo, digamos que existem não é um SQL server na sua pilha de tecnologia, e estiver recebendo falsos positivos relacionados com essas regras. Desativar essas regras não necessariamente enfraquecer a sua segurança.

Uma vantagem de usar uma lista de exclusão é que apenas uma parte específica de um pedido está a ser desabilitada. No entanto, isso significa que uma exclusão específica é aplicável a todo o tráfego passar através do WAF, porque se trata de uma definição global. Por exemplo, isso pode levar a um problema se *1 = 1* é uma solicitação válida no corpo para uma determinada aplicação, mas não para outras pessoas. Outra vantagem é que pode escolher entre o corpo, cabeçalhos e cookies para ser excluído se uma determinada condição for cumprida, em vez de excluir toda a solicitação.

Ocasionalmente, há casos em que os parâmetros específicos obterem passados para o WAF de forma que não pode ser intuitivo. Por exemplo, existe um token que é passado ao autenticar com o Azure Active Directory. Este token *__RequestVerificationToken*, normalmente, obter passado como um Cookie do pedido. No entanto, em alguns casos em que os cookies estão desativados, este token também é passado como um atributo de pedido ou "arg". Se isto acontecer, precisa garantir que *__RequestVerificationToken* é adicionado à lista de exclusão, como um **nome do atributo de pedido** também.

![Exclusões](media/waf-tshoot/exclusion-list.png)

Neste exemplo, pretende excluir da **nome de atributo de pedido** que é igual a *Texto1*. Isso é aparente, pois pode ver o nome de atributo nos registos de firewall: **dados: Dados correspondentes: 1 = 1 encontrado dentro ARGS:text1: 1=1**. O atributo é **Texto1**. Também pode encontrar este nome de atributo algumas outras formas, consulte [localizando pedido nomes de atributo](#finding-request-attribute-names).

![Listas de exclusão de WAF](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>Desativar regras

Outra forma de contornar um falso positivo é que ao desativar a regra correspondentes no pensamento de entrada a WAF foi malicioso. Uma vez que analisado os registos da WAF e foram reduzidas a regra para baixo para 942130, pode desativá-lo no portal do Azure. Ver [personalizar regras de firewall de aplicações web através do portal do Azure](application-gateway-customize-waf-rules-portal.md).

Uma vantagem de desativar uma regra é que se sabe, todo o tráfego que contém uma determinada condição que normalmente será bloqueada tráfego é válido, pode desativar essa regra para o WAF inteiro. No entanto, se apenas tráfego é válido num caso de uso específico, abrir uma vulnerabilidade desabilitando essa regra para o WAF todo uma vez que é uma definição global.

Se pretender utilizar o Azure PowerShell, veja [personalizar regras de firewall de aplicações web através do PowerShell](application-gateway-customize-waf-rules-powershell.md). Se pretender utilizar a CLI do Azure, veja [personalizar regras de firewall de aplicações web através da CLI do Azure](application-gateway-customize-waf-rules-cli.md).

![Regras de WAF](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Nomes de atributo de pedido de localização

Com a ajuda do [Fiddler](https://www.telerik.com/fiddler), inspecionar solicitações individuais e determinar quais campos específicos de uma página da web são chamados. Isto pode ajudar a excluir determinados campos da inspeção utilizar listas de exclusão.

Neste exemplo, pode ver que o campo onde o *1 = 1* foi introduzida a cadeia de caracteres é chamado **Texto1**.

![Fiddler](media/waf-tshoot/fiddler-1.png)

Este é um campo que pode excluir. Para saber mais sobre listas de exclusão, consulte [limites de tamanho de pedido de firewall de aplicação Web e listas de exclusão](application-gateway-waf-configuration.md#waf-exclusion-lists). Pode excluir a avaliação neste caso, configurar a exclusão seguinte:

![Exclusão de WAF](media/waf-tshoot/waf-exclusion-02.png)

Também pode examinar os registos de firewall para obter as informações para ver o que precisa de adicionar à lista de exclusão. Para ativar o registo, consulte [estado de funcionamento do Back-end, registos de diagnóstico e métricas para o Gateway de aplicação](application-gateway-diagnostics.md).

Examine o registo de firewall e ver o ficheiro PT1H.json durante a hora em que ocorreu o pedido que quer inspecionar.

Neste exemplo, pode ver que tem quatro regras com o mesmo TransactionID e que todos eles ocorreram no mesmo momento exato:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Com o seu conhecimento de como a regra CRS define o trabalho e que o conjunto de regras CRS 3.0 funciona com uma sistema de classificação de anomalia (consulte [firewall de aplicações Web para o Gateway de aplicação do Azure](waf-overview.md)) sabe que a parte inferior duas regras com o  **ação: Bloqueado** estão a bloquear o propriedade com base na classificação total de anomalias. As regras para se concentrar estão as principais dois.

A primeira entrada é registada porque o utilizador utilizado um endereço IP numérico para navegar para o Gateway de aplicação, que pode ser ignorado nesse caso.

O segundo um (regra 942130) é o interessante. Pode ver os detalhes que correspondente um padrão (1 = 1) e o campo é chamado **Texto1**. Siga os passos anteriores para excluir os **nome do atributo de pedido** que **igual a** **1 = 1**.

## <a name="finding-request-header-names"></a>Nomes de cabeçalho de pedido de localização

Fiddler é uma ferramenta útil mais uma vez para localizar os nomes de cabeçalho de pedido. Captura de ecrã seguinte, pode ver os cabeçalhos para este pedido GET, que incluem *Content-Type*, *agente do usuário*e assim por diante.

![Fiddler](media/waf-tshoot/fiddler-2.png)

Outra forma de ver os cabeçalhos de solicitação e resposta é examinar o interior das ferramentas de desenvolvimento do Chrome. Pode pressionar F12 ou o botão direito do mouse -> **Inspect** -> **ferramentas de programação**e selecione o **rede** separador. Carregar uma página da web e clique o pedido que quer inspecionar.

![Chrome F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Nomes de cookie do pedido de localização

Se o pedido contém cookies, o **Cookies** guia pode ser selecionada para visualizá-los no Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Restringir os parâmetros de globais para eliminar falsos positivos

- Desativar a inspeção de corpo de pedido

   Definindo **corpo do pedido Inspect** como desativado, os corpos de pedido de todo o tráfego não serão avaliados por seu WAF. Isso poderá ser útil se souber que os corpos de pedido não são maliciosos à sua aplicação.

   Ao desabilitar esta opção, somente o corpo do pedido não é inspecionado. Os cabeçalhos e os cookies permanecem inspected, a menos que os itens individuais são excluídos usando a funcionalidade de lista de exclusão.

- Limites de tamanho de ficheiro

   Ao limitar o tamanho do ficheiro do seu WAF, está limitando a possibilidade de um ataque acontecer aos seus servidores web. Ao permitir que os ficheiros grandes ser carregado, aumenta o risco de back-end ficar sobrecarregada. Limitar o tamanho do ficheiro para um caso de utilização normal para a sua aplicação é apenas outra forma de impedir ataques.

   > [!NOTE]
   > Se souber que seu aplicativo nunca precisará qualquer carregamento de ficheiro acima um tamanho específico, pode restringir que, ao definir um limite.

## <a name="next-steps"></a>Passos Seguintes

Ver [como configurar a firewall de aplicações web no Gateway de aplicação](tutorial-restrict-web-traffic-powershell.md).
