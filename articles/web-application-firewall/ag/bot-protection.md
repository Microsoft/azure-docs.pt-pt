---
title: Configurar a proteção de bot para o Firewall do aplicativo Web do Azure (WAF)
description: Saiba como configurar a proteção de bot para o WAF (firewall do aplicativo Web) no gateway Aplicativo Azure.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516867"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configurar a proteção de bot para o Firewall do aplicativo Web no gateway de Aplicativo Azure (versão prévia)

Este artigo mostra como configurar uma regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para o gateway de aplicativo usando o portal do Azure. 

Você pode habilitar um conjunto de regras de proteção de bot gerenciado para seu WAF para bloquear ou registrar solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP são originados do feed do Microsoft Threat Intelligence. O Grafo de Segurança Inteligente da Microsoft é a tecnologia das Informações sobre Ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de Segurança do Azure.

> [!NOTE]
> O conjunto de regras de proteção de bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Consulte os [termos de uso complementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política básica de WAF para o gateway de aplicativo seguindo as instruções descritas em [criar políticas de firewall do aplicativo Web para o gateway de aplicativo](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar conjunto de regras de proteção de bot

1. Na página de política **básica** que você criou anteriormente, em **configurações**, selecione **regras**.  

2. Na página de detalhes, na seção **gerenciar regras** , no menu suspenso, marque a caixa de seleção da regra de proteção de bot e, em seguida, selecione **salvar**.

> [!div class="mx-imgBorder"]
> ](../media/bot-protection/bot-protection.png) de proteção de ![bot

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre regras personalizadas, consulte [Custom Rules for Web Application Firewall v2 on aplicativo Azure gateway](custom-waf-rules-overview.md).