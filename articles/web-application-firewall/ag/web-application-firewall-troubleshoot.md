---
title: Troubleshoot - Firewall de aplicação web Azure
description: Este artigo fornece informações de resolução de problemas para firewall de aplicação web (WAF) para Gateway de Aplicação Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046198"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Firewall de aplicação web de resolução de problemas (WAF) para Gateway de aplicação Azure

Existem algumas coisas que pode fazer se os pedidos que devem passar através da sua Firewall de Aplicação Web (WAF) estiverem bloqueados.

Em primeiro lugar, certifique-se de que leu a visão geral da [WAF](ag-overview.md) e os documentos de [configuração waf.](application-gateway-waf-configuration.md) Além disso, certifique-se de que permitiu [a monitorização](../../application-gateway/application-gateway-diagnostics.md) do WAF Estes artigos explicam como funciona a WAF, como funciona a regra WAF e como aceder aos registos waf.

## <a name="understanding-waf-logs"></a>Compreender os registos waf

O objetivo dos registos WAF é mostrar todos os pedidos que são combinados ou bloqueados pela WAF. É um livro-razão de todos os pedidos avaliados que são combinados ou bloqueados. Se notar que o WAF bloqueia um pedido que não deve (um falso positivo), pode fazer algumas coisas. Primeiro, reduza-se, e encontre o pedido específico. Procure nos registos para encontrar o URI específico, carimbo de tempo ou identificação de transação do pedido. Quando encontrar as entradas de registo associadas, pode começar a agir com base nos falsos positivos.

Por exemplo, diga que tem um tráfego legítimo contendo a corda *1=1* que pretende passar através do seu WAF. Se tentar o pedido, o WAF bloqueia o tráfego que contém a sua corda *1=1* em qualquer parâmetro ou campo. Esta é uma corda frequentemente associada a um ataque de injeção SQL. Pode ver os registos e ver o carimbo de tempo do pedido e as regras que bloquearam/correspondem.

