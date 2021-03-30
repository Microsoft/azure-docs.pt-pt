---
title: Configure a proteção do bot para firewall de aplicação web com porta frontal Azure (pré-visualização)
description: Aprenda a configurar a regra de proteção do bot no Azure Web Application Firewall (WAF) para porta frontal utilizando o portal Azure.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267011"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configure a proteção de bots para firewall de aplicação web (pré-visualização)
Este artigo mostra-lhe como configurar a regra de proteção de bots no Azure Web Application Firewall (WAF) para porta frontal utilizando o portal Azure. A regra de proteção do bot também pode ser configurada usando o modelo CLI, Azure PowerShell ou Azure Resource Manager.

> [!IMPORTANT]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política básica de WAF para porta frontal seguindo as instruções descritas na [Criar uma política WAF para a porta frontal Azure utilizando o portal Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Ativar o conjunto de regras de proteção de bot

Na página **'Regras Geridas'** ao criar uma política de Firewall para aplicações na Web, primeiro encontre a secção **definida por regras geridas,** selecione a caixa de verificação em frente à regra **Microsoft_BotManager_1.0** do menu suspenso e, em seguida, selecione **Review + Create**.

   ![Regra de proteção de bots](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como monitorizar a [WAF](waf-front-door-monitor.md).
