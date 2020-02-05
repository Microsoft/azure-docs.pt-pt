---
title: WAF na visão geral da proteção do bot de aplicação Azure Gateway
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece uma visão geral da firewall de aplicação web (WAF) sobre a proteção de bots Application Gateway
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027098"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Firewall de aplicação web Azure na visão geral da proteção do bot de aplicação Azure

Cerca de 20% de todo o tráfego da Internet vem de bots maus. Fazem coisas como raspar, digitalizar e procurar vulnerabilidades na sua aplicação web. Quando estes bots são parados na Firewall de Aplicação Web (WAF), não podem atacá-lo. Também não podem usar os seus recursos e serviços, como os seus backends e outras infraestruturas subjacentes.

Pode ativar uma regra de proteção de bots gerida definida para o seu WAF bloquear ou registar pedidos de endereços IP maliciosos conhecidos. Os endereços IP são obtidos a partir do feed da Microsoft Threat Intelligence. O Grafo de Segurança Inteligente da Microsoft é a tecnologia das Informações sobre Ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de Segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Consulte os [Termos De Utilização Suplementares para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure para obter mais detalhes.

## <a name="use-with-owasp-rulesets"></a>Utilização com conjuntos de regras OWASP

Pode utilizar as regras de Proteção bot ao lado de qualquer uma das regras OWASP (2.2.9, 3.0 e 3.1). Apenas uma regra OWASP pode ser utilizada a qualquer momento. O conjunto de regras de proteção de bots contém uma regra adicional que aparece no seu próprio conjunto de regras. É intitulado **Microsoft_BotManagerRuleSet_0.1**, e pode habilitar ou desativá-lo como as outras regras da OWASP.

![Conjunto de regras bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Atualização ruleset

A lista de regras de mitigação de bots de endereços IP conhecidos atualiza várias vezes por dia a partir do feed da Microsoft Threat Intelligence para se manter sincronizado com os bots. As suas aplicações web estão continuamente protegidas, mesmo à medida que os vetores de ataque de bot mudam.

## <a name="next-steps"></a>Passos seguintes

- [Configure a proteção de bots para firewall de aplicação web no Gateway de aplicação Do Azure (Pré-visualização)](bot-protection.md)
