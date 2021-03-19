---
title: Personalize as regras usando CLI - Azure Web Application Firewall
description: Este artigo fornece informações sobre como personalizar as regras de Firewall de Aplicação Web em Gateway de aplicação com o Azure CLI.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74048522"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Personalize as regras de Firewall de aplicações web usando o Azure CLI

O Azure Application Gateway Web Application Firewall (WAF) fornece proteção para aplicações web. Estas proteções são fornecidas pelo Open Web Application Security Project (OWASP) Core Rule set (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esta razão, o Application Gateway fornece a capacidade de personalizar grupos de regras e regras. Para obter mais informações sobre os grupos e regras específicas de regras, consulte [a Lista de grupos e regras de regras do CRS do Firewall de Aplicação Web.](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Ver grupos de regras e regras

Os seguintes exemplos de código mostram como ver regras e grupos de regras que são configuráveis.

### <a name="view-rule-groups"></a>Ver grupos de regras

O exemplo a seguir mostra como ver os grupos de regras:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Segue-se uma resposta truncada do exemplo anterior:

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

### <a name="view-rules-in-a-rule-group"></a>Ver regras num grupo de regras

O exemplo a seguir mostra como ver as regras num grupo de regras especificado:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Segue-se uma resposta truncada do exemplo anterior:

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

## <a name="disable-rules"></a>Desativar regras

O exemplo a seguir desativa as regras `910018` e num gateway de `910017` aplicações:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que a WAF bloqueie o pedido durante o modo de prevenção (no Modo de Deteção são registados como exceções). Estes não podem ser configurados ou desativados:

* A não análise do organismo de pedido resulta no bloqueio do pedido, a menos que a inspeção corporal seja desligada (XML, JSON, dados do formulário)
* O comprimento dos dados do corpo do pedido (sem ficheiros) é maior do que o limite configurado
* O corpo de pedido (incluindo ficheiros) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedeu limiar

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as suas regras desativadas, pode aprender a ver os seus registos WAF. Para mais informações, consulte os [diagnósticos do Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
