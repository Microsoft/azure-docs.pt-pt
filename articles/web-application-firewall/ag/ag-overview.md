---
title: O que é Azure Web Application Firewall no Gateway de aplicações Azure?
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece uma visão geral da Firewall de Aplicação Web (WAF) no Gateway de aplicações
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 09/16/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 659e7fcdbd2284110282d14fc89bd4d8d5ac2472
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267028"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>O que é Azure Web Application Firewall no Gateway de aplicações Azure?

O Azure Web Application Firewall (WAF) no Azure Application Gateway fornece proteção centralizada das suas aplicações web contra explorações e vulnerabilidades comuns. As aplicações web são cada vez mais direcionadas por ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. A injeção de SQL e a scripting cross-site estão entre os ataques mais comuns.

O WAF on Application Gateway baseia-se no [Conjunto de Regras Fundamentais (CRS)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3.1, 3.0 ou 2.2.9 do Open Web Application Security Project (OWASP). O WAF atualiza automaticamente para incluir proteção contra novas vulnerabilidades, sem necessidade de configuração adicional. 

Todas as funcionalidades da WAF listadas abaixo existem dentro de uma política da WAF. Pode criar várias políticas, e podem ser associadas a um Gateway de Aplicação, a ouvintes individuais ou a regras de encaminhamento baseadas em caminhos numa Gateway de aplicações. Desta forma, pode ter políticas separadas para cada site por trás do seu Gateway de Aplicação, se necessário. Para obter mais informações sobre as políticas da WAF, consulte [Criar uma Política WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > As políticas per-URI WAF estão em Visualização Pública. Isto significa que esta funcionalidade está sujeita aos Termos Complementares de Utilização da Microsoft. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Diagrama WAF do Gateway de Aplicação](../media/ag-overview/waf1.png)

O Application Gateway funciona como controlador de entrega de aplicações (ADC). Oferece segurança de camada de transporte (TLS), anteriormente conhecida como Secure Sockets Layer (SSL), rescisão, afinidade da sessão baseada em cookies, distribuição de carga de robin redondo, encaminhamento baseado em conteúdo, capacidade de hospedar vários websites e melhorias de segurança.

As melhorias de segurança do Gateway de aplicação incluem a gestão da política TLS e o suporte tls de ponta a ponta. A segurança da aplicação é reforçada pela integração da WAF no Application Gateway. A combinação protege as suas aplicações web contra vulnerabilidades comuns. E fornece uma localização central fácil de configurar para gerir.

## <a name="benefits"></a>Benefícios

Esta secção descreve os principais benefícios que a WAF no Gateway de aplicação proporciona.

### <a name="protection"></a>Proteção

* Proteja as suas aplicações web contra vulnerabilidades e ataques da web sem modificar o código back-end.

* Proteja várias aplicações web ao mesmo tempo. Uma instância de Application Gateway pode hospedar até 40 websites que estão protegidos por uma firewall de aplicação web.

* Crie políticas waf personalizadas para diferentes sites por trás da mesma WAF 

* Proteja as suas aplicações web de bots maliciosos com o manual de regras ip reputation (pré-visualização)

### <a name="monitoring"></a>Monitorização

* Monitorize os ataques contra as suas aplicações web utilizando um registo WAF em tempo real. O log é integrado com [o Azure Monitor](../../azure-monitor/overview.md) para rastrear alertas WAF e monitorizar facilmente as tendências.

* O Gateway WAF de aplicação está integrado no Centro de Segurança Azure. O Centro de Segurança fornece uma visão central do estado de segurança de todos os seus recursos Azure.

### <a name="customization"></a>Personalização

* Personalize as regras e grupos de regras da WAF de acordo com os seus requisitos de aplicação e elimine falsos positivos.

* Associar uma Política DE WAF para cada site por trás do seu WAF para permitir a configuração específica do site

* Crie regras personalizadas para atender às necessidades da sua aplicação

## <a name="features"></a>Funcionalidades

- Proteção contra injeção de SQL.
- Proteção de scripts de locais cruzados.
- Proteção contra outros ataques web comuns, tais como injeção de comando, pedido HTTP contrabando, divisão de resposta HTTP e inclusão de ficheiros remotos.
- Proteção contra violações do protocolo HTTP.
- Proteção contra anomalias do protocolo HTTP, tais como falta de agente de utilizador do hospedeiro e aceita cabeçalhos.
- Proteção contra rastejantes e scanners.
- Deteção de configurações comuns de aplicações (por exemplo, Apache e IIS).
- Limites de tamanho de pedido configuráveis com limites inferiores e superiores.
- As listas de exclusão permitem omitir determinados atributos de pedido de uma avaliação da WAF. Um exemplo comum são fichas inseridas no Ative Directory que são usadas para campos de autenticação ou palavra-passe.
- Crie regras personalizadas de acordo com as necessidades específicas das suas aplicações.
- Tráfego de geo-filtro para permitir ou bloquear certos países/regiões de ter acesso às suas aplicações. (pré-visualização)
- Proteja as suas aplicações de bots com as regras de mitigação do bot. (pré-visualização)

## <a name="waf-policy-and-rules"></a>Política e regras da WAF

Para ativar uma Firewall de Aplicação Web no Gateway de aplicações, tem de criar uma política WAF. Esta política é onde todas as regras geridas, regras personalizadas, exclusões e outras personalizações, tais como limite de upload de ficheiros, existem.

Pode configurar uma política da WAF e associar essa política a uma ou mais portas de aplicação para proteção. Uma política da WAF consiste em dois tipos de regras de segurança:

- Regras personalizadas que cria

- Conjuntos de regras geridos que são uma coleção de regras pré-configuradas geridas pelo Azure

Quando ambas estão presentes, as regras personalizadas são processadas antes de processar as regras num conjunto de regras gerido. Uma regra é feita de uma condição de jogo, uma prioridade, e uma ação. Os tipos de ação suportados são: ALLOW, BLOCK e LOG. Pode criar uma política totalmente personalizada que satisfaça os seus requisitos específicos de proteção de aplicações, combinando regras geridas e personalizadas.

As regras dentro de uma política são processadas por ordem prioritária. Prioridade é um número inteiro único que define a ordem das regras para processar. O menor valor inteiro denota uma prioridade maior e essas regras são avaliadas antes de regras com um valor inteiro mais elevado. Uma vez que uma regra é correspondida, a ação correspondente que foi definida na regra é aplicada ao pedido. Uma vez que tal jogo é processado, regras com prioridades mais baixas não são processadas mais longe.

Uma aplicação web entregue pela Application Gateway pode ter uma política de WAF associada a ela a nível global, a nível por site ou a um nível por URI.

### <a name="core-rule-sets"></a>Conjuntos de regras principais

O Application Gateway suporta três conjuntos de regras: CRS 3.1, CRS 3.0 e CRS 2.2.9. Estas regras protegem as suas aplicações web de atividades maliciosas.

Para obter mais informações, consulte [grupos e regras de regras de regras de CRS de firewall de aplicação web](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Regras personalizadas

O Application Gateway também suporta regras personalizadas. Com regras personalizadas, pode criar as suas próprias regras, que são avaliadas para cada pedido que passa através da WAF. Estas regras têm uma prioridade maior do que o resto das regras nos conjuntos de regras geridos. Se um conjunto de condições for cumprido, é tomada uma ação para permitir ou bloquear. 

O operador de geomatch está agora disponível em pré-visualização pública para regras personalizadas. Consulte [as regras personalizadas da Geomatch](custom-waf-rules-overview.md#geomatch-custom-rules-preview) para mais informações.

> [!NOTE]
> O operador de geomatch para regras personalizadas está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Para obter mais informações sobre regras personalizadas, consulte [As Regras Personalizadas para o Gateway de Aplicações.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Mitigação do Bot (pré-visualização)

Um conjunto de regras de proteção bot gerido pode ser habilitado para o seu WAF bloquear ou registar pedidos de endereços IP maliciosos conhecidos, juntamente com o conjunto de regras geridos. Os endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. O Grafo de Segurança Inteligente alimenta as informações sobre ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de Segurança do Azure.

> [!NOTE]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a Proteção de Bot estiver ativada, os pedidos de entrada que correspondam aos IPs do cliente do Bot malicioso são registados no registo firewall, consulte mais informações abaixo. Pode aceder aos registos waf a partir de conta de armazenamento, centro de eventos ou análise de registo. 

### <a name="waf-modes"></a>Modos WAF

O Gateway DE Aplicação WAF pode ser configurado para ser executado nos dois modos seguintes:

* **Modo de deteção**: Monitores e regista todos os alertas de ameaça. Liga os diagnósticos de registo de registo sonoro para o Gateway de Aplicações na secção **De Diagnóstico.** Também deve certificar-se de que o registo WAF é selecionado e ligado. A firewall da aplicação web não bloqueia os pedidos de entrada quando está a funcionar no modo deteção.
* **Modo de prevenção**: Bloqueia intrusões e ataques que as regras detetam. O intruso recebe uma exceção de "acesso não autorizado 403" e a ligação está fechada. O modo de prevenção regista tais ataques nos registos da WAF.

> [!NOTE]
> Recomenda-se que execute um WAF recém-implantado no modo deteção durante um curto período de tempo num ambiente de produção. Isto proporciona a oportunidade de obter [registos de firewall](../../application-gateway/application-gateway-diagnostics.md#firewall-log) e atualizar quaisquer exceções ou [regras personalizadas](./custom-waf-rules-overview.md) antes da transição para o modo de Prevenção. Isto pode ajudar a reduzir a ocorrência de tráfego bloqueado inesperado.

### <a name="anomaly-scoring-mode"></a>Modo de pontuação de anomalia

O OWASP tem dois modos para decidir se bloqueia o tráfego: modo tradicional e modo de pontuação de anomalias.

No modo tradicional, o tráfego que corresponda a qualquer regra é considerado independentemente de quaisquer outras regras. Este modo é fácil de entender. Mas a falta de informação sobre quantas regras correspondem a um pedido específico é uma limitação. Então, o modo de pontuação de anomalia foi introduzido. É o padrão para OWASP 3. *x*.

No modo de pontuação de anomalia, o tráfego que corresponde a qualquer regra não é imediatamente bloqueado quando a firewall está no modo prevenção. As regras têm uma certa gravidade: *Crítico,* *Erro,* *Aviso*ou *Aviso*. Essa gravidade afeta um valor numérico para o pedido, que é chamado de Pontuação de Anomalia. Por exemplo, uma partida de regra de *aviso* contribui com 3 para a pontuação. Um *jogo de regras críticas* contribui com 5.

|Gravidade  |Valor  |
|---------|---------|
|Crítico     |5|
|Erro        |4|
|Aviso      |3|
|Aviso       |2|

Há um limiar de 5 para a Pontuação de Anomalias bloquear o tráfego. Assim, uma única partida *de regras críticas* é suficiente para que o Gateway WAF de aplicação bloqueie um pedido, mesmo no modo prevenção. Mas uma regra de *aviso* só aumenta a Pontuação de Anomalia em 3, o que não é suficiente por si só para bloquear o tráfego.

> [!NOTE]
> A mensagem que é registada quando uma regra da WAF corresponde ao tráfego inclui o valor de ação "Bloqueado". Mas o trânsito só está bloqueado para uma pontuação de anomalia de 5 ou mais.  

### <a name="waf-monitoring"></a>Monitorização da WAF

É importante monitorizar o estado de funcionamento do gateway de aplicação. A monitorização da saúde do seu WAF e as aplicações que protege são suportadas pela integração com os registos do Azure Security Center, Azure Monitor e Azure Monitor.

![Diagrama de diagnósticos WAF gateway de aplicação](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Os registos gateway de aplicação estão integrados com [o Azure Monitor](../../azure-monitor/overview.md). Isto permite-lhe rastrear informações de diagnóstico, incluindo alertas e registos waf. Pode aceder a esta capacidade no separador **Diagnóstico no** recurso 'Gateway' de aplicação no portal ou diretamente através do Azure Monitor. Para saber mais sobre a ativação de registos, consulte os [diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

[O Centro de Segurança](../../security-center/security-center-intro.md) ajuda-o a prevenir, detetar e responder a ameaças. Proporciona uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. O Gateway de Aplicação está [integrado no Centro de Segurança.](../../application-gateway/application-gateway-integration-security-center.md) O Centro de Segurança verifica o seu ambiente para detetar aplicações web desprotegidas. Pode recomendar a Aplicação Gateway WAF para proteger estes recursos vulneráveis. Cria-se as firewalls diretamente do Centro de Segurança. Estes casos da WAF estão integrados no Centro de Segurança. Enviam alertas e informações de saúde ao Centro de Segurança para reportar.

![Janela geral do Centro de Segurança](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

O Microsoft Azure Sentinel é uma solução de gestão de eventos de informação de segurança (SIEM) e de orquestração de segurança (SOAR). O Azure Sentinel fornece análises inteligentes de segurança e inteligência de ameaças em toda a empresa, fornecendo uma única solução para deteção de alerta, visibilidade de ameaças, caça proativa e resposta a ameaças.

Com o livro de eventos de firewall Azure WAF incorporado, você pode obter uma visão geral dos eventos de segurança no seu WAF. Isto inclui eventos, regras compatíveis e bloqueadas, e tudo o resto que é registado nos registos de firewall. Veja mais na sessão abaixo. 


![Livro de eventos de firewall da Azure WAF](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Livro de trabalho do Monitor Azure para WAF

Este livro permite visualizar personalizados eventos WAF relevantes para a segurança em vários painéis filtrantes. Funciona com todos os tipos de WAF, incluindo Porta de Aplicação, Porta Frontal e CDN, e pode ser filtrado com base no tipo WAF ou numa instância waf específica. Importar através do modelo ARM ou modelo de galeria. Para implementar este livro, consulte [o Livro de Trabalho da WAF](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20WAF/Azure%20Monitor%20Workbook).

#### <a name="logging"></a>Registo

O Gateway WAF de aplicação fornece relatórios detalhados sobre cada ameaça que deteta. O registo está integrado com registos Azure Diagnostics. Os alertas são registados no formato .json. Estes registos podem ser integrados com [registos do Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![Aplicação Gateway diagnostica janelas](../media/ag-overview/waf2.png)

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

Os modelos de preços são diferentes para os WAF_v1 e WAF_v2 SKUs. Consulte a página [de preços do Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) para saber mais. 

## <a name="whats-new"></a>Novidades

Para saber quais as novidades com o Azure Web Application Firewall, consulte as atualizações do [Azure](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as regras geridas pela WAF](application-gateway-crs-rulegroups-rules.md)
- Saiba mais sobre [As Regras Personalizadas](custom-waf-rules-overview.md)
- Saiba mais sobre [firewall de aplicação web na porta frontal Azure](../afds/afds-overview.md)
