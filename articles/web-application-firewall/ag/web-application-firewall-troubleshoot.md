---
title: Resolução de problemas - Firewall de aplicação web Azure
description: Este artigo fornece informações de resolução de problemas para firewall de aplicação web (WAF) para gateway de aplicação Azure
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131841"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Firewall de aplicação web de resolução de problemas (WAF) para gateway de aplicação Azure

Há algumas coisas que pode fazer se os pedidos que devem passar pela sua Firewall de Aplicação Web (WAF) estiverem bloqueados.

Em primeiro lugar, certifique-se de que leu a [visão geral](ag-overview.md) da WAF e os documentos de [configuração da WAF.](application-gateway-waf-configuration.md) Além disso, certifique-se de que ativou [a monitorização da WAF](../../application-gateway/application-gateway-diagnostics.md) Estes artigos explicam como funciona a WAF, como funciona a regra WAF e como aceder aos registos da WAF.

As regras da OWASP são concebidas para serem muito rígidas fora da caixa e para serem sintonizadas de acordo com as necessidades específicas da aplicação ou organização utilizando o WAF. É inteiramente normal, e na verdade esperado em muitos casos, criar exclusões, regras personalizadas e até mesmo desativar regras que possam estar a causar problemas ou falsos positivos. As políticas per-site e per-URI permitem que estas alterações afetem apenas sites/URIs específicos, pelo que quaisquer alterações não devem ter de afetar outros sites que podem não estar a desabar nas mesmas questões. 

## <a name="understanding-waf-logs"></a>Compreender registos WAF

O objetivo dos registos WAF é mostrar todos os pedidos que sejam correspondidos ou bloqueados pela WAF. É um livro-razão de todos os pedidos avaliados que são combinados ou bloqueados. Se notar que o WAF bloqueia um pedido que não deve (um falso positivo), pode fazer algumas coisas. Primeiro, reduza e encontre o pedido específico. Procure nos registos para encontrar o URI específico, o timetamp ou o ID de transação do pedido. Quando encontrar as entradas de registo associadas, pode começar a agir sobre os falsos positivos.

Por exemplo, digamos que tem um tráfego legítimo contendo a corda *1=1* que pretende passar através do seu WAF. Se experimentar o pedido, o WAF bloqueia o tráfego que contém a sua corda *1=1* em qualquer parâmetro ou campo. Esta é uma corda frequentemente associada a um ataque de injeção SQL. Pode consultar os registos e ver o tempo de verificação do pedido e as regras que bloqueiam/correspondem.

