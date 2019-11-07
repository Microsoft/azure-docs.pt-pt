---
title: Introdução ao firewall do aplicativo Web do Azure no gateway de Aplicativo Azure
description: Este artigo fornece uma visão geral do WAF (firewall do aplicativo Web) no gateway de aplicativo
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/05/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 6d073648dc908cbbe40962f7ba079abcfe85ce45
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607291"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Firewall do aplicativo Web do Azure no gateway de Aplicativo Azure

O WAF (firewall do aplicativo Web) do Azure no gateway Aplicativo Azure fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. Os aplicativos Web são cada vez mais direcionados a ataques mal-intencionados que exploram vulnerabilidades comumente conhecidas. A injeção de SQL e o script entre sites estão entre os ataques mais comuns.

O WAF no gateway de aplicativo baseia-se no [CRS (Core Rule Set)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1, 3,0 ou 2.2.9 do projeto de segurança de aplicativo Web aberto (OWASP). O WAF é atualizado automaticamente para incluir proteção contra novas vulnerabilidades, sem necessidade de configuração adicional. 

Todos os recursos de WAF listados abaixo existem dentro de uma política de WAF. Você pode criar várias políticas e elas podem ser associadas a um gateway de aplicativo, a ouvintes individuais ou a regras de roteamento com base em caminhos em um gateway de aplicativo. Dessa forma, você pode ter políticas separadas para cada site por trás do seu gateway de aplicativo, se necessário. Para obter mais informações sobre políticas de WAF, consulte [criar uma política de WAF](create-waf-policy-ag.md).

   > [!NOTE]
   > As políticas de WAF por site e por URI estão em visualização pública. Isso significa que esse recurso está sujeito aos termos de uso suplementares da Microsoft. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Diagrama de WAF do gateway de aplicativo](../media/ag-overview/waf1.png)

O gateway de aplicativo funciona como um ADC (controlador de entrega de aplicativos). Ele oferece encerramento de protocolo SSL (SSL), afinidade de sessão baseada em cookie, distribuição de carga Round Robin, roteamento baseado em conteúdo, capacidade de hospedar vários sites e aprimoramentos de segurança.

Os aprimoramentos de segurança do gateway de aplicativo incluem o gerenciamento de política SSL e o suporte a SSL de ponta a ponta. A segurança do aplicativo é reforçada pela integração do WAF ao gateway de aplicativo. A combinação protege seus aplicativos Web contra vulnerabilidades comuns. E fornece um local central fácil de configurar para gerenciar o.

## <a name="benefits"></a>Benefícios

Esta seção descreve os principais benefícios que o WAF no gateway de aplicativo fornece.

### <a name="protection"></a>Proteção

* Proteja seus aplicativos Web contra vulnerabilidades e ataques da Web sem modificações no código de back-end.

* Proteja vários aplicativos Web ao mesmo tempo. Uma instância do gateway de aplicativo pode hospedar de até 40 sites que são protegidos por um firewall do aplicativo Web.

* Criar políticas de WAF personalizadas para sites diferentes por trás do mesmo WAF 

* Proteger seus aplicativos Web contra bots mal-intencionados com o conjunto de regras de reputação de IP (versão prévia)

### <a name="monitoring"></a>Monitorização

* Monitore ataques contra seus aplicativos Web usando um log de WAF em tempo real. O log é integrado com [Azure monitor](../../azure-monitor/overview.md) para rastrear alertas do WAF e monitorar facilmente as tendências.

* O WAF do gateway de aplicativo é integrado à central de segurança do Azure. A central de segurança fornece uma exibição central do estado de segurança de todos os seus recursos do Azure.

### <a name="customization"></a>Personalização

* Personalize regras de WAF e grupos de regras para atender aos requisitos do seu aplicativo e eliminar falsos positivos.

* Associar uma política de WAF para cada site por trás de seu WAF para permitir a configuração específica do site

* Crie regras personalizadas para atender às necessidades do seu aplicativo

## <a name="features"></a>Funcionalidades

