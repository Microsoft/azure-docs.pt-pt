---
title: Configure a proteção de bots para firewall de aplicação web azure (WAF)
description: Saiba como configurar a proteção de bots para firewall de aplicação web (WAF) no Portal de Aplicações Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73516867"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configure a proteção de bots para firewall de aplicação web no Gateway de aplicação Do Azure (Pré-visualização)

Este artigo mostra-lhe como configurar uma regra de proteção de bots no Azure Web Application Firewall (WAF) para Application Gateway utilizando o portal Azure. 

Pode ativar uma regra de proteção de bots gerida definida para o seu WAF bloquear ou registar pedidos de endereços IP maliciosos conhecidos. Os endereços IP são obtidos a partir do feed da Microsoft Threat Intelligence. O Smart Security Graph alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.

> [!NOTE]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Consulte os [Termos De Utilização Suplementares para visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) do Microsoft Azure para mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política básica de WAF para gateway de aplicação seguindo as instruções descritas na [Create Web Application Firewall policies for Application Gateway](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Ativar o conjunto de regras de proteção de bots

1. Na página de política **Basic** que criou anteriormente, em **Definições,** selecione **Regras**.  

2. Na página de detalhes, sob a secção de  **regras de Gestão,** a partir do menu suspenso, selecione a caixa de verificação para a regra de proteção do bot e, em seguida, selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Proteção contra bots](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre regras personalizadas, consulte as regras personalizadas para firewall [v2 de aplicação web no Portal de Aplicações Azure](custom-waf-rules-overview.md).