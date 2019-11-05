---
title: Configurar a proteção de bot para o Firewall do aplicativo Web com a porta frontal do Azure (versão prévia)
description: Saiba mais sobre o WAF (firewall do aplicativo Web).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512473"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>Configurar a proteção de bot para o Firewall do aplicativo Web (versão prévia)
Este artigo mostra como configurar a regra de proteção de bot no firewall do aplicativo Web do Azure (WAF) para a porta frontal usando CLI do Azure, Azure PowerShell ou Azure Resource Manager modelo.

Um conjunto de regras de proteção de bot gerenciado pode ser habilitado para que seu WAF execute ações personalizadas em solicitações de endereços IP mal-intencionados conhecidos. Os endereços IP são originados do feed do Microsoft Threat Intelligence. [Gráfico de segurança inteligente](https://www.microsoft.com/security/operations/intelligence) capacita a inteligência contra ameaças da Microsoft e é usada por vários serviços, incluindo a central de segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção de bot está atualmente em visualização pública e é fornecido com um contrato de nível de serviço de visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos

Crie uma política de WAF básica para a porta frontal seguindo as instruções descritas em [criar uma política de WAF para a porta frontal do Azure usando o portal do Azure](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Habilitar conjunto de regras de proteção de bot

1. Na página de política básica que você criou na seção anterior, em **configurações**, clique em **regras**.
2. Na página detalhes, na seção **gerenciar regras** , no menu suspenso, marque a caixa de seleção na frente da regra **BotProtection-preview-0,1**e selecione **salvar** acima.
    
   ![Regra de proteção de bot](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como [monitorar o WAF](waf-front-door-monitor.md).
