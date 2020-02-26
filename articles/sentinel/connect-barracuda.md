---
title: Ligue os dados da Barracuda ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados da Barracuda ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588489"
---
# <a name="connect-your-barracuda-appliance"></a>Ligue o seu aparelho Barracuda 



O conector Barracuda Web Application Firewall (WAF) permite-lhe ligar facilmente os seus registos Barracuda ao seu Azure Sentinel, ver dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. O Azure Sentinel aproveita a integração nativa entre o **Agente Barracuda** e o Agente Log Analytics para proporcionar uma integração perfeita. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configure e ligue barracuda WAF
A Firewall de Aplicação Web Barracuda pode integrar e exportar registos diretamente para o Azure Sentinel através do agente Log Analytics.
1. Vá ao fluxo de [configuração da Barracuda WAF,](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)siga as instruções para configurar a ligação, utilizando estes parâmetros:
    - **ID**do espaço de trabalho : copie o valor do seu ID do espaço de trabalho na página do conector Azure Sentinel Barracuda.
    - **Chave primária**: copie o valor da sua chave primária a partir da página do conector Azure Sentinel Barracuda.
1. Para utilizar o esquema relevante no Log Analytics para os eventos Barracuda, procure o **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos Barracuda ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.


