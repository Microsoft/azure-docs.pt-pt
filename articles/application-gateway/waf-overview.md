---
title: Introdução à firewall de aplicações web para o Gateway de aplicação do Azure
description: Este artigo fornece uma descrição geral da firewall de aplicações web (WAF) para o Gateway de aplicação
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 5/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 9c2759222198f5df682d9e7a5363c0d9679e0fad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991408"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Firewall de aplicações Web para o Gateway de aplicação do Azure

O Gateway de aplicação do Azure oferece uma firewall de aplicações web (WAF) que fornece proteção centralizada das suas aplicações web de vulnerabilidades e exploits comuns. Aplicativos Web são cada vez mais direcionados por ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Injeção de SQL e scripts entre sites estão entre os ataques mais comuns.

Impedir esses ataques no código da aplicação é um desafio. Ele pode exigir manutenção rigorosa, a aplicação de patches e a monitorização em várias camadas da topologia da aplicação. Uma firewall de aplicações web centralizada ajuda a tornar o gerenciamento de segurança muito mais simples. Uma WAF também oferece aos administradores do aplicativo maior garantia de proteção contra ameaças e invasões.

Uma solução WAF pode reagir a uma ameaça de segurança mais rápida ao centralmente corrigir uma vulnerabilidade conhecida, em vez de proteger cada aplicativo web individualmente. Gateways de aplicação existentes podem ser facilmente convertidos em gateways de aplicação com capacidade de parede de fogo.