No exemplo seguinte, pode ver que quatro regras são desencadeadas durante o mesmo pedido (utilizando o campo TransactionId). O primeiro diz que correspondeu porque o utilizador usou um URL numérico/IP para o pedido, o que aumenta a pontuação de anomalia em três, uma vez que é um aviso. A próxima regra que correspondeu é 942130, que é a que procura. Pode ver o *1=1* no `details.data` campo. Isto aumenta ainda mais a pontuação da anomalia em três novamente, já que é também um aviso. Geralmente, todas as regras que têm a ação **Igualadas** aumentam a pontuação da anomalia, e neste momento a pontuação de anomalia seria de seis. Para mais informações, consulte o [modo de pontuação Anomaly](ag-overview.md#anomaly-scoring-mode).

As duas últimas entradas de registo mostram que o pedido foi bloqueado porque a pontuação da anomalia era alta o suficiente. Estas entradas têm uma ação diferente das outras duas. Mostram que *bloquearam* o pedido. Estas regras são obrigatórias e não podem ser desativadas. Não devem ser considerados como regras, mas mais como infraestruturas fundamentais dos internos da WAF.

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

## <a name="fixing-false-positives"></a>Fixação de falsos positivos

Com esta informação, e o conhecimento que a regra 942130 é a que corresponde à corda *1=1,* você pode fazer algumas coisas para impedir que isso bloqueie o seu tráfego:

- Utilize uma Lista de Exclusão

   Consulte a [configuração WAF](application-gateway-waf-configuration.md#waf-exclusion-lists) para obter mais informações sobre listas de exclusão.
- Desativar a regra.

### <a name="using-an-exclusion-list"></a>Usando uma lista de exclusão

Para tomar uma decisão informada sobre como lidar com um falso positivo, é importante familiarizar-se com as tecnologias que a sua aplicação utiliza. Por exemplo, digamos que não há um servidor SQL na sua pilha de tecnologia, e está a receber falsos positivos relacionados com essas regras. Desativar essas regras não enfraquece necessariamente a sua segurança.

Um dos benefícios da utilização de uma lista de exclusão é que apenas uma parte específica de um pedido está a ser desativada. No entanto, isto significa que uma exclusão específica é aplicável a todo o tráfego que passa pelo seu WAF porque é um cenário global. Por exemplo, isto pode levar a um problema se *1=1* é um pedido válido no organismo para uma determinada aplicação, mas não para outros. Outro benefício é que você pode escolher entre corpo, cabeçalhos e cookies a excluir se uma determinada condição for satisfeita, em oposição a excluir todo o pedido.

Ocasionalmente, há casos em que parâmetros específicos são passados para a WAF de uma forma que pode não ser intuitiva. Por exemplo, há um símbolo que é passado ao autenticar usando o Diretório Ativo Azure. Este símbolo, *__RequestVerificationToken,* geralmente é passado como um Cookie de Pedido. No entanto, em alguns casos em que os cookies são desativados, este token também é passado como um atributo de pedido ou "arg". Se isso acontecer, tem de garantir que *__RequestVerificationToken* é adicionado à lista de exclusão como nome de **atributo do Pedido.**

![Exclusões](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Neste exemplo, pretende excluir o nome de **atributo do Pedido** que seja igual ao *texto1*. Isto é evidente porque pode ver o nome do atributo nos registos de firewall: **dados: Dados compatíveis: 1=1 encontrados dentro do ARGS:text1: 1=1**. O atributo é **texto 1**. Você também pode encontrar este nome de atributo de várias outras maneiras, ver [encontrar nomes de atributos de pedido](#finding-request-attribute-names)de pedido .

![Listas de exclusão da WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Regras incapacitantes

Outra forma de contornar um falso positivo é desativar a regra que correspondia à entrada que a WAF pensava ser maliciosa. Uma vez que analisou os registos waf e reduziu a regra para 942130, pode desativá-la no portal Azure. Consulte personalizar as regras de [firewall da aplicação web através do portal Azure](application-gateway-customize-waf-rules-portal.md).

Um dos benefícios de desativar uma regra é que se souber todo o tráfego que contenha uma determinada condição que normalmente será bloqueada é o tráfego válido, pode desativar essa regra para toda a WAF. No entanto, se for apenas um tráfego válido num caso de uso específico, abre-se uma vulnerabilidade desativando essa regra para toda a WAF, uma vez que é um cenário global.

Se quiser utilizar o Azure PowerShell, consulte personalizar as regras de firewall da [aplicação web através do PowerShell](application-gateway-customize-waf-rules-powershell.md). Se quiser utilizar o Azure CLI, consulte Personalizar as regras de firewall da [aplicação web através do Azure CLI](application-gateway-customize-waf-rules-cli.md).

![Regras waf](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Encontrar nomes de atributos de pedido

Com a ajuda do [Fiddler,](https://www.telerik.com/fiddler)inspeciona os pedidos individuais e determina quais os campos específicos de uma página web. Isto pode ajudar a excluir certos campos da inspeção utilizando listas de exclusão.

Neste exemplo, pode ver que o campo onde a corda *1=1* foi introduzida é chamado **texto1**.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Este é um campo que pode excluir. Para saber mais sobre listas de exclusão, consulte os limites de tamanho do pedido de firewall de [aplicação Web e listas de exclusão](application-gateway-waf-configuration.md#waf-exclusion-lists). Pode excluir a avaliação neste caso configurando a seguinte exclusão:

![Exclusão waf](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Também pode examinar os registos de firewall para obter a informação para ver o que precisa adicionar à lista de exclusão. Para permitir a exploração madeireira, consulte a [saúde de back-end, os registos de diagnóstico e as métricas para o Gateway](../../application-gateway/application-gateway-diagnostics.md)da Aplicação .

Examine o registo de firewall e veja o ficheiro PT1H.json durante a hora que o pedido que pretende inspecionar ocorreu.

Neste exemplo, pode ver que tem quatro regras com o mesmo TransactionID, e que todas ocorreram exatamente ao mesmo tempo:

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

Com o seu conhecimento de como a regra do CRS funciona, e que a regra do CRS 3.0 funciona com um sistema de pontuação de anomalias (ver Firewall de aplicação web para Gateway de [aplicação azure)](ag-overview.md)sabe que as duas regras inferiores com a **ação: A** propriedade bloqueada está bloqueando com base na pontuação total de anomalias. As regras em que nos concentrarsão são as duas primeiras.

A primeira entrada é registada porque o utilizador usou um endereço IP numérico para navegar para o Gateway da Aplicação, o que pode ser ignorado neste caso.

A segunda (regra 942130) é a interessante. Pode ver nos detalhes que corresponde a um padrão (1=1), e o campo é chamado **texto1**. Siga os mesmos passos anteriores para excluir o Nome do **Atributo pedido** que **equivale a** **1=1**.

## <a name="finding-request-header-names"></a>Encontrar nomes de cabeçalho de pedido

Fiddler é uma ferramenta útil mais uma vez para encontrar nomes de cabeçalho de pedido. Na seguinte imagem, pode ver os cabeçalhos para este pedido GET, que incluem *Conteúdo-Tipo,* *User-Agent,* e assim por diante.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Outra forma de ver os cabeçalhos de pedido e resposta é olhar para dentro das ferramentas de desenvolvimento do Chrome. Pode premir F12 ou clicar à direita -> **Inspecione** -> **as Ferramentas**de Desenvolvimento, e selecione o separador **Rede.** Carregue uma página web e clique no pedido que pretende inspecionar.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Encontrar nomes de cookies de pedido

Se o pedido contiver cookies, o separador **Cookies** pode ser selecionado para vê-los no Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Restringir os parâmetros globais para eliminar falsos positivos

- Inspeção corporal de pedido de desativação

   Ao definir o organismo de **pedido de inspeção** para desligar, os corpos de pedido de todo o tráfego não serão avaliados pela sua WAF. Isto pode ser útil se souber que os órgãos de pedido não são maliciosos para a sua aplicação.

   Ao desativar esta opção, apenas o organismo de pedido não é inspecionado. Os cabeçalhos e os cookies permanecem inspecionados, a menos que os individuais sejam excluídos utilizando a funcionalidade da lista de exclusão.

- Limites de tamanho de ficheiro

   Ao limitar o tamanho do ficheiro para o seu WAF, está a limitar a possibilidade de um ataque acontecer aos seus servidores web. Ao permitir o upload de ficheiros grandes, o risco de o seu backend ser sobrecarregado aumenta. Limitar o tamanho do ficheiro a um caso de utilização normal para a sua aplicação é apenas mais uma forma de prevenir ataques.

   > [!NOTE]
   > Se souber que a sua aplicação nunca precisará de qualquer upload de ficheiro acima de um determinado tamanho, pode restringir isso estabelecendo um limite.

## <a name="firewall-metrics-waf_v1-only"></a>Métricas de Firewall (apenas WAF_v1)

Para firewalls de aplicação web v1, as seguintes métricas estão agora disponíveis no portal: 

1. Contagem de pedidos bloqueados por firewall de aplicação web O número de pedidos que foram bloqueados
2. Firewall firewall firewall bloqueado Regra Contagem Todas as regras que foram correspondidas **e** o pedido foi bloqueado
3. Distribuição total de regras da firewall da aplicação web Todas as regras que foram correspondidas durante a avaliação
     
Para ativar as métricas, selecione o separador **Métricas** no portal e selecione uma das três métricas.

## <a name="next-steps"></a>Passos seguintes

Ver [Como configurar a firewall](tutorial-restrict-web-traffic-powershell.md)da aplicação web no Gateway da aplicação .