No exemplo seguinte, pode ver que quatro regras são desencadeadas durante o mesmo pedido (utilizando o campo TransactionId). O primeiro diz que corresponde porque o utilizador usou um URL numérico/IP para o pedido, o que aumenta a pontuação da anomalia em três, uma vez que é um aviso. A próxima regra que corresponde é 942130, que é a que procura. Pode ver o *1=1* no `details.data` campo. Isto aumenta ainda mais a pontuação da anomalia em três novamente, já que também é um aviso. Geralmente, todas as regras que têm a ação **Matched** aumentam a pontuação da anomalia, e neste momento a pontuação da anomalia seria de seis. Para obter mais informações, consulte [o modo de pontuação de Anomalias](ag-overview.md#anomaly-scoring-mode).

As duas últimas entradas de registo mostram que o pedido foi bloqueado porque a pontuação da anomalia era alta o suficiente. Estas entradas têm uma ação diferente das outras duas. Mostram que *bloquearam* o pedido. Estas regras são obrigatórias e não podem ser desativadas. Não devem ser consideradas regras, mas mais como infraestruturas centrais dos internos da WAF.

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

Com esta informação, e o conhecimento de que a regra 942130 é a que corresponde à cadeia *1=1,* pode fazer algumas coisas para impedir que isto bloqueie o seu tráfego:

- Utilize uma Lista de Exclusão

   Consulte [a configuração DAM](application-gateway-waf-configuration.md#waf-exclusion-lists) para obter mais informações sobre listas de exclusão.
- Desative a regra.

### <a name="using-an-exclusion-list"></a>Usando uma lista de exclusão

Para tomar uma decisão informada sobre como lidar com um falso positivo, é importante familiarizar-se com as tecnologias que a sua aplicação utiliza. Por exemplo, digamos que não há um servidor SQL na sua pilha de tecnologia, e que está a receber falsos positivos relacionados com essas regras. Desativar essas regras não enfraquece necessariamente a sua segurança.

Um dos benefícios da utilização de uma lista de exclusão é que apenas uma parte específica de um pedido está a ser desativada. No entanto, isto significa que uma exclusão específica é aplicável a todo o tráfego que passa pelo seu WAF porque é um cenário global. Por exemplo, isto pode levar a um problema se *1=1* é um pedido válido no corpo para uma determinada aplicação, mas não para outras. Outro benefício é que pode escolher entre corpo, cabeçalhos e cookies a excluir se uma determinada condição for satisfeita, em oposição a excluir todo o pedido.

Ocasionalmente, há casos em que parâmetros específicos são passados para a WAF de uma forma que pode não ser intuitiva. Por exemplo, há um token que é passado ao autenticar usando o Azure Ative Directory. Este token, *__RequestVerificationToken,* geralmente é passado como um Cookie de Pedido. No entanto, em alguns casos em que os cookies são desativados, este token também é passado como um atributo de pedido ou "arg". Se isso acontecer, tem de se certificar de que *__RequestVerificationToken* é adicionado à lista de exclusão como **nome de atributo Pedido.**

![Exclusões](../media/web-application-firewall-troubleshoot/exclusion-list.png)

Neste exemplo, pretende excluir o **nome de atributo Pedido** que equivale a *texto1*. Isto é aparente porque pode ver o nome do atributo nos registos de firewall: **dados: Dados combinados: 1=1 encontrado dentro de ARGS:text1: 1=1**. O atributo é **texto1**. Também pode encontrar este nome de atributo de algumas outras formas, ver [Encontrar pedido atribuir nomes](#finding-request-attribute-names).

![Listas de exclusão da WAF](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Regras incapacitantes

Outra forma de contornar um falso positivo é desativar a regra que correspondia na entrada que o WAF pensava ser maliciosa. Uma vez que analisou os registos da WAF e reduziu a regra para 942130, pode desativá-la no portal Azure. Consulte [as regras de firewall de aplicações web personalizadas através do portal Azure](application-gateway-customize-waf-rules-portal.md).

Um benefício de desativar uma regra é que se você sabe que todo o tráfego que contém uma determinada condição que normalmente será bloqueado é tráfego válido, você pode desativar essa regra para toda a WAF. No entanto, se for apenas tráfego válido num caso de uso específico, você abre uma vulnerabilidade desativando essa regra para toda a WAF, uma vez que é um cenário global.

Se pretender utilizar o Azure PowerShell, consulte [as regras de firewall de aplicações web personalizadas através do PowerShell](application-gateway-customize-waf-rules-powershell.md). Se quiser utilizar o Azure CLI, consulte [as regras de firewall de aplicações web personalizadas através do Azure CLI](application-gateway-customize-waf-rules-cli.md).

![Regras da WAF](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Encontrar nomes de atributos de pedido

Com a ajuda do [Fiddler,](https://www.telerik.com/fiddler)inspeciona os pedidos individuais e determina quais os campos específicos de uma página web. Isto pode ajudar a excluir certos campos da inspeção utilizando listas de exclusão.

Neste exemplo, pode ver-se que o campo onde a corda *1=1* foi inserida é chamado **texto1**.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Screenshot do Progress Telerik Fiddler Web Debugger. No separador Raw, 1 = 1 é visível após o nome text1." border="false":::

Este é um campo que pode excluir. Para saber mais sobre listas de exclusão, consulte os limites de [tamanho do pedido de firewall da web e listas de exclusão.](application-gateway-waf-configuration.md#waf-exclusion-lists) Pode excluir a avaliação neste caso configurando a seguinte exclusão:

![Exclusão da WAF](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

Também pode examinar os registos de firewall para obter as informações para ver o que precisa de adicionar à lista de exclusão. Para ativar o registo, consulte [a saúde de back-end, registos de recursos e métricas para o Gateway de Aplicações](../../application-gateway/application-gateway-diagnostics.md).

Examine o registo de firewall e veja a PT1H.jsno ficheiro durante a hora em que o pedido que pretende inspecionar ocorreu.

Neste exemplo, pode ver que tem quatro regras com o mesmo TransactionID, e que todas elas ocorreram exatamente ao mesmo tempo:

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

Com o seu conhecimento de como funciona a regra do CRS, e que as regras do CRS funcionam com um sistema de pontuação de anomalias (ver [Web Application Firewall for Azure Application Gateway),](ag-overview.md)sabe que as duas regras inferiores com a **ação: A** propriedade bloqueada está a bloquear com base na pontuação total da anomalia. As regras para nos concentrarmos são as duas primeiras.

A primeira entrada é registada porque o utilizador usou um endereço IP numérico para navegar para o Gateway de Aplicações, o que pode ser ignorado neste caso.

A segunda (regra 942130) é a interessante. Pode ver nos detalhes que corresponde a um padrão (1=1), e o campo é nomeado **texto1**. Siga os mesmos passos anteriores para excluir o **Nome de Atributo pedido** que é igual **a** **1=1**.

## <a name="finding-request-header-names"></a>Encontrar nomes de cabeçalho de pedido

O Violinista é uma ferramenta útil mais uma vez para encontrar nomes de cabeçalho de pedido. Na imagem seguinte, pode ver os cabeçalhos para este pedido GET, que inclui *o Tipo de Conteúdo,* *User-Agent,* e assim por diante.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Screenshot do Progress Telerik Fiddler Web Debugger. As listas de separadores Raw solicitam detalhes do cabeçalho como a ligação, o tipo de conteúdo e o agente de utilizador." border="false":::

Outra forma de ver os cabeçalhos de pedido e resposta é olhar para dentro das ferramentas de desenvolvimento do Chrome. Pode premir F12 ou click -> **Inspect**  ->  **Developer Tools**, e selecionar o separador **'Rede'.** Carregue uma página web e clique no pedido que pretende inspecionar.

![Cromo F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Encontrar nomes de cookies de pedido

Se o pedido contiver cookies, o separador **Cookies** pode ser selecionado para os visualizar no Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Restringir os parâmetros globais para eliminar falsos positivos

- Inspeção do corpo de pedido de desativação

   Ao definir **o corpo de pedido de inspeção** para desligar, os corpos de pedido de todo o tráfego não serão avaliados pelo seu WAF. Isto pode ser útil se você sabe que os corpos de pedido não são mal-intencionados para a sua aplicação.

   Ao desativar esta opção, apenas o organismo de pedido não é inspecionado. Os cabeçalhos e cookies permanecem inspecionados, a menos que os individuais sejam excluídos usando a funcionalidade da lista de exclusão.

- Limites de tamanho do ficheiro

   Ao limitar o tamanho do ficheiro para o seu WAF, está a limitar a possibilidade de um ataque acontecer aos seus servidores web. Ao permitir o upload de grandes ficheiros, o risco de o seu backend ser sobrecarregado aumenta. Limitar o tamanho do ficheiro a um caso de utilização normal para a sua aplicação é apenas mais uma forma de prevenir ataques.

   > [!NOTE]
   > Se souber que a sua aplicação nunca necessitará de qualquer upload de ficheiros acima de um determinado tamanho, pode restringir isso definindo um limite.

## <a name="firewall-metrics-waf_v1-only"></a>Métricas de Firewall (apenas WAF_v1)

Para as firewalls de aplicação web v1, as seguintes métricas estão agora disponíveis no portal: 

1. Firewall de aplicação web bloqueado pedido contar o número de pedidos que foram bloqueados
2. Firewall de aplicação web bloqueou regras todas as regras que foram correspondidas **e** o pedido foi bloqueado
3. Distribuição total de regras da Web Application Firewall Todas as regras que foram correspondidas durante a avaliação
     
Para ativar as métricas, selecione o **separador Métricas** no portal e selecione uma das três métricas.

## <a name="next-steps"></a>Passos seguintes

Ver [como configurar firewall de aplicações web no Gateway de aplicações de aplicações.](tutorial-restrict-web-traffic-powershell.md)