A WAF do Gateway de aplicação baseia-se no [conjunto de regra Core (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9 do Open Web Application Security Project (OWASP). O WAF é atualizada automaticamente para incluir a proteção contra novas vulnerabilidades, com qualquer configuração adicional necessária.

![Diagrama de WAF do Gateway de aplicação](./media/waf-overview/WAF1.png)

Gateway de aplicação atua como um controlador de entrega de aplicação (ADC). Ele oferece terminação de Secure Sockets Layer (SSL), afinidade de sessão baseada em cookies, distribuição de carga round robin, encaminhamento baseado em conteúdo, capacidade de alojar vários Web sites e os aprimoramentos de segurança.

Melhoramentos de segurança do Gateway de aplicação incluem gestão de políticas SSL e suporte de SSL ponto a ponto. Segurança de aplicativos é reforçada pela integração do WAF no Gateway de aplicação. A combinação protege as suas aplicações web contra vulnerabilidades mais comuns. E fornece uma localização central fácil configurar para gerir.

## <a name="benefits"></a>Benefícios

Esta secção descreve as principais vantagens que o Gateway de aplicação e o respetivo WAF fornecem.

### <a name="protection"></a>Proteção

* Proteja seus aplicativos web contra vulnerabilidades e ataques sem modificação do código de back-end web.

* Protege várias aplicações web ao mesmo tempo. Pode alojar uma instância de Gateway de aplicação de até 100 Web sites protegidos por uma firewall de aplicações web.

### <a name="monitoring"></a>Monitorização

* Monitorizar ataques lançados contra seus aplicativos web com um registo da WAF em tempo real. O registo está integrado [do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para acompanhar os alertas de WAF e monitorizar tendências facilmente.

* A WAF do Gateway de aplicação está integrado no Centro de segurança do Azure. Centro de segurança fornece uma visão central do Estado de segurança de todos os seus recursos do Azure.

### <a name="customization"></a>Personalização

* Pode personalizar regras WAF e grupos de regras para atender às suas necessidades de aplicação e eliminar falsos positivos.

## <a name="features"></a>Funcionalidades

- Proteção de injeção de SQL.
- Proteção contra scripts entre sites.
- Proteção contra outros ataques web comuns, como injeção de comandos, a divisão de respostas HTTP, contrabando de pedidos e a inclusão de ficheiros remota.
- Proteção contra violações de protocolo HTTP.
- Proteção contra anomalias de protocolo HTTP, como em falta alojar o agente do usuário e cabeçalhos de aceitação.
- Proteção contra bots, crawlers e scanners.
- Deteção de aplicação incorretas comuns (por exemplo, o Apache e o IIS).
- Limites de tamanho de pedido configurável com inferiores e superiores.
- Listas de exclusão permitem-lhe omitir determinados atributos de pedido de uma avaliação de WAF. Um exemplo comum é inserido o Active Directory tokens que são utilizados para autenticação ou campos de palavra-passe.

### <a name="core-rule-sets"></a>Conjuntos de regras principais

Gateway de aplicação suporta dois conjuntos de regras, o CRS 3.0 e CRS 2.2.9. Estas regras proteger seus aplicativos web contra atividades maliciosas.

A WAF do Gateway de aplicação vem pré-configurada com CRS 3.0 por predefinição. Mas pode optar por utilizar em vez disso, o CRS 2.2.9. CRS 3.0 oferece menos falsos positivos em comparação com CRS 2.2.9. Também pode [personalizar regras para se adequar às suas necessidades](application-gateway-customize-waf-rules-portal.md).

O WAF protege contra vulnerabilidades web da seguinte:

- Ataques de injeção de SQL
- Ataques de script entre sites
- Outros ataques comuns, como injeção de comandos, HTTP do pedido contrabando, a divisão de resposta HTTP e a inclusão de ficheiros remota
- Violações de protocolo HTTP
- Anomalias de protocolo HTTP, como em falta alojam o agente do usuário e cabeçalhos de aceitação
- Bots, crawlers e scanners
- Aplicação incorretas comuns (por exemplo, o Apache e o IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

CRS 3.0 inclui 13 grupos de regras, conforme mostrado na seguinte tabela. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Bloqueio pendente métodos (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|Proteger contra os scanners de porta e o ambiente|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Proteger contra o protocolo e problemas de codificação|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Proteger contra injeção de cabeçalho, contrabando de pedidos e divisão de respostas|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Proteger contra ataques de ficheiro e caminho|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Proteger contra ataques de inclusão (RFI) de ficheiros remota|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Proteger novamente a ataques de execução de código remoto|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Proteger contra ataques de injeção PHP|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Proteger contra ataques de script entre sites|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Proteger contra ataques de injeção de SQL|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Proteger contra ataques de fixação de sessão|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

O CRS 2.2.9 inclui 10 grupos de regras, conforme mostrado na seguinte tabela. Cada grupo contém várias regras, que podem ser desativadas.

|Grupo de regras|Descrição|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Proteger contra violações de protocolo (como carateres inválidos ou um GET com um corpo de pedido)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Proteger contra informações de cabeçalho incorretas|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Proteger contra argumentos ou ficheiros que excedem limitações|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Proteger contra métodos restritos, cabeçalhos e tipos de ficheiro|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Proteger contra web crawlers e scanners|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Proteger contra ataques genéricos (por exemplo, a fixação de sessão, inclusão de ficheiros remota e injeção PHP)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Proteger contra ataques de injeção de SQL|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Proteger contra ataques de script entre sites|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Proteger contra ataques de passagem de caminho|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Proteger contra trojans de backdoor|

### <a name="waf-modes"></a>Modos de WAF

A WAF do Gateway de aplicação pode ser configurado para ser executado em dois modos seguintes:

* **Modo de deteção**: Monitoriza e regista todos os alertas de ameaça. Ative os diagnósticos de Registro em log para o Gateway de aplicação no **diagnóstico** secção. Também deve certificar-se de que o registo WAF está selecionado e ativado. Firewall de aplicações Web não bloqueia pedidos de entrada quando ele está a funcionar no modo de deteção.
* **Modo de prevenção**: Invasões por meio de blocos e de ataques que as regras de detetar. O atacante recebe uma exceção de "acesso não autorizado 403" e a ligação é terminada. Modo de prevenção regista esses ataques nos registos WAF.

### <a name="anomaly-scoring-mode"></a>Modo de Scoring de anomalias

OWASP tem dois modos para decidir se pretende bloquear o tráfego: Modo tradicional e o modo de anomalias de classificação.

No modo tradicional, o tráfego que corresponde a qualquer regra é considerado independentemente das outras correspondências de regra. Esse modo é fácil de entender. Mas a falta de informações sobre regras de quantos correspondem uma solicitação específica é uma limitação. Por isso, foi introduzido o modo de anomalias de classificação. É a predefinição para 3 da OWASP. *x*.

No modo de anomalias de classificação de mensagens em fila, o tráfego que corresponde a qualquer regra não é imediatamente impedido quando a firewall está no modo de prevenção. Regras tenham uma gravidade de determinados: *Crítico*, *erro*, *aviso*, ou *aviso*. Que gravidade afeta um valor numérico para o pedido, o que é chamado de classificação de anomalia. Por exemplo, um *aviso* regra de correspondência contribui 3 para a classificação. Um *crítico* regra de correspondência contribui 5.

|Gravidade  |Value  |
|---------|---------|
|Crítico     |5|
|Erro        |4|
|Aviso      |3|
|Tenha em atenção       |2|

Existe um limite de 5 para a classificação de anomalias para bloquear o tráfego. Deste modo, um único *crítico* correspondência de regra é o suficiente para o WAF do Gateway de aplicação bloquear um pedido, até mesmo no modo de prevenção. Mas um *aviso* regra de correspondência apenas aumenta a anomalias pontuação por 3, que não não suficiente por si só para bloquear o tráfego.

> [!NOTE]
> A mensagem que é registada quando uma regra de WAF corresponde ao tráfego inclui o valor de ação "Bloqueado". Mas o tráfego é, na verdade, apenas bloqueado para uma classificação de anomalia de 5 ou superior.  

### <a name="waf-monitoring"></a>Monitorização da WAF

É importante monitorizar o estado de funcionamento do gateway de aplicação. Monitorização do Estado de funcionamento do seu WAF e as aplicações que esta protege é suportada pela integração com o Centro de segurança do Azure, Azure Monitor, e os registos do Azure Monitor.

![Diagrama de diagnóstico de WAF do Gateway de aplicação](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Registos do Gateway de aplicação estão integrados [do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md). Isto permite-lhe controlar as informações de diagnóstico, incluindo WAF alertas e registos. Pode aceder a esta capacidade no **diagnóstico** separador no recurso de Gateway de aplicação no portal ou diretamente através do Azure Monitor. Para saber mais sobre como ativar os registos, consulte [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

[Centro de segurança](../security-center/security-center-intro.md) ajuda a evitar, detetar e responder a ameaças. Ele fornece maior visibilidade e controlo da segurança dos seus recursos do Azure. Gateway de aplicação [integrado com o Centro de segurança](application-gateway-integration-security-center.md). Centro de segurança analisa o seu ambiente para detetar aplicações web desprotegidas. Ele pode recomendar o WAF do Gateway de aplicação para proteger estes recursos vulneráveis. Criar as firewalls diretamente a partir do Centro de segurança. Estas instâncias de WAF são integradas com o Centro de segurança. Enviam alertas e informações de estado de funcionamento ao centro de segurança para relatórios.

![Janela de descrição geral do Centro de segurança](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Registo

WAF do Gateway de aplicação fornece relatórios detalhados sobre cada ameaça que Deteta. Os registos estão integrados com registos de diagnóstico do Azure. Alertas são registados no formato. JSON. Estes registos podem ser integrados no [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![Registos de diagnóstico do Gateway de aplicação windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Preços da SKU da WAF do Gateway de Aplicação

A WAF do Gateway de aplicação está disponível num novo um SKU. Esta SKU está disponível apenas no modelo de aprovisionamento do Azure Resource Manager, não no modelo de implementação clássica. Além disso, o WAF SKU vem apenas em tamanhos de instâncias de Gateway de aplicação médios e grandes. Todos os limites para o Gateway de aplicação também se aplicam para o SKU da WAF.

Preço baseia-se numa tarifa de instância de gateway por hora e uma taxa de processamento de dados. [Preços do Gateway de aplicação](https://azure.microsoft.com/pricing/details/application-gateway/) para o SKU da WAF recém-carregado é diferente dos custos do SKU standard. Os custos de processamento de dados são o mesmo. Não existem por regra ou um grupo de regras custos. Pode proteger várias aplicações web atrás da mesma firewall de aplicação web. Não é cobrada para dar suporte a várias aplicações.

## <a name="next-steps"></a>Passos Seguintes

Ver [como configurar a firewall de aplicações web no Gateway de aplicação](tutorial-restrict-web-traffic-powershell.md).
