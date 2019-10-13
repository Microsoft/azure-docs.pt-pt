---
title: Conectar dados do Barracuda ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar dados do Barracuda ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 5142204db86158937c16d19bf52fb677099598a5
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72284655"
---
# <a name="connect-your-barracuda-appliance"></a>Conectar seu dispositivo Barracuda 



O conector do WAF (firewall do aplicativo Web) Barracuda permite que você conecte facilmente seus logs do Barracuda à sentinela do Azure, exiba painéis, crie alertas personalizados e melhore a investigação. Isso lhe dá mais informações sobre a rede da sua organização e aprimora seus recursos de operação de segurança. O Azure Sentinel aproveita a integração nativa entre o **Barracuda** e o agente de log Analytics para fornecer integração direta. 


> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho no qual você está executando o Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configurar e conectar o Barracuda WAF
O Firewall do aplicativo Web Barracuda pode integrar e exportar logs diretamente para o Azure Sentinel por meio do agente de Log Analytics.
1. Vá para [fluxo de configuração do Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)e siga as instruções para configurar a conexão, usando estes parâmetros:
    - **ID do espaço de trabalho**: Copie o valor da ID do espaço de trabalho na página do conector do Azure Sentinel Barracuda.
    - **Chave primária**: Copie o valor da sua chave primária da página do conector do Azure Sentinel Barracuda.
1. Para usar o esquema relevante em Log Analytics para os eventos de Barracuda, pesquise **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Validar conectividade

Pode levar até 20 minutos até que os logs comecem a aparecer na Log Analytics. 



## <a name="next-steps"></a>Passos seguintes
Neste documento, você aprendeu a conectar os dispositivos Barracuda ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

