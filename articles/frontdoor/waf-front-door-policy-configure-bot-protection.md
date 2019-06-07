---
title: Configurar a proteção de bot de firewall de aplicações web com o Azure desde início (pré-visualização)
description: Aprenda a firewall de aplicações web (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481628"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurar a proteção de bot de firewall de aplicações web (pré-visualização)
Este artigo mostra-lhe como configurar regra de proteção de bot no firewall de aplicações web do Azure (WAF) para a porta de entrada com o modelo de CLI do Azure, Azure PowerShell ou do Azure Resource Manager.

Um conjunto de regras de proteção de Bot gerido pode ser ativado para o WAF efetuar ações personalizadas nos pedidos de endereços IP maliciosos conhecidos. Os endereços IP são originados da Microsoft informações sobre ameaças do feed. [Gráfico de segurança inteligente](https://www.microsoft.com/security/operations/intelligence) alimenta sobre ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de segurança do Azure.

> [!IMPORTANT]
> Conjunto de regras de proteção de bot está atualmente em pré-visualização pública e é fornecido com um contrato de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Criar uma política de WAF básica para a porta de entrada ao seguir as instruções descritas [criar uma política de WAF para a porta da frente do Azure com o portal do Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Ativar o conjunto de regras de proteção de bot

1. Na página básica de políticas que criou na secção anterior, em **configurações**, clique em **regras**.
2. Na página de detalhes, sob o **gerir regras** secção, no menu pendente, selecione a caixa de verificação na frente a regra **BotProtection-pré-visualização-0,1**e, em seguida, selecione **guardar**acima.
    
   ![Regra de proteção de bot](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [monitorizar WAF](waf-front-door-monitor.md).
