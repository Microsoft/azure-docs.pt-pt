---
title: Ligue os dados da Barracuda ao Azure Sentinel| Microsoft Docs
description: Saiba como utilizar o conector Barracuda Web Application Firewall (WAF) para ligar os registos Barracuda com o Azure Sentinel.
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
ms.openlocfilehash: 1131c8e79d9df2883dcf7e975af9732c21318a49
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621169"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Ligue o seu aparelho Barracuda WAF 

O conector Barracuda Web Application Firewall (WAF) permite-lhe ligar facilmente os seus registos Barracuda com o seu Azure Sentinel, para ver dashboards, criar alertas personalizados e melhorar a investigação. Isto dá-lhe mais informações sobre a rede da sua organização e melhora as suas capacidades de operação de segurança. Azure Sentinel aproveita a integração nativa entre **Barracuda** e o agente Log Analytics para proporcionar uma integração perfeita. 

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configure e ligue Barracuda WAF
Barracuda Web Application Firewall pode integrar e exportar registos diretamente para Azure Sentinel via Log Analytics agent.
1. Vá ao [fluxo de configuração barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/), e siga as instruções para configurar a ligação, utilizando estes parâmetros:
    - **ID do espaço de** trabalho : copie o valor do seu ID do seu espaço de trabalho a partir da página do conector Azure Sentinel Barracuda.
    - **Tecla principal**: copie o valor da sua chave primária na página do conector Azure Sentinel Barracuda.
1. Para utilizar o esquema relevante no Log Analytics para os eventos Barracuda, procure o **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Validar conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os aparelhos Barracuda ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.