- Proteção de injeção de SQL.
- Proteção de scripts entre sites.
- Proteção contra outros ataques comuns da Web, como injeção de comando, indesejada de solicitação HTTP, divisão de resposta HTTP e inclusão de arquivo remoto.
- Proteção contra violações de protocolo HTTP.
- Proteção contra anomalias de protocolo HTTP, como o agente de usuário de host ausente e os cabeçalhos de aceitação.
- Proteção contra rastreadores e scanners.
- Detecção de incorretas configurações de aplicativo comuns (por exemplo, Apache e IIS).
- Limites de tamanho de solicitação configuráveis com limites inferiores e superiores.
- As listas de exclusão permitem omitir determinados atributos de solicitação de uma avaliação do WAF. Um exemplo comum são os tokens inseridos Active Directory que são usados para os campos de autenticação ou senha.
- Crie regras personalizadas para atender às necessidades específicas de seus aplicativos.
- O tráfego de filtro geográfico para permitir ou impedir que determinados países obtenham acesso aos seus aplicativos. (pré-visualização)
- Proteja seus aplicativos de bots com o conjunto de regras de mitigação de bot. (pré-visualização)

## <a name="waf-policy"></a>Política de WAF

Para habilitar um firewall do aplicativo Web em um gateway de aplicativo, você deve criar uma política de WAF. Essa política é onde existem todas as regras gerenciadas, regras personalizadas, exclusões e outras personalizações, como o limite de carregamento de arquivos. 

### <a name="core-rule-sets"></a>Conjuntos de regras principais

O gateway de aplicativo dá suporte a três conjuntos de regras: CRS 3,1, CRS 3,0 e CRS 2.2.9. Essas regras protegem seus aplicativos Web contra atividades mal-intencionadas.

