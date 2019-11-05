---
title: Personalizar regras de firewall do aplicativo Web no gateway Aplicativo Azure-CLI do Azure
description: Este artigo fornece informações sobre como personalizar as regras de firewall do aplicativo Web no gateway de aplicativo com o CLI do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 08/23/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 58cc339e8f849a71fb01973e99dba936f6b53a8c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516945"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Personalizar regras de firewall do aplicativo Web usando o CLI do Azure

O WAF (firewall do aplicativo Web) Aplicativo Azure Gateway fornece proteção para aplicativos Web. Essas proteções são fornecidas pelo CRS (conjunto de regras principais) do OWASP (projeto de segurança de aplicativo Web aberto). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o gateway de aplicativo fornece a capacidade de personalizar grupos de regras e regras. Para obter mais informações sobre grupos de regras e regras específicas, consulte [lista de regras e grupos de regras CRS de firewall do aplicativo Web](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Exibir regras e grupos de regras

Os exemplos de código a seguir mostram como exibir regras e grupos de regras que são configuráveis.

### <a name="view-rule-groups"></a>Exibir grupos de regras

O exemplo a seguir mostra como exibir os grupos de regras:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Exibir regras em um grupo de regras

O exemplo a seguir mostra como exibir regras em um grupo de regras especificado:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A saída a seguir é uma resposta truncada do exemplo anterior:

```json
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Desabilitar regras

O exemplo a seguir desabilita as regras `910018` e `910017` em um gateway de aplicativo:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que o WAF bloqueie a solicitação no modo de prevenção (no modo de detecção, elas são registradas como exceções). Eles não podem ser configurados ou desabilitados:

* Falha ao analisar os resultados do corpo da solicitação na solicitação sendo bloqueada, a menos que a inspeção do corpo seja desativada (XML, JSON, dados de formulário)
* O comprimento de dados do corpo da solicitação (sem arquivos) é maior que o limite configurado
* O corpo da solicitação (incluindo arquivos) é maior que o limite
* Ocorreu um erro interno no mecanismo de WAF

Específico do CRS 3. x:

* Limite de Pontuação de anomalias de entrada excedido

## <a name="next-steps"></a>Passos seguintes

Depois de configurar suas regras desabilitadas, você pode aprender a exibir os logs do WAF. Para obter mais informações, consulte [diagnóstico do gateway de aplicativo](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
