---
title: Introdução à firewall de aplicações web (WAF) para o Gateway de aplicação do Azure
description: Este artigo fornece uma descrição geral da firewall de aplicações web (WAF) para o Gateway de aplicação
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 11/16/2018
ms.author: amsriva
ms.openlocfilehash: 014353bafa31b1c4e924cba8335dbd30a48c2d11
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651435"
---
# <a name="web-application-firewall-waf"></a>Firewall de aplicações Web (WAF)

Firewall de aplicações Web (WAF) é uma funcionalidade de Gateway de Aplicação que fornece proteção centralizada das suas aplicações Web a partir de exploits e vulnerabilidades comuns.

Cada vez mais, as aplicações Web são alvo de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Comuns entre essas explorações são os ataques de injeção de SQL e ataques de script entre sites para citar alguns. 

Impedir este tipo de ataques ao código das aplicações constitui um desafio e exige uma manutenção, correção e monitorização rigorosas em várias camadas da topologia da aplicação. Uma firewall de aplicações Web centralizada ajuda a simplificar em muito a gestão da segurança e confere aos administradores de aplicações uma maior garantia de proteção contra as ameaças ou intrusões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rápida ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

WAF baseia-se nas regras da [conjuntos de regras de núcleo OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. Ele atualiza automaticamente para incluir a proteção contra novas vulnerabilidades, com qualquer configuração adicional necessária.

![imageURLroute](./media/waf-overview/WAF1.png)

Gateway de aplicação atua como um controlador de entrega de aplicações (ADC) e oferece terminação de SSL, afinidade de sessão baseada em cookies, distribuição de carga round robin, encaminhamento baseado em conteúdo, capacidade de alojar vários Web sites e os aprimoramentos de segurança.

Aprimoramentos de segurança do Gateway de aplicação oferecidos incluem gestão de políticas SSL e suporte SSL ponto a ponto. Agora, a segurança das aplicações está mais fortalecida, com a WAF (firewall de aplicações Web) a ser integrada diretamente na oferta do ADC. Desta forma, conseguimos fornecer uma localização central fácil de configurar que permite gerir e proteger as suas aplicações Web contra vulnerabilidades Web conhecidas.

## <a name="benefits"></a>Benefícios

Seguem-se as principais vantagens proporcionadas pelo Gateway de Aplicação e pela firewall de aplicações Web:

### <a name="protection"></a>Proteção

* Protege a sua aplicação Web de vulnerabilidades e ataques Web sem modificar o código de back-end.

* Protege várias aplicações Web em simultâneo por trás de um gateway de aplicação. O gateway de aplicação suporta o alojamento de até 20 Web sites por trás de um único gateway, que podem ser protegidos contra ataques Web com a WAF.

### <a name="monitoring"></a>Monitorização

* Utilize um registo da WAF em tempo real para monitorizar a sua aplicação Web contra ataques. Este registo está integrado no [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para registar alertas e registos da WAF e monitorizar tendências facilmente.

* WAF está integrada no Centro de segurança do Azure. O Centro de Segurança do Azure permite obter uma visão central do estado de segurança de todos os seus recursos do Azure.

### <a name="customization"></a>Personalização

* A capacidade para personalizar regras e grupos de regras WAF para se adequarem às necessidades da sua aplicação e eliminar falsos positivos.

## <a name="features"></a>Funcionalidades

- SQL injection protection
- Cruzar a proteção de criação de scripts do site
- Comuns Web proteção contra ataques, como injeção de comandos, contrabando, divisão de respostas HTTP de pedidos HTTP e ataques de inclusão de ficheiros remota
- Proteção contra violações de protocolo HTTP
- Proteção contra anomalias de protocolo HTTP, como em falta alojar o agente do usuário e cabeçalhos de aceitação
- Prevenção de contra bots, crawlers e scanners
- Deteção de aplicação incorretas comuns (por exemplo, Apache, IIS etc.)

### <a name="public-preview-features"></a>Funcionalidades de pré-visualização pública

O atual WAF pré-visualização pública SKU inclui as seguintes funcionalidades:

- **Limites de tamanho de pedido** -Firewall de aplicações Web permite aos usuários configurar limites de tamanho do pedido dentro inferiores e superiores.
- **Listas de exclusão** -listas de exclusão de WAF permitir que os utilizadores omitir determinados atributos de pedido de uma avaliação de WAF. Um exemplo comum é o que Active Directory inserido tokens que são utilizados para autenticação ou campos de palavra-passe.

Para obter mais informações sobre a pré-visualização pública do WAF, veja [Web limites de tamanho de pedido de firewall de aplicação e listas de exclusão (pré-visualização pública)](application-gateway-waf-configuration.md).





### <a name="core-rule-sets"></a>Conjuntos de regras principais

O Gateway de Aplicação suporta dois conjuntos de regras: CRS 3.0 e CRS 2.2.9. Estes conjuntos de regras principais são coleções de regras que protegem as suas aplicações Web contra atividades maliciosas.

A firewall de aplicação Web está pré-configurada com CRS 3.0, por predefinição, ou pode optar por utilizar a 2.2.9. A CRS 3.0 oferece menos falsos positivos do que a 2.2.9. É disponibilizada a capacidade de [personalizar a regras para se adequarem às suas necessidades](application-gateway-customize-waf-rules-portal.md). Algumas das vulnerabilidades Web comuns contra as quais a firewall de aplicações Web protege incluem:

- SQL injection protection
- Cruzar a proteção de criação de scripts do site
- Comuns Web proteção contra ataques, como injeção de comandos, contrabando, divisão de respostas HTTP de pedidos HTTP e ataques de inclusão de ficheiros remota
- Proteção contra violações de protocolo HTTP
- Proteção contra anomalias de protocolo HTTP, como em falta alojar o agente do usuário e cabeçalhos de aceitação
- Prevenção de contra bots, crawlers e scanners
- Deteção de aplicação incorretas comuns (por exemplo, Apache, IIS, etc.)

Para obter uma lista mais detalhada das regras e das respetivas proteções, veja [conjuntos de regras de núcleo](#core-rule-sets).


#### <a name="owasp30"></a>OWASP_3.0

O conjunto de regras principais 3.0 fornecido tem 13 grupos de regras, conforme mostrado na tabela seguinte. Cada um destes grupos de regras contém várias regras, que podem ser desativadas.

|RuleGroup|Descrição|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|Contém regras para bloquear métodos (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| Contém regras para proteger contra analisadores de portas e de ambientes.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|Contém regras para proteger contra problemas de protocolos e codificação.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|Contém regras para proteger contra injeção de cabeçalho, contrabando de pedidos e divisão de respostas.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|Contém regras para proteger contra ataques de ficheiros e caminhos.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|Contém regras para proteger contra Inclusão de Ficheiros Remota (RFI).|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|Contém regras para proteger contra Execução de Código Remota.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|Contém regras para proteger contra ataques de injeção PHP.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|Contém regras para proteger contra scripts entre sites.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|Contém regras para proteger contra ataques de injeção de SQL Server.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|Contém regras para proteger contra Ataques de Fixação de Sessão.|

#### <a name="owasp229"></a>OWASP_2.2.9

O conjunto de regras principais 2.2.9 fornecido tem dez grupos de regras, conforme mostrado na tabela seguinte. Cada um destes grupos de regras contém várias regras, que podem ser desativadas.

|RuleGroup|Descrição|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|Contém regras para proteger contra violações de protocolo (carateres inválidos, GET com um corpo de pedido, etc.)|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|Contém regras para proteger contra informações de cabeçalho incorretas.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|Contém regras para proteger contra argumentos ou ficheiros que excedem limitações.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|Contém regras para proteger contra métodos, cabeçalhos e tipos de ficheiros restringidos. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|Contém regras para proteger contra crawlers e scanners da Web.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|Contém regras para proteger contra ataques genéricos (fixação de sessão, inclusão de ficheiros remota, injeção PHP, etc.)|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|Contém regras para proteger contra ataques de injeção de SQL.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|Contém regras para proteger contra scripts entre sites.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|Contém uma regra para proteger contra ataques de caminho tranversais.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|Contém regras para proteger contra trojans de backdoor.|

### <a name="waf-modes"></a>Modos de WAF

A WAF do Gateway de Aplicação pode ser configurada para ser executada nos dois modos seguintes:

* **Modo de deteção** -se configurado para ser executado no modo de deteção, os monitores de WAF do Gateway de aplicação e logs de alertas de ameaça todos os para um ficheiro de registo. Os Diagnósticos de registo do Gateway de Aplicação devem ser ativados através da secção **Diagnósticos**. Também deverá certificar de que o registo WAF está selecionado e ativado. Quando é executada no modo de deteção, a firewall de aplicações Web não bloqueia pedidos de entrada.
* **Modo de prevenção** – Se configurar o Gateway de Aplicação para ser executado no modo de prevenção, este bloqueia ativamente intrusões e ataques detetados pelas respetivas regras. O atacante recebe uma exceção de acesso não autorizado 403 e a ligação é terminada. O modo de prevenção continua a registar os ataques nos registos WAF.

### <a name="anomaly-scoring-mode"></a>Modo de classificação de anomalias 
 
OWASP tem dois modos para decidir se a bloquear o tráfego ou não. Existe um modo tradicional e um modo de anomalias de classificação. No modo de tradicional, qualquer regra de correspondência de tráfego é considerada independentemente se outras regras corresponder à demasiado. Embora seja mais fácil de entender, a falta de informações sobre o número de regras são acionadas por uma solicitação específica é uma das limitações neste modo. Por conseguinte, o modo de classificação de anomalias foi introduzido, que se tornou o padrão com OWASP 3.x. 

No modo de classificação de anomalias, o fato de que as regras descritos na secção anterior ao tráfego não imediatamente significa que o tráfego será bloqueado, partindo do princípio de que a firewall está no modo de prevenção. Regras com uma determinada gravidade (crítico, erro, aviso e tenha em atenção) e, dependendo do que gravidade eles irão aumentar um valor numérico para o pedido de chamada a classificação de anomalia. Por exemplo, uma regra de aviso correspondente irá contribuir com um valor de 3, mas uma regra fundamental correspondente irá contribuir com um valor de 5. 

Existe um limite para a classificação de anomalia sob a qual o tráfego não é cancelado, esse limite está definido para 5. Isso significa que, uma única regra crítica correspondente é suficiente para que o Azure WAF bloqueia um pedido no modo de prevenção (uma vez que a regra crítica aumenta a classificação de anomalia por 5, de acordo com o parágrafo anterior). No entanto, a correspondência de uma regra com um nível de aviso será apenas aumento da anomalia pontuação por 3. Uma vez que o 3 é ainda menor do que o limiar de 5, nenhum tráfego será bloqueado, mesmo que o WAF está no modo de prevenção. 

Tenha em atenção que a mensagem registado quando um tráfego de correspondências de regras WAF incluirá o action_s de campo como "Bloqueado", mas que não significa necessariamente que o tráfego, na verdade, foi bloqueado. Uma classificação de anomalia de 5 ou superior é necessária para realmente bloquear o tráfego.  

### <a name="application-gateway-waf-reports"></a>Monitorização da WAF

É importante monitorizar o estado de funcionamento do gateway de aplicação. Monitorizar o estado de funcionamento da web firewall de aplicações e os aplicativos que esta protege são proporcionados através do registo e da integração com o Azure Monitor, o Centro de segurança do Azure, e registos do Azure Monitor.

![diagnósticos](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Cada registo do gateway de aplicação é integrado no [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).  Desta forma, pode acompanhar as informações de diagnóstico, incluindo alertas e registos da WAF.  Esta capacidade é disponibilizada no recurso do Gateway da Aplicação no portal, no separador **Diagnósticos** ou diretamente através do serviço Azure Monitor. Para saber mais sobre como ativar os registos de diagnóstico para o gateway de aplicação, veja [diagnóstico do Gateway de aplicação](application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

O [Centro de Segurança do Azure](../security-center/security-center-intro.md) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. O gateway de aplicação [está agora integrado no Centro de Segurança do Azure](application-gateway-integration-security-center.md). O Centro de Segurança do Azure analisa o seu ambiente para detetar aplicações Web desprotegidas. Já pode recomendar o WAF do gateway de aplicação para proteger estes recursos vulneráveis. Pode criar diretamente o WAF do gateway de aplicação a partir do Centro de Segurança do Azure.  Estas instâncias do WAF são integradas no Centro de Segurança do Azure e irão enviar alertas e informações do estado de funcionamento para o Centro de Segurança do Azure para os relatórios.

![figura 1](./media/waf-overview/figure1.png)

#### <a name="logging"></a>Registo

A WAF do Gateway de Aplicação fornece relatórios detalhados sobre cada ameaça detetada. Os registos estão integrados no Diagnóstico do Azure e os registos e alertas são registados no formato json. Estes registos podem ser integrados no [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md).

![imageURLroute](./media/waf-overview/waf2.png)

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

A firewall de aplicações Web está disponível num novo WAF SKU. Este SKU só está disponível no modelo de aprovisionamento do Azure Resource Manager e não no modelo de implementação clássica. Além disso, o WAF SKU vem apenas em tamanhos de instância de gateway de aplicação médios e grandes. Todos os limites para o gateway de aplicação também se aplicam ao WAF SKU.

O preço baseia-se no custo de instância de gateway por hora e no custo do processamento de dados. O preço de gateway por hora do WAF SKU é diferente dos custos do Standard SKU e está disponível em [Detalhes dos preços do Gateway da Aplicação](https://azure.microsoft.com/pricing/details/application-gateway/). Os custos de processamento permanecem iguais. Há não por regra ou cobranças de grupo de regra. Pode proteger várias aplicações web atrás da mesma firewall de aplicação web e não lhe são cobradas para dar suporte a várias aplicações.

## <a name="next-steps"></a>Passos Seguintes

Após a aprendizagem sobre WAF, veja [como configurar a firewall de aplicações web no Gateway de aplicação](tutorial-restrict-web-traffic-powershell.md).