Para obter mais informações, consulte [regras e grupos de regras CRS do firewall do aplicativo Web](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Regras personalizadas

O gateway de aplicativo também dá suporte a regras personalizadas. Com as regras personalizadas, você pode criar suas próprias regras, que são avaliadas para cada solicitação que passa pelo WAF. Essas regras têm uma prioridade mais alta do que o restante das regras nos conjuntos de regras gerenciadas. Se um conjunto de condições for atendido, será executada uma ação para permitir ou bloquear. 

Para obter mais informações, consulte [Custom Rules for Application Gateway.](custom-waf-rules-overview.md)

### <a name="bot-mitigation-preview"></a>Mitigação de bot (visualização)

Um conjunto de regras de proteção de bot gerenciado pode ser habilitado para que seu WAF bloqueie ou registre solicitações de endereços IP mal-intencionados conhecidos, juntamente com o conjunto de regras gerenciado. Os endereços IP são originados do feed do Microsoft Threat Intelligence. O Grafo de Segurança Inteligente da Microsoft é a tecnologia das Informações sobre Ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de Segurança do Azure.

> [!NOTE]
> O conjunto de regras de proteção de bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a proteção de bot estiver habilitada, as solicitações de entrada que corresponderem aos IPs de cliente do bot mal-intencionado serão registradas no log do firewall, Confira mais informações abaixo. Você pode acessar os logs do WAF da conta de armazenamento, Hub de eventos ou log Analytics. 

### <a name="waf-modes"></a>Modos de WAF

O WAF do gateway de aplicativo pode ser configurado para ser executado nos dois modos a seguir:

* **Modo de detecção**: monitora e registra todos os alertas de ameaça. Você ativa o diagnóstico de log para o gateway de aplicativo na seção **diagnóstico** . Você também deve certificar-se de que o log WAF está selecionado e ativado. O Firewall do aplicativo Web não bloqueia solicitações de entrada quando está operando no modo de detecção.
* **Modo de prevenção**: bloqueia invasões e ataques que as regras detectam. O invasor recebe uma exceção de "acesso não autorizado 403" e a conexão é fechada. O modo de prevenção registra esses ataques nos logs do WAF.

> [!NOTE]
> É recomendável que você execute um WAF implantado recentemente no modo de detecção por um curto período de tempo em um ambiente de produção. Isso fornece a oportunidade de obter [logs de firewall](../../application-gateway/application-gateway-diagnostics.md#firewall-log) e atualizar as exceções ou [regras personalizadas](./custom-waf-rules-overview.md) antes de fazer a transição para o modo de prevenção. Isso pode ajudar a reduzir a ocorrência de tráfego bloqueado inesperado.

### <a name="anomaly-scoring-mode"></a>Modo de Pontuação de anomalias

O OWASP tem dois modos para decidir se deve bloquear o tráfego: modo tradicional e a pontuação de anomalias.

No modo tradicional, o tráfego que corresponde a qualquer regra é considerado independentemente de qualquer outra correspondência de regra. Esse modo é fácil de entender. Mas a falta de informações sobre quantas regras correspondem a uma solicitação específica é uma limitação. Portanto, o modo de Pontuação de anomalias foi introduzido. É o padrão para OWASP 3. *x*.

No modo de Pontuação de anomalias, o tráfego que corresponde a qualquer regra não é bloqueado imediatamente quando o firewall está no modo de prevenção. As regras têm uma determinada gravidade: *crítico*, *erro*, *aviso*ou *aviso*. Essa gravidade afeta um valor numérico para a solicitação, que é chamado de Pontuação de anomalias. Por exemplo, uma regra de *aviso* corresponde a contribuição 3 para a pontuação. Uma regra *crítica* corresponde à 5.

|Gravidade  |Valor  |
|---------|---------|
|Crítica     |5|
|Erro        |4|
|Aviso      |3|
|Observa       |2|

Há um limite de 5 para a pontuação de anomalias bloquear o tráfego. Portanto, uma única correspondência de regra *crítica* é suficiente para que o WAF do gateway de aplicativo bloqueie uma solicitação, mesmo no modo de prevenção. Mas uma correspondência de regra de *aviso* aumenta apenas a pontuação de anomalias em 3, o que não é suficiente por si só para bloquear o tráfego.

> [!NOTE]
> A mensagem registrada quando uma regra WAF corresponde ao tráfego inclui o valor de ação "bloqueado". Mas o tráfego é, na verdade, bloqueado apenas para uma pontuação de anomalias de 5 ou mais.  

### <a name="waf-monitoring"></a>Monitoramento de WAF

É importante monitorizar o estado de funcionamento do gateway de aplicação. O monitoramento da integridade do seu WAF e dos aplicativos que ele protege tem suporte pela integração com os logs da central de segurança do Azure, do Azure Monitor e do Azure Monitor.

![Diagrama do diagnóstico de WAF do gateway de aplicativo](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Os logs do gateway de aplicativo são integrados ao [Azure monitor](../../azure-monitor/overview.md). Isso permite que você acompanhe informações de diagnóstico, incluindo alertas e logs do WAF. Você pode acessar esse recurso na guia **diagnóstico** no recurso de gateway de aplicativo no portal ou diretamente por meio de Azure monitor. Para saber mais sobre como habilitar logs, consulte [diagnóstico do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md).

#### <a name="azure-security-center"></a>Centro de Segurança do Azure

A [central de segurança](../../security-center/security-center-intro.md) ajuda você a prevenir, detectar e responder a ameaças. Ele fornece maior visibilidade e controle sobre a segurança dos recursos do Azure. O gateway de aplicativo é [integrado à central de segurança](../../application-gateway/application-gateway-integration-security-center.md). A central de segurança verifica seu ambiente para detectar aplicativos Web desprotegidos. Ele pode recomendar o WAF do gateway de aplicativo para proteger esses recursos vulneráveis. Você cria os firewalls diretamente na central de segurança. Essas instâncias WAF são integradas à central de segurança. Eles enviam informações de alertas e de integridade para a central de segurança para relatórios.

![Janela Visão geral da central de segurança](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel é uma solução de disparar (gerenciamento de eventos de informações de segurança) escalonável, nativa de nuvem e de ingestão de segurança (SIEM). O Azure Sentinel fornece análise de segurança inteligente e inteligência contra ameaças em toda a empresa, fornecendo uma solução única para detecção de alertas, visibilidade de ameaças, busca proativa e resposta a ameaças.

Com a pasta de trabalho interna de eventos do firewall WAF do Azure, você pode obter uma visão geral dos eventos de segurança em seu WAF. Isso inclui eventos, regras de correspondência e bloqueadas e todo o resto que é registrado nos logs do firewall. Veja mais sobre registro em log abaixo. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Registo

O WAF do gateway de aplicativo fornece relatórios detalhados sobre cada ameaça detectada. O registro em log é integrado a logs de Diagnóstico do Azure. Os alertas são registrados no formato. JSON. Esses logs podem ser integrados a [logs de Azure monitor](../../azure-monitor/insights/azure-networking-analytics.md).

![Janelas de logs de diagnóstico do gateway de aplicativo](../media/ag-overview/waf2.png)

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

Os modelos de preços são diferentes para as SKUs WAF_v1 e WAF_v2. Consulte a página de [preços do gateway de aplicativo](https://azure.microsoft.com/pricing/details/application-gateway/) para saber mais. 

## <a name="next-steps"></a>Passos seguintes

- Comece [criando uma política de WAF](create-waf-policy-ag.md)
- Saiba mais sobre [as regras gerenciadas do WAF](application-gateway-crs-rulegroups-rules.md)
- Saiba mais sobre [regras personalizadas](custom-waf-rules-overview.md)
- Saiba mais sobre o [Firewall do aplicativo Web na porta frontal do Azure](../afds/afds-overview.md)

