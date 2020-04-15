---
title: Introdução ao Firewall de aplicação web azure
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece uma visão geral da firewall de aplicação web (WAF) no Gateway de Aplicações
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: e0e5c143e619b1c381a4a618a811883ad189719b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314349"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Firewall de aplicação web azure no gateway de aplicação azure

O Firewall de aplicação web Azure (WAF) no Portal de Aplicações Azure fornece proteção centralizada das suas aplicações web a partir de explorações e vulnerabilidades comuns. As aplicações web são cada vez mais alvo de ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. A injeção SQL e a escrita cruzada estão entre os ataques mais comuns.

WaF on Application Gateway baseia-se no [Core Rule set (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1, 3.0 ou 2.2.9 do Projeto de Segurança de Aplicações Abertas da Web (OWASP). O WAF atualiza automaticamente para incluir proteção contra novas vulnerabilidades, sem necessidade de configuração adicional. 

Todas as características waf listadas abaixo existem dentro de uma política waf. Você pode criar múltiplas políticas, e elas podem ser associadas a um Gateway de Aplicação, a ouvintes individuais, ou a regras de encaminhamento baseadas em caminhos em um Gateway de aplicação. Desta forma, pode ter políticas separadas para cada site por trás do seu Gateway de Aplicação, se necessário. Para obter mais informações sobre as políticas da WAF, consulte [Criar uma Política WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > Por site e por URI AS políticas waf estão em pré-visualização pública. Isto significa que esta funcionalidade está sujeita aos Termos de Utilização Suplementares da Microsoft. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

![Diagrama de Gateway WAF de aplicação](../media/ag-overview/waf1.png)

Application Gateway funciona como controlador de entrega de aplicações (ADC). Oferece segurança em camadas de transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), rescisão, afinidade de sessão baseada em cookies, distribuição de carga de robin redondo, encaminhamento baseado em conteúdo, capacidade de hospedar vários websites e melhorias de segurança.

As melhorias de segurança do Gateway de aplicação incluem a gestão da política TLS e o suporte tLS de ponta a ponta. A segurança da aplicação é reforçada pela integração da WAF no Application Gateway. A combinação protege as suas aplicações web contra vulnerabilidades comuns. E fornece uma localização central fácil de configurar para gerir.

## <a name="benefits"></a>Vantagens

Esta secção descreve os benefícios fundamentais que a WAF no Gateway de Aplicação proporciona.

### <a name="protection"></a>Proteção

* Proteja as suas aplicações web de vulnerabilidades web e ataques sem modificação para o código final.

* Proteja várias aplicações web ao mesmo tempo. Um exemplo de Application Gateway pode hospedar até 40 websites que estão protegidos por uma firewall de aplicação web.

* Crie políticas de WAF personalizadas para diferentes sites por trás da mesma WAF 

* Proteja as suas aplicações web de bots maliciosos com o conjunto de regras IP Reputation (pré-visualização)

### <a name="monitoring"></a>Monitorização

* Monitorize os ataques contra as suas aplicações web utilizando um registo WAF em tempo real. O registo está integrado com o [Azure Monitor](../../azure-monitor/overview.md) para monitorizar os alertas WAF e monitorizar facilmente as tendências.

* O Application Gateway WAF está integrado no Azure Security Center. O Centro de Segurança fornece uma visão central do estado de segurança de todos os seus recursos Azure.

### <a name="customization"></a>Personalização

* Personalize as regras waf e os grupos de regras para atender aos seus requisitos de aplicação e elimine falsos positivos.

* Associe uma Política WAF para cada site por trás do seu WAF para permitir a configuração específica do site

* Crie regras personalizadas para atender às necessidades da sua aplicação

## <a name="features"></a>Funcionalidades

- Proteção contra injeção SQL.
- Proteção de scripts transversais.
- Proteção contra outros ataques comuns da web, tais como injeção de comando, http solicitar contrabando, divisão de resposta HTTP e inclusão de ficheiros remotos.
- Proteção contra violações do protocolo HTTP.
- Proteção contra anomalias do protocolo HTTP, tais como agente-utilizador hospedeiro desaparecido e cabeçalhos aceite.
- Proteção contra rastejantes e scanners.
- Deteção de configurações de aplicações comuns (por exemplo, Apache e IIS).
- Limites de tamanho de pedido configuráveis com limites inferiores e superiores.
- As listas de exclusão permitem-lhe omiti-lo de determinados atributos de pedido de uma avaliação waf. Um exemplo comum são as fichas inseridas no Diretório Ativo que são usadas para campos de autenticação ou palavra-passe.
- Crie regras personalizadas para atender às necessidades específicas das suas aplicações.
- Tráfego de geofiltração para permitir ou bloquear certos países de ter acesso às suas aplicações. (pré-visualização)
- Proteja as suas aplicações de bots com a regra de mitigação do bot. (pré-visualização)

## <a name="waf-policy"></a>Política waf

Para ativar uma firewall de aplicação web num Gateway de aplicação, tem de criar uma Política WAF. Esta Política é onde todas as regras geridas, regras personalizadas, exclusões e outras personalizações, tais como o limite de upload de ficheiros existem. 

### <a name="core-rule-sets"></a>Conjuntos de regras principais

Application Gateway suporta três conjuntos de regras: CRS 3.1, CRS 3.0 e CRS 2.2.9. Estas regras protegem as suas aplicações web de atividades maliciosas.

Para mais informações, consulte os grupos e regras de regras CRS da [firewall de aplicação web](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Regras personalizadas

Application Gateway também suporta regras personalizadas. Com regras personalizadas, pode criar as suas próprias regras, que são avaliadas para cada pedido que passa pela WAF. Estas regras têm uma prioridade maior do que as restantes regras dos conjuntos de regras geridos. Se for em condições, é tomada uma ação para permitir ou bloquear. 

O operador de geomatch está agora disponível em pré-visualização pública para regras personalizadas. Por favor, consulte [as regras personalizadas](custom-waf-rules-overview.md#geomatch-custom-rules-preview) do geomatch para mais informações.

> [!NOTE]
> O operador de geomatch para regras personalizadas está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Para obter mais informações sobre as regras personalizadas, consulte Regras Personalizadas para Gateway de [Aplicação.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Mitigação de Bot (pré-visualização)

Um conjunto de regras de proteção bot gerido pode ser ativado para que o seu WAF bloqueie ou faça log solicitações de endereços IP maliciosos conhecidos, juntamente com o conjunto de regras gerido. Os endereços IP são obtidos a partir do feed da Microsoft Threat Intelligence. O Smart Security Graph alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.

> [!NOTE]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a Proteção bot estiver ativada, os pedidos de entrada que correspondam aos IPs do cliente da Malicious Bot estão registados no registo firewall, consulte mais informações abaixo. Pode aceder a registos WAF a partir de conta de armazenamento, centro de eventos ou análise de registo. 

### <a name="waf-modes"></a>Modos WAF

O Gateway WAF de aplicação pode ser configurado para ser executado nos dois modos seguintes:

* **Modo de deteção**: Monitores e regista todos os alertas de ameaça. Ligue os diagnósticos de registo para o Gateway de Aplicação na secção **de Diagnósticos.** Deve também certificar-se de que o registo WAF é selecionado e ligado. A firewall da aplicação web não bloqueia os pedidos de entrada quando está a funcionar no modo de Deteção.
* **Modo de prevenção**: Bloqueia intrusões e ataques que as regras detetam. O agressor recebe uma exceção de "403 acessos não autorizados" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos waf.

> [!NOTE]
> Recomenda-se que execute um WAF recém-implantado no modo deteção por um curto período de tempo num ambiente de produção. Isto proporciona a oportunidade de obter [registos](../../application-gateway/application-gateway-diagnostics.md#firewall-log) de firewall e atualizar quaisquer exceções ou [regras personalizadas](./custom-waf-rules-overview.md) antes da transição para o modo de Prevenção. Isto pode ajudar a reduzir a ocorrência de tráfego bloqueado inesperado.

### <a name="anomaly-scoring-mode"></a>Modo de pontuação de anomalia

OOWASP tem dois modos para decidir se bloqueia o tráfego: Modo tradicional e modo de pontuação de anomalia.

No modo Tradicional, o tráfego que corresponda a qualquer regra é considerado independentemente de qualquer outra regra que corresponda. Este modo é fácil de entender. Mas a falta de informação sobre quantas regras correspondem a um pedido específico é uma limitação. Então, o modo Anomaly Scoring foi introduzido. É o padrão para OWASP 3. *x*.

No modo Depontuação de Anomalias, o tráfego que corresponda a qualquer regra não é imediatamente bloqueado quando a firewall está em modo de Prevenção. As regras têm uma certa gravidade: *Crítica,* *Erro,* *Aviso*ou *Aviso.* Esta gravidade afeta um valor numérico para o pedido, que é chamado de Pontuação de Anomalia. Por exemplo, uma partida de regra de *aviso* contribui 3 para a pontuação. Uma regra *crítica* contribui com 5.

|Gravidade  |Valor  |
|---------|---------|
|Crítica     |5|
|Erro        |4|
|Aviso      |3|
|Aviso prévio       |2|

Há um limiar de 5 para o "Anomaly Score" bloquear o tráfego. Assim, uma única regra *crítica* é suficiente para que o Gateway WAF de aplicação bloqueie um pedido, mesmo em modo de Prevenção. Mas uma regra de *advertência* só aumenta a Pontuação de Anomalia em 3, o que não é suficiente por si só para bloquear o tráfego.

> [!NOTE]
> A mensagem que é registada quando uma regra waf corresponde ao tráfego inclui o valor de ação "Bloqueado". Mas o tráfego só está bloqueado para uma Pontuação de Anomalia de 5 ou mais.  

### <a name="waf-monitoring"></a>Monitorização waf

É importante monitorizar o estado de funcionamento do gateway de aplicação. A monitorização da saúde do seu WAF e as aplicações que protege são suportadas pela integração com os registos do Azure Security Center, Azure Monitor e Azure Monitor.

![Diagrama de diagnósticos WAF de gateway de aplicação](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Os registos de gateway de aplicação estão integrados com o [Monitor Azure.](../../azure-monitor/overview.md) Isto permite-lhe rastrear informações de diagnóstico, incluindo alertas waf e registos. Pode aceder a esta capacidade no separador **Diagnostics** no recurso Application Gateway no portal ou diretamente através do Monitor Azure. Para saber mais sobre a ativação de registos, consulte [diagnósticos de Gateway](../../application-gateway/application-gateway-diagnostics.md)de aplicação .

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Centro](../../security-center/security-center-intro.md) de Segurança ajuda-o a prevenir, detetar e responder a ameaças. Proporciona uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. O Application Gateway está [integrado no Centro de Segurança.](../../application-gateway/application-gateway-integration-security-center.md) O Security Center verifica o seu ambiente para detetar aplicações web desprotegidas. Pode recomendar o Application Gateway WAF para proteger estes recursos vulneráveis. Cria as firewalls diretamente do Centro de Segurança. Estes casos waf estão integrados com o Centro de Segurança. Enviam alertas e informações de saúde ao Centro de Segurança para reportagem.

![Janela geral do Centro de Segurança](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

O Microsoft Azure Sentinel é uma solução de gestão de eventos de informação de segurança (SIEM) escalável, nativa da nuvem, e uma solução automatizada de resposta automatizada de orquestração de segurança (SOAR). O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças.

Com o livro de trabalho de eventos de firewall Azure WAF incorporado, você pode obter uma visão geral dos eventos de segurança no seu WAF. Isto inclui eventos, regras correspondidas e bloqueadas, e tudo o resto que é registado nos registos de firewall. Veja mais sobre a sessão de sessão de sessão de registo abaixo. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Registo

Aplicação Gateway WAF fornece relatórios detalhados sobre cada ameaça que deteta. A exploração madeireira está integrada com registos de Diagnóstico Sinuoso Azure. Os alertas são registados no formato .json. Estes registos podem ser integrados com [registos do Monitor Azure](../../azure-monitor/insights/azure-networking-analytics.md).

![Aplicação Gateway diagnósticos logs janelas](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Preços da SKU da WAF do Gateway de Aplicação

Os modelos de preços são diferentes para as WAF_v1 e WAF_v2 SKUs. Consulte a página de preços do Gateway de [aplicação](https://azure.microsoft.com/pricing/details/application-gateway/) para saber mais. 

## <a name="next-steps"></a>Passos seguintes

- Começar por [Criar uma política waf](create-waf-policy-ag.md)
- Saiba mais sobre [as regras geridas pela WAF](application-gateway-crs-rulegroups-rules.md)
- Saiba mais sobre [regras personalizadas](custom-waf-rules-overview.md)
- Saiba mais sobre firewall de [aplicação web na porta da frente do Azure](../afds/afds-overview.md)

