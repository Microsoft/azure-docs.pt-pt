---
title: Configure a proteção de bots para firewall de aplicação web Azure (WAF)
description: Saiba como configurar a proteção de bots para firewall de aplicação web (WAF) no Gateway de aplicações Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "73516867"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configurar a proteção contra bots para a Firewall de Aplicações Web no Gateway de Aplicação do Azure (Pré-visualização)

Este artigo mostra-lhe como configurar uma regra de proteção de bots no Azure Web Application Firewall (WAF) para o Gateway de aplicações utilizando o portal Azure. 

Pode ativar um conjunto de regras de proteção contra bots gerido para a WAF bloquear ou registar pedidos de endereços IP maliciosos conhecidos. Os endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. O Grafo de Segurança Inteligente alimenta as informações sobre ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de Segurança do Azure.

> [!NOTE]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Consulte os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política básica de WAF para o Gateway de aplicações seguindo as instruções descritas nas [políticas de Firewall de Aplicação Web para Gateway de aplicação](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Ativar o conjunto de regras de proteção de bot

1. Na página de política **básica** que criou anteriormente, em **Definições**, selecione **Regras**.  

2. Na página de detalhes, na secção  **Regras De Gestão,**   a partir do menu suspenso, selecione a caixa de verificação para a regra de Proteção de bots e, em seguida, selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Proteção contra bots](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre regras personalizadas, consulte [as regras personalizadas para Web Application Firewall v2 no Azure Application Gateway](custom-waf-rules-overview.md).