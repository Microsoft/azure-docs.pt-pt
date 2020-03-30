---
title: Personalize regras usando cli - Firewall de aplicação web azure
description: Este artigo fornece informações sobre como personalizar as regras de Firewall de aplicação web no Gateway de Aplicações com o ClI Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048522"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Personalize as regras de firewall de aplicações web usando o Azure CLI

O Firewall de aplicação web da Aplicação Gateway (WAF) da Aplicação Azure Application (WAF) fornece proteção para aplicações web. Estas proteções são fornecidas pelo Conjunto de Regras de Regra do Núcleo de Segurança de Aplicações Abertas (OWASP) (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esta razão, o Application Gateway fornece a capacidade de personalizar grupos e regras de regras. Para obter mais informações sobre os grupos e regras de regras específicas, consulte [lista de grupos e regras](application-gateway-crs-rulegroups-rules.md)de regras DO FIREWALL de aplicação web .

## <a name="view-rule-groups-and-rules"></a>Ver grupos de regras e regras

Os seguintes exemplos de código mostram como ver regras e grupos de regras que são configuráveis.

### <a name="view-rule-groups"></a>Ver grupos de regras

O exemplo que se segue mostra como ver os grupos de regras:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

A seguinte saída é uma resposta truncada do exemplo anterior:

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

### <a name="view-rules-in-a-rule-group"></a>Ver regras em um grupo de regras

O exemplo que se segue mostra como ver regras num grupo de regras especificado:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

A seguinte saída é uma resposta truncada do exemplo anterior:

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

## <a name="disable-rules"></a>Regras de desativação

O exemplo seguinte `910018` desativa as regras e `910017` numa porta de aplicação:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista que se segue contém condições que fazem com que o WAF bloqueie o pedido enquanto está no Modo de Prevenção (no Modo de Deteção são registados como exceções). Estes não podem ser configurados ou desativados:

* A não análise do organismo de pedido resulta no bloqueio do pedido, a menos que a inspeção corporal seja desligada (XML, JSON, dados de formulário)
* O comprimento de dados do corpo de pedidos (sem ficheiros) é maior do que o limite configurado
* O organismo de pedido (incluindo ficheiros) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedida limiar

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as suas regras de incapacidade, pode aprender a ver os seus registos WAF. Para mais informações, consulte [diagnósticos de Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)de aplicação .

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
